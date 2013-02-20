Set the following properties
```java
System.setProperty("evcache.pool.provider", "com.netflix.evcache.pool.standalone.SimpleEVCacheClientPoolImpl");
/*
Where host1 and host2 is the name of the host when memcached is running and port is the memcached port 
*/
System.setProperty("EVCACHE.EVCacheClientPool.hosts", "<host1:port>,<host2:port>"); 
```

Initialize the EVCache Client Pool. This will establish the connection to an EVCache cluster called as **EVCACHE_CUSTOMER**
```java
EVCacheClientPoolManager.getInstance().initEVCache("EVCACHE_CUSTOMER");
```

Create an instance of EVCache using the Builder passing the app name **EVCACHE_CUSTOMER** and the cache name called as **cid**. The cache name (optional) is used to prefix the keys stored in EVCache to avoid cache collisions. 
```java
EVCache gCache = (new EVCache.Builder()).setAppName("EVCACHE_CUSTOMER").setCacheName("cid").enableZoneFallback().build();
```