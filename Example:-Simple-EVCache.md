Below example depicts the use case for storing customer data in EVCache hosts (host1 and host2). The name of the EVCache cluster is called as **EVCACHE_CUSTOMER** and the cache name is called as **cid**. 

### Setting the Properties
Set the following properties before initializing EVCache or pass it as a command line argument **-D**
```java
System.setProperty("evcache.pool.provider", "com.netflix.evcache.pool.standalone.SimpleEVCacheClientPoolImpl");
/*
Where host1 and host2 is the name of the host when memcached is running and port is the memcached port 
*/
System.setProperty("EVCACHE_CUSTOMER.EVCacheClientPool.hosts", "<host1:port>,<host2:port>"); 
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
//Sets the key: "Hello" to value: "World" for a time of 900 seconds in EVCache.
evCache.set("Hello", "World", 900);

//Gets the value for key: "Hello" from EVCache.
String value = evCache.get("Hello");

//Deletes the value for key: "Hello" from EVCache.
evCache.delete("Hello");
```
