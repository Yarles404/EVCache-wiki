In most applications there is some amount of data that will be frequently used. Some of this data is transient and can be recalculated, while other data will need to be fetched from the database or a middle tier service. In the Netflix Cloud Architecture such data is cached in Memcached servers

The advantages of distributed caching are: 
* Faster response time compared to data being fetched from source/database
* Reduces the number of servers needed to handle the requests as most of the requests are served by the cache
* Reduces the load on the servers fronted by the cache
* Increases the throughput of the services fronted by the cache

A use case with a 3 node memcahced cluster with 2 clients is shown below

![Simple EVCache deployment](https://raw.github.com/Netflix/EVCache/master/images/Simple_EVCache.png?login=pprasanna&token=5c6eb8cfee260be8c45140886aa5aebe)