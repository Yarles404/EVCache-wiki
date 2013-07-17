Below are the set of properties that needs to be set to customize EVCache client. These properties can be use as Java System property ( System.setProperty(key, value)) or using [Netflix Archaius](https://github.com/Netflix/archaius). All the properties can be changed dynamically at runtime and will take effect after the change.

# Read Timeout 
By Default the read timeout is 100 milli seconds. To increase the value set the below property to desired value. 
NOTE : When client and memcached server are in the same AWS availability zone 100 milliseconds is sufficient.
< <EVCache App Name>.EVCacheClientPool.readTimeout=<timeout in milliseconds. Default is 100 milliseconds. >

Pool Size
By Default we establish one connection from a Client to the memcached sever. To increase the pool size set the below property to the desired value. 
NOTE : For most usecases 1 should be enough. YMMV.
<EVCache App Name>.EVCacheClientPool.poolSize=<size of the pool. Default is 1>



Exception Throwing
By default exceptions are not thrown by EVCache client. If data is not found in the memached server null is returned. 
To enable throwing of exceptions set the below property. 

For an EVCache client that has app name and cache name 
<EVCache App Name>.<cache name>.throw.exception=true

For an EVCache client that only has an app name 
<EVCache App Name>.throw.exception=true


EVCache clients using Multi-Zone or Eureka 

Zone Fallback
If the data is replicated then on a cache miss EVCache client tries to fall through to other zones to get this data.
By enabling this property there we can achieve higher resiliency due to instances or memcached loss. 

For an EVCache client that has app name and cache name 
<EVCache App Name>.<cache name>.fallback.zone=false

For an EVCache client that only has an app name 
<EVCache App Name>.fallback.zone=false


Zone Affininty 
By default we read the data from the zone local to the EVCache client if the client is found in the same availability zone.
If this needs to be disabled set the below property to false

<EVCache App Name>.EVCacheClientPool.zoneAffinity=false


Write Only Mode 

In this mode memcached servers in zone can be put to write only mode. 
All the reads to the zone that is in write only mode will be sent to other zones. This is helpful if we need to do any maintenance on the servers by restarting or replacing them. 
This way the hit rate is not affected at the cost of slight increased latency due. Default is false and setting the below property makes the servers in a zone to be in write only mode. 

<EVCache App Name>.<Zone>.EVCacheClientPool.writeOnly=true


Multi Zone EVCache Cluster

Zones
To specify the list of zones set the below property
<EVCache App Name>.EVCacheClientPool.zones=<comma separated list of zones>

Memcached hosts in a zone
To specify a list of hosts that are part of a zone set the below property
<EVCache App Name>.<Zone>.EVCacheClientPool.hosts=<comma serparted list of host:port>


Simple EVCache Cluster

Memcached hosts in the cluster
To specify a list of hosts that are part of the cluster set the below property
<EVCache App Name>.EVCacheClientPool.hosts=<comma serparted list of host:port>