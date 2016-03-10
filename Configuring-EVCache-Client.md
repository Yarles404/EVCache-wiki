Below are the set of properties that needs to be set to customize EVCache client. These properties can be set as Java System property [System.setProperty(key, value)] or using [Netflix Archaius](https://github.com/Netflix/archaius). All the below properties can be changed dynamically at runtime and will take effect soon after the change.

### Read Timeout 
By default the read timeout is 20 milli-seconds. To increase it set the below property to the desired value. 
```property

<EVCache App Name>.EVCacheClientPool.readTimeout=<timeout in milliseconds>

# Default : 20
# NOTE : When client and memcached server are in the same AWS availability zone
# 20 milliseconds should be sufficient to fetch the data.

```

### Pool Size
By default one connection from a Client is established per memcached host. To increase the pool size set the below property to the desired value. 
```property
<EVCache App Name>.EVCacheClientPool.poolSize=<size of the pool.>

# Default : 1
# NOTE : For most use case 1 should be enough. YMMV.

```

### Server Group Fallback 
If the data is replicated across server groups then on a cache miss EVCache client tries to fall through to other Server Groups to get this data. By enabling this property we can achieve higher resiliency due to instances or memcached loss. 

```property
<EVCache App Name>.fallback.zone=true

# Default : true

# if you want to disable or enable for a particular cache name then set the property with cache name and it will be disabled

<EVCache App Name>.<cache name>.fallback.zone=false

```

### Server Group Fallback Bulk Operations
If the data is replicated across server groups then on a cache miss for getBulk call EVCache client tries to fall through to other Server Groups to get this data. By enabling this property we can achieve higher resiliency due to instances or memcached loss. 

```property
<EVCache App Name>.bulk.fallback.zone=true

# Default : true

# if you want to disable for partial fallback i.e. if all the keys fail then do the fallback.
# To disable fallback if a few of them fail the set the partial fallback to false. 

<EVCache App Name>.bulk.partial.fallback.zone=false

```

###Write Only Mode 
In this mode memcached servers in a server group can be put to "write only" mode. 
In this mode that server group which is in "write only" accept only writes and all the reads go to other server groups that have read enabled. This is helpful to do any maintenance on the servers by restarting or replacing them. 
This approach ensures the hit rate is not affected. 

```property
<EVCache App Name>.<Zone>.EVCacheClientPool.writeOnly=true

# Default : false

```

###Operation Failure Mode
If a EVCache operation fails due to any EVCache Node issue (Ex network connectivity, instance crash) then you can either retry or cancel the operation

```property
<EVCache App Name>.failure.mode=Cancel

# Default : Cancel
# Other options are Redistribute or Retry

```

##Max Operation Queue Size for Write Operations 
To control how many write operations can be queued before we start to reject by specifying the below property

```property
<EVCache App Name>.max.queue.length=16384

# Default : 16384

```

##Max Write Timeout 
By default the write operations are timed out after 2500 milli-seconds. To increase it set the below property to the desired value. 

```property
<EVCache App Name>.operation.timeout=2500

# Default : 2500

```

***

### InMemory Cache
You can optionally cache data in EVCache on the client's memory. This can be an effective approach when the data for the same is requested frequently in a tight loop. It can also be used to front immutable data.

```property
<EVCache App Name>.<cache name>.use.inmemory.cache=true
# Default: false

```

**InMemory Cache size**  
To control how many items can be cached in memory specify the following property

```property
<EVCache App Name>.inmemory.cache.size=100
# Default: 100

```


**InMemory Cache Duration**  
To control the max time in milliseconds that an item can cached in memory specify the following property

```property
<EVCache App Name>.inmemory.cache.duration.ms=100
# Default: 100

```

***

### Exception Propagation
By default exceptions are not thrown by EVCache client. If data is not found in the memached host null is returned. To enable throwing of exceptions set the below property. 

```property
<EVCache App Name>.throw.exception=true
```

To enable only for a cache name 
```property
<EVCache App Name>.<cache name>.throw.exception=true
```


***
