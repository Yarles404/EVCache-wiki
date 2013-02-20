### Pool Size Property :
This is a Integer property which can be used to specify how many connections you need from a client to the server. Default is 1.

This can be set has a DynamicProperty and the change needs a restart. To change the pool dynamically you can call refreshPool() on the EVCacheClientPoolImplMBean. 

```java
# To set the pool size to 2 for EVCACHE_SEARCH set the below property.
EVCACHE_SEARCH.EVCacheClientPool.poolSize = 2
```

###Read Timeout Property :
This is a Integer property which can be used to specify how long it takes for the client to timeout. Default is 100 milliseconds .

This can be set as a DynamicProperty and the change does not need a restart.

# To set the read timeout for EVCACHE_SEARCH to 50 milli-seconds
EVCACHE_SEARCH.EVCacheClientPool.readTimeout = 50

Ping Server Property :
This is a Boolean property which can be used to specify if the clients needs to ping the evcache servers. This ensures the connection stays open when the client is idle and the Firewall does not close.

This can be set as a DyanamicProperty and the change does not need a restart. Default it false.

# To enable ping for cineps clients to EVCACHE_SEARCH servers set the below property
EVCACHE_SEARCH.ping.servers = true

Server in Write Only Mode Property :
This is a Boolean property which can be used to specify if a particular zone needs to be in write only mode. This can be useful when we lose instances in a zone causing elevated cache misses. This is also helpful when you need to add instances to a zone or push a new ami to a zone.

zone affinity needs to be set to true for this to work.
This can be set as a DynamicProperty and it effects immediately. This does not need a restart of server.

# To put EVCACHE_SEARCH to "write only" in zone "A" set the below property
EVCACHE_SEARCH.A.EVCacheClientPool.writeOnly = true
EDIT
Latency Throttling Property : 
You can set the below properties to test your app against latency and errors.

NOTE : This effects all the clients for the specified app and take great care while doing this in PROD. Involve EVCache team when doing this. 

# To turn throttling on for EVCACHE_PLAYLIST set the below property
EVCACHE_PLAYLIST.enable.throttling = true
 
 
# To return errors for 10 percent of calls set the below property
EVCACHE_PLAYLIST.throttle.percent = 10
 
# To increase the latency by 25 milli-seconds set the below property
EVCACHE_PLAYLIST.throttle.time = 25