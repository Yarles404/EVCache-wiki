Below example depicts the use case for storing customer data in EVCache and using [Eureka](https://github.com/Netflix/eureka) to discover & manage EVCache instances. The name of the EVCache cluster is called as **EVCACHE_CUSTOMER** and the cache name is called as **cid**. 

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
/*
Sets the key: "Hello" to value: "World" for a time of 900 seconds in EVCache. This data will be written to both zones A and B.
*/
evCache.set("Hello", "World", 900);

/*
Gets the value for key: "Hello" from EVCache. The data will be retrieved from the zone local to the client. 
If the client is not in those zones then a zone will be chosen at random.
*/
String value = evCache.get("Hello");

//Deletes the value for key: "Hello" from EVCache.
evCache.delete("Hello");
```