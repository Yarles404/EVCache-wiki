## Initializing EVCache
The first step to use EVCache is to create connections to memcahced server. You can create the connection either at the start of your application or lazily when you first get the request. If the memcached cluster size is large or spread across multiple availability zones then it is recommended to setup the connections at start up. 

### Creating connections at Startup
This can be done in two ways.

1. By setting the EVCache apps value for property 
```property
evcache.appsToInit=<comma separated list of apps>
```
or
2. Explicitly calling 
```java
EVCacheClientPoolManager.getInstance().initEVCache(<EVCache App name>);
```