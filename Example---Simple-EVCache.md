Set the following properties
```java
System.setProperty("evcache.pool.provider", "com.netflix.evcache.pool.standalone.SimpleEVCacheClientPoolImpl");
/*
Where host1 and host2 is the name of the host when memcached is running and port is the memcached port 
*/
System.setProperty("EVCACHE.EVCacheClientPool.hosts", "<host1:port>,<host2:port>"); 
```