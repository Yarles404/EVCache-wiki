Below are the set of properties that needs to be set to customize EVCache client. These properties can be set as Java System property [System.setProperty(key, value)] or using [Netflix Archaius](https://github.com/Netflix/archaius). All the below properties can be changed dynamically at runtime and will take effect soon after the change.

####Read Timeout:

The max time in milli-seconds the client waits to retrieve the value from EVCache.
```property
<App>.EVCacheClientPool.readTimeout=20

#Default: 20. 
#Valid Values : 1 and above
```

####Bulk Read Timeout:

The max time in milli-seconds the client waits to retrieve the values from EVCache for the given set of keys.
```property
<App>.EVCacheClientPool.bulkReadTimeout=20

#Default: 20 (read timeout value). 
#Valid Values : 1 and above
```

####Max Read Queue Length:

The max number of items in queue before we start rejecting read operations (fast fail).
Note : A reject does not mean we fail the entire operation. If there are multiple server group for this <App> then we try the operation on a server group whose queue is not full and can perform the operation. 

```property
<App>.max.read.queue.length=5

#Default: 5. 
#Valid Values : 1 and above
```


####Max Number of Retries:

The Max number of retries across the server groups. For example if you have 3 copies in a region and this property is set to 1 on a cache miss or exception on the first operation we randomly pick one of the remaining server group and try to fetch the data from them. 

```property
<App>.max.retry.count=1

#Default: 1. 
#Valid Values : 1 and above
```

####Retries across all copies:

Retries all the server groups for the <App> to retrieve the data. For example if you have 3 copies in a region and this property is set then on a cache miss or exception on the first operation we try the remaining server group one by one until we are able fetch the data. Use this option if the <App> cannot afford to have a cache miss. In such a setup you will have more than 3 copies per region. 

```property
<App>.retry.all.copies=false

#Default: false. 
#Valid Values : true or false
```

####Pool Size:

Number of connections from a Client to a memcached host.
```property
<App>.EVCacheClientPool.poolSize=1

#Default: 1. 
#Valid Values : 1 and above
```

####Zone Fallback:

On a cache miss should the EVCache client try to fall through to other Server Groups to get the requested data.
```property
<App>.fallback.zone=true

#Default: true. 
#Valid Values : true or false
```

####Bulk Operation Zone Fallback:

On a cache miss for Bulk Operations should the EVCache client try to fall through to other Server Groups to get the requested data.
```property
<App>.bulk.fallback.zone=true

#Default: true. 
#Valid Values : true or false
```

####Bulk Operation Partial Zone Fallback:

If few of the keys are missing for Bulk Operations should the EVCache client try to fall through to other Server Groups to get the missing data.
```property
<App>.bulk.partial.fallback.zone=true

#Default: true. 
#Valid Values : true or false
```

####Write Only mode:

In this mode that server group which is in "write only" accept only writes and all the reads go to other server groups that have read enabled. This is helpful to do any maintenance on the servers by restarting or replacing them. This approach ensures the hit rate is not affected.
```property
<App>.<Zone>.EVCacheClientPool.writeOnly=false 
or
<App>.<ServerGroup>.EVCacheClientPool.writeOnly=false

#Default: false. 
#Valid Values : true or false
```

####Dynamic Write Only mode:

When this is enabled we mark a server group as "Write Only" if we were to lose 50% of the instances in that server group. This ensures that we don't even try a server group and fallback to other server group for request. This is especially helpful if a zone is having issues and ensures that client reads are not affected. 
```property
EVCacheClientPool.enable.dynamic.writeonly=true 

#Default: true. 
#Valid Values : true or false
```

####Failure Mode:

If a EVCache operation fails due to any EVCache Node issue (Ex network connectivity, instance crash) then you can either retry or cancel the operation.
```property
<App>.failure.mode=Canel

#Default: Cancel. 
#Valid Values : Cancel, Redistribute or Retry
```

####Max Operation Queue Size for Write Operations:

To control how many write operations can be queued before we start to reject by specifying the below property.
```property
<App>.max.queue.length=16384

#Default: 16348. 
#Valid Values : 1 and above
```

####Max Write Timeout:

By default the write operations are timed out after 2500 milli-seconds. To increase it set the below property to the desired value.
```property
<App>.operation.timeout=2500

#Default: 2500. 
#Valid Values : 1 and above
```

####Max Queue Block Time:

The max time a thread is blocked before the operation is accepted and added to the queue. 

```property
<App>.operation.QueueMaxBlockTime=10

#Default: 10. 
#Valid Values : 1 and above
```


***
###Chunking:

You can optionally chunk data into multiple chunks. This is helpful if you want to store large items by splitting them into smaller chunks. There is a slight overhead while reading or writing the data but chunking ensures that when the sizes of the items changes there are no evictions due to items migrating across memcached slabs.

#####Enable Chunking:

To Enable chunking of data 
```property
<App>.chunk.data=false

#Default: false. 
#Valid Values : true or false
```

####Chunk Size:

To control the size of each chunk specify the following property.
```property
<App>.chunk.size=1180

#Default: 1180. 
#Valid Values : 120 and above
```

***
###InMemory Cache
You can optionally cache data in EVCache on the client's memory. This can be an effective approach when the data for the same is requested frequently in a tight loop. It can also be used to front immutable data.

#####Enable InMemory Cache:
To Enable InMemory Cache

```property
<App>.use.inmemory.cache=false

#Default: false. 
#Valid Values : true or false
```

#####InMemory Max Cache Items:

To control how many items can be cached in memory specify the following property.
```property
<App>.inmemory.cache.size=100 

#Default: 100. 
#Valid Values : 1 and above
```

#####InMemory Cache Duration:

To control the max time in milliseconds that an item can cached in memory specify the following property.
```property
<App>.inmemory.cache.duration.ms=20

#Default: 20. 
#Valid Values : 1 and above
```

***

####Exception Propagation:

By default exceptions are not thrown by EVCache client. If data is not found in the memached host null is returned. To enable throwing of exceptions set the below property.
```property
<App>.throw.exception=false
or
<App>.<cache_prefix>.throw.exception=false

#Default: false. 
#Valid Values : true or false
```

####Ping Servers:

You can enable to ping EVCache servers (Default : 30 seconds). This ensures the connections remain active and are ready to perform operations. This is helpful when the firewalls in between server the connection if they are idle for a long periods of time. We have noticed issues trying to reestablish connection in such an event if the traffic is suddenly turned on such idle connections. 
```property
<App>.ping.servers=false

#Default: false. 
#Valid Values : true or false
```


***
###Logging

You can enable logging of evcache operations to datastores like Hive,kafka or stream them to spark. This can be used to get insights into the access behavior of the cache. For example by logging you can find out 
what should be the optimal TTL for your data? 
Are there any hot keys? 
Are there any inefficiencies like you add data to cache which is never read?
What keys are missing from evcache?

####Logging Percent:
The percent of keys to be logged. We get the hashcode of the key and divide by 1000. If the result is less than or equal to this value then the operation is logged. 

```property
<App>.log.operation=0

#Default: 0. 
#Valid Values : 0 to 1000
```

####Log Operations:
The calls to be logged to datastores like Hive,kafka or stream them to spark. 

```property
<App>.log.operation.calls=SET,DELETE,GMISS,TMISS,BMISS_ALL,TOUCH,REPLACE

#Default: SET,DELETE,GMISS,TMISS,BMISS_ALL,TOUCH,REPLACE
#Valid Values : SET,DELETE,GMISS,TMISS,BMISS_ALL,TOUCH,REPLACE,GET,INCR,DECR,APPEND,PREPEND,REPLACE
```

###Logging using slf4j
EVCache uses slf4j for logging and you can enabled logging to log messages for a provider of your choice. 
For log4j you can set

```property
log4j.logger.com.netflix.evcache.EVCacheImpl=<level>,<appender>
#DEBUG : This will log all the operations in a detailed way
#INFO : Cache misses with keys

log4j.logger.com.netflix.evcache.pool.EVCacheClient=<level>,<appender>
#DEBUG : This will log all the operations in a detailed way

Similarly you can add logging to most of the EVCache classes. Please check the source code for details.
```

***

###Ports

#####evcache port:
The port on which all evcache communication happens. This is the same port memcached is listening on. 
```property
evcache.port=11211

#Default: 11211 
```

#####rend port:
The port on which all rend communication happens. This is the rend server is listening on. 
```property
rend.port=11211

#Default: 11211 
```

#####UDS memcached Proxy Port
The port on which all the proxying to memcached happens. This will bypass rend and directly talk to memcached.  
```property
udsproxy.memcached.port=22211

#Default: 22211 
```

#####UDS memento Proxy Port
The port on which all the proxying to memento happens. This will bypass rend and directly talk to memento.  
```property
udsproxy.memento.port=22212

#Default: 22212 
```
