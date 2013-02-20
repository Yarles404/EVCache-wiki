### Zone Based Replication
* Can create clusters based on availability zone for an EVCache app. This improves reliability and availability of the data.

### Dynamically re-sizable cluster
* The cluster size can be changed by adding or removing nodes. The EVCache client automatically configures (Eureka) to the new size. 

### Connection Pools and Management
* Create a Pool of connections to each EVCache App
* Separate pools for Read and Write Operations

### Zone Fallback
* If a request to a zone fails (instance containing the data has crashed or the cluster size is increased causing a rehash of keys or a new ASG has been added) you can have clients fallback to other zones containing the data. 
This will incur an additional cost but typically is a lot smaller than going to the source of truth. 

### Set a zone to write only mode
When we lose instances in a zone we can set that zone to Write only  until the cache is substantially full. 


***

##Eureka Specific Features

### Resilient to state in Eureka
* Be passive when disconnecting from EVCache server i.e. if eureka drops a server the client should not remove it until the connection is lost. 