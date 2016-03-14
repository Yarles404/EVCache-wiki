Below are the set of properties that needs to be set to customize EVCache client. These properties can be set as Java System property [System.setProperty(key, value)] or using [Netflix Archaius](https://github.com/Netflix/archaius). All the below properties can be changed dynamically at runtime and will take effect soon after the change.

####Read Timeout:

The max time in milli-seconds the client waits to retrieve the value from EVCache.
```property
<App>.EVCacheClientPool.readTimeout=20

#Default: 20. 
#Valid Values : 1 and above
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

####Enable Chunking:

You can optionally chunk data into multiple chunks. This is helpful if you want to store large items by splitting them into smaller chunks. There is a slight overhead while reading or writing the data but chunking ensures that when the sizes of the items changes there are no evictions due to items migrating across memcached slabs.
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

####Enable InMemory Cache:

You can optionally cache data in EVCache on the client's memory. This can be an effective approach when the data for the same is requested frequently in a tight loop. It can also be used to front immutable data.
```property
<App>.use.inmemory.cache=false

#Default: false. 
#Valid Values : true or false
```

####InMemory Max Cache Items:

To control how many items can be cached in memory specify the following property.
```property
<App>.inmemory.cache.size=100 

#Default: 100. 
#Valid Values : 1 and above
```

####InMemory Cache Duration:

To control the max time in milliseconds that an item can cached in memory specify the following property.
```property
<App>.inmemory.cache.duration.ms=20

#Default: 20. 
#Valid Values : 1 and above
```

####Exception Propagation:

By default exceptions are not thrown by EVCache client. If data is not found in the memached host null is returned. To enable throwing of exceptions set the below property.
```property
<App>.throw.exception=false
or
<App>.<cache_prefix>.throw.exception=false

#Default: false. 
#Valid Values : true or false
```
