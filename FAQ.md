*** What is memached?
[memcached](http://code.google.com/p/memcached/) is a high-performance in-memory data caching application.

*** What is spymemached?
[spymemcached] (http://code.google.com/p/spymemcached/) is a java client to access memcached.

*** What is an EVCache App?
EVCache App is a logical grouping of one or more memcached instances (servers). Each instance can be a
* EVCache Server (to be open sourced soon) running memcached and a Java sidecar app
* EC2 instance running memcached
* ElastiCache instance 
* instance that can talk memcahce protocol (eg. Couchbase, MemcacheDB)

Each app is associated with a name.  Though it is not recommend, a memcached instance can be shared across multiple EVCache Apps. 

*** What is an EVCache Client?
EVCache client manages all the operations between an Java application and an EVCache App. 

*** What is an EVCache Server?
EVCache Server is an EC2 instance running an instances of memcached and a Java Sidecar application. The sidecar is responsible for interacting with Eureka, monitoring the memcached process and collecting and reporting performance data to the Servo. This will be Open Sources soon. 