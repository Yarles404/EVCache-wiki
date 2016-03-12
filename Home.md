![Logo](https://raw.github.com/Netflix/EVCache/master/images/evcache_logo_text.jpg)

<iframe src="//www.slideshare.net/slideshow/embed_code/key/AqBz3zvrrVktEA" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> 

In most applications there is some amount of data that will be frequently used. Some of this data is transient and can be recalculated, while other data will need to be fetched from the database or a middle tier service. In the Netflix Cloud Architecture such data is cached in Memcached servers

The advantages of distributed caching are: 
* Faster response time compared to data being fetched from source/database
* Reduces the number of servers needed to handle the requests as most of the requests are served by the cache
* Reduces the load on the servers fronted by the cache
* Increases the throughput of the services fronted by the cache

Simple EVCache Deployment
=========================
A 3 node memcached cluster with 2 clients is shown below

![Simple EVCache deployment](https://raw.github.com/Netflix/EVCache/master/images/Simple_EVCache.png)

The data is sharded across the cluster based on [Ketama consistent hashing algorithm](http://www.audioscrobbler.net/development/ketama/). In this mode all the memcached nodes can be in the same availability zone or spread out across multiple availability zones. 

Multi-Zone EVCache Deployment with zone affinity 
===
A 3 node memcached cluster in 2 availability zones with a client in each zone is shown below

![Multi-Zone EVCache Deployment](https://raw.github.com/Netflix/EVCache/master/images/Multizone_EVCache.png)

The data is sharded across the cluster within the same zone based on [Ketama consistent hashing algorithm](http://www.audioscrobbler.net/development/ketama/). In this mode all the reads by a client are sent to the same zone whereas the writes are done on both the zones. This ensures that data is replicated across both the zones thus increasing its availability. Since the data is always read from the local zone this improves the latency at the sametime improving the data reliability. This approach is best suited if you want to achieve better performance with higher reliability. 

If zone fallback is enabled and some data is lost due to instance failure of eviction, then the data can be fetched from the other zone. This however causes an increase in latency but higher reliability. In most cases fetching data from other zone (fallback zone) is much faster than getting the data from source.


EVCache Deployment based on Eureka 
===
A 3 node memcached cluster in 3 availability zones with a client in each zone is shown below

![EVCache Deployment using Eureka](https://raw.github.com/Netflix/EVCache/master/images/Eureka.png)

When using EVCache based on Eureka, the EVCache client gets the list of servers from Eureka and creates cluster based on the zone. If the EVCache Server instances are added or removed the client re-configures.  
Similar to Mulit-Zone EVCache the data is sharded across the cluster within the same zone based on [Ketama consistent hashing algorithm](http://www.audioscrobbler.net/development/ketama/). All the reads by a client are sent to the same zone whereas the writes are done on all the zones. This ensures that data is replicated across zones thus increasing its availability. Since the data is always read from the local zone this improves the latency at the same time improving the data reliability. 

If zone fallback is enabled and some data is lost due to instance failure of eviction, then the data can be fetched from the other zone. This however causes an increase in latency but higher reliability. In most cases fetching data from other zone (fallback zone) is much faster than getting the data from source.