# Sample EVCache Deployment

This article walks you through setting up an example deployment of EVCache and running the sample app so you can see that your deployment works.

The example deployment here demonstrates two of the major features that EVCache adds on top of Memcached:

1. **Replication**, for data durability, and
1. **Scalability**, so a deployed cache can be bigger than any one machine's RAM

(A third major feature of EVCache is "data chunking," which lets you store more data per key than Memcached allows natively. This article does not explore that aspect of EVCache.)

After following this guide, you will know some basics about setting up and configuring an EVCache deployment, and you will be able to apply what you've learned to setting up and trying out your own "real" EVCache deployment.

The example deployment consists of four Memcached processes. The four processes are configured as two replicas, each of which holds a full copy of all the data. Each replica is made up of two shards. EVCache uses a consistent-hashing algorithm to determine which shard within each replica is responsible for a given key. (Some parts of EVCache refer to "replicas" as "server groups.")

The deployment described in this article is just an example. It runs as four Memcached processes on your local host, but you'd never do that for a "real" deployment. Normally, the Memcached processes (that is, the shards) would each be running on separate, dedicated hosts, and each shard would be configured to use almost all of its host's RAM. The EVCache architecture requires that all shards within a replica have the same memory size.

This article uses Bash and Ubuntu-style Linux commands. You might need different commands if you use another shell (like a csh derivative), or if you use another Linux distro (yum instead of apt-get?) or OS X.

## STEP 1: Install Memcached

You need to install Memcached on your host. This command works on Ubuntu-derived Linux:

    apt-get install memcached

Most versions of Memcached will work fine for the sample app. The Memcached version pre-installed with OS X is so old that it doesn't support some features that EVCache can use, but the sample app is so simple that it might still work with that version.

## STEP 2: Clone and build the EVCache repo

Use these commands to clone and build the EVCache repository from GitHub:

    git clone https://github.com/Netflix/EVCache.git
    cd EVCache
    ./gradlew build

The build command generates a lot of output. You can ignore all of it if you see BUILD SUCCESSFUL at the end.

## STEP 3: Start four local copies of Memcached

Start four copies of Memcached in "daemon" mode on your local host, each using a different port:

    memcached -d -p 11211
    memcached -d -p 11212
    memcached -d -p 11213
    memcached -d -p 11214

In a real deployment, these would be on separate hosts, with almost all the host RAM assigned to the cache. Usually, they would all use the default Memcached port 11211.

## STEP 4: Create a cache deployment descriptor

Applications which use EVCache need to describe all the Memcached processes that make up the cache deployment. The descriptor gives the hostname and port number for each shard and lays out how they are arranged into replicas.

The sample app sets a Java system property which tells EVCache it is using the "simple node list provider" method of describing the deployment. The sample then reads the configuration string from a certain environment variable and sets another Java system property to its value, which specifies the hostname, port number, and replica/shard arrangement of the cache. The less-simple way to describe a deployment is to use Netflix Eureka, but that's beyond the scope of this article.

Here is one way to set the required environment variable which the sample app uses. The long descriptor string is composed from separate pieces to make the format easier to read.

    s1=localhost:11211  # shard 1
    s2=localhost:11212  # shard 2
    s3=localhost:11213  # shard 3
    s4=localhost:11214  # shard 4
    r1=$s1,$s2          # replica 1 is shards 1 and 2
    r2=$s3,$s4          # replica 2 is shards 3 and 4
    export EVC_SAMPLE_DEPLOYMENT="SERVERGROUP1=$r1;SERVERGROUP2=$r2"

The EVC_SAMPLE_DEPLOYMENT variable is the key part here - that is the environment variable that the sample app reads. In the deployment descriptor string, SERVERGROUP means "replica."

## STEP 5: Run the sample app

The sample app is extremely simple. It just writes ten keys, then reads them back.

    ./gradlew runSample

The console output of the sample application includes some startup details, then a line for each set and get operation being done. You should see "Finished setting" reports for setting the ten different keys, and then "...returned data_0" through "data_9" reports for getting those same keys. This output shows that the app was able to read what it wrote.

To see how to run the sample app directly from the command line (not via gradlew), you can set the environment variable EVCACHE_SAMPLE_VERBOSE=true and then run using ./gradlew as above. When you do this, the program dumps a Java command line (including the necessary classpath setting) for running the sample without using Gradle.

## STEP 6: Use "stats" to verify what happened

After running the sample app, you might want to look at your Memcached stats to see what actually happened.

First, dump the "cmd_set" statistic from each of the two shards that make up the first replica. The output of each of the commands below shows the number of "set" commands that shard has seen since its metrics were last reset.

    echo stats | nc localhost 11211 -q 0 | grep cmd_set
    echo stats | nc localhost 11212 -q 0 | grep cmd_set

You should see that these two shards saw a total of ten "cmd_set" requests. (If you've run the sample more than once, you'll see a multiple of ten.) There won't be a clean 50/50 split in the counts - in each replica, one shard will probably be holding more keys than the other. EVCache's algorithm for assigning keys to shards isn't always perfectly balanced, and with a small number of keys the imbalance can really show.

Next, dump the "set" statistics from the shards that make up the second replica:

    echo stats | nc localhost 11213 -q 0 | grep cmd_set
    echo stats | nc localhost 11214 -q 0 | grep cmd_set

The two shards making up the second replica should show the same total number of cmd_set operations as the first replica, because every SET was sent to both replicas. The distribution might be different from the first shard, though. The assignment of keys to shards is different for each replica.

Now look at the total of the "get" statistics across both replicas:

    echo stats | nc localhost 11211 -q 0 | grep cmd_get
    echo stats | nc localhost 11212 -q 0 | grep cmd_get
    echo stats | nc localhost 11213 -q 0 | grep cmd_get
    echo stats | nc localhost 11214 -q 0 | grep cmd_get

Among the four shards, you should see a total of ten cmd_get operations. (A multiple of ten if you've run the sample more than once.) For each "get," EVCache first picks a target replica at random; then it computes which shard in that replica should hold the key you're asking for. Finally, the library code performs the "get" on that shard.

Because the sample app performs ten "get" operations, the total number of "gets" seen by the whole deployment will go up by ten every time you run the sample. But the distribution of those "gets" among the replicas could change, because it's partly random.

When you use the simple configuration style of the sample application, you can't tell EVCache to favor one replica over another. In a "real" deployment using Netflix Eureka to manage "discovery" for replicas and shards, EVCache will prefer the "closest" replica(s) -- those in the same AWS Availability Zone, for example.

You can reset the statistics before re-running the sample, to make it easier to see the counts for each run, like this:

    echo stats reset | nc localhost 11211 -q 0
    echo stats reset | nc localhost 11212 -q 0
    echo stats reset | nc localhost 11213 -q 0
    echo stats reset | nc localhost 11214 -q 0

## Finished - no more steps

That's it. You just learned how to set up a sample EVCache deployment consisting of two replicas, each with two shards. You learned how to describe that cache configuration to the sample application, how to run the sample app, and how to see some Memcached statistics to confirm the behavior.

With just a few changes, you can now set up a more "real" deployment - one where the shards are running on separate hosts using (almost) all the RAM on each.

When you finish experimenting, you can find and terminate your four sample shards using "ps" and "kill." You can even use "killall memcached" if you're sure there are no other Memcached processes running on your host, besides this sample deployment.

To continue learning about EVCache, take a look at the sample app to see what it's actually doing each step of the way. And read through the rest of the Netflix EVCache wiki on GitHub.
