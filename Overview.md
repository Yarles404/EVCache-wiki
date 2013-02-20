In most applications there is some amount of data that will be frequently used. Some of this data is transient and can be recalculated, while other data will need to be fetched from the database or a middle tier service. In the Netflix Cloud Architecture such data is cached in Memcached servers

The advantages of distributed caching are: 
* Faster response time compared to data being fetched from source/database
* Reduces the number of servers needed to handle the requests as most of the requests are served by the cache
* Reduces the load on the servers fronted by the cache
* Increases the throughput of the services fronted by the cache

Simple EVCache Deployment
=========================
A 3 node memcahced cluster with 2 clients is shown below

![Simple EVCache deployment](https://raw.github.com/Netflix/EVCache/master/images/Simple_EVCache.png?login=smadappa&token=e7a5d0559028b344af320cbb8b25711a)

All the reads are sharded across the cluster based on [Ketama consistent hashing algorithm](http://www.audioscrobbler.net/development/ketama/). In this mode all the memcached nodes can be in the same availability zone or spread out across multiple availability zones. 

Multi-Zone EVCache Deployment with zone affinity for reads
=========================
A use case with a 3 node memcahced cluster with 2 clients is shown below

![Multi-Zone EVCache Deployment](https://raw.github.com/Netflix/EVCache/master/images/Multizone_EVCache.png?login=smadappa&token=00423173e4fb3bd9a2ca3de7cc27eb9e)