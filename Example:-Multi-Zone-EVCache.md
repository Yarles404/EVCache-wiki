Below example depicts the use case for storing customer data in availability zones **A** and **B**. Each zone has 2 hosts. The name of the EVCache cluster is called as **EVCACHE_CUSTOMER** and the cache name is called as **cid**. 

### Setting the Properties
Set the following properties before initializing EVCache or pass it as a command line argument **-D**
```java
System.setProperty("evcache.pool.provider", "com.netflix.evcache.pool.standalone.ZoneClusteredEVCacheClientPoolImpl");

//Specify the zones where the data will be stored
System.setProperty("EVCACHE_CUSTOMER.EVCacheClientPool.zones", "A,B");

//
System.setProperty("EVCACHE_CUSTOMER.A.EVCacheClientPool.hosts","host1:port,host2:port");
System.setProperty("EVCACHE_CUSTOMER.B.EVCacheClientPool.hosts","host3:port,host4:port");
```

### Initializing the pool (optional)
Optionally, you can initialize the EVCache Client Pool when your application/server is starting. This will establish the connection to an EVCache cluster called as **EVCACHE_CUSTOMER**
```java
EVCacheClientPoolManager.getInstance().initEVCache("EVCACHE_CUSTOMER");
```

### Creating EVCache instance
Create an instance of EVCache using the Builder passing the app name **EVCACHE_CUSTOMER** and the cache name called as **cid**. The cache name (optional) is used to prefix the keys stored in EVCache to avoid cache collisions. 
```java
EVCache evCache = (new EVCache.Builder()).setAppName("EVCACHE_CUSTOMER").setCacheName("cid").enableZoneFallback().build();
```

### Performing operations on EVCache
```java
/*Sets the key: "Hello" to value: "World" for a time of 900 seconds in EVCache. This data will be written to both zones A and B.
*/
evCache.set("Hello", "World", 900);

/*Gets the value for key: "Hello" from EVCache. The data will be retrieved from the zone local to the client. If the client is not in those zones then a zone will be chosen at random.*/
String value = evCache.get("Hello");

//Deletes the value for key: "Hello" from EVCache.
evCache.delete("Hello");
```