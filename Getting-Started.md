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


### Creating EVCache Instance
To interact with EVCache Server you need to create an instance or EVCache. 
You can do that by 

By Injecting the EVCache.Builder

```property
import com.netflix.evcache;
@Inject
public MyClass(EVCache.Builder evCacheBuilder, <Other Librariess your client needs>) {
        EVCache evCache = evCacheBuilder.setAppName(<EVCache App Name>)
                                        .setCachePrefix(<prefix>) // All keys will have this prefix or null 
                                        .enableRetries() // enable cross zone retries
                                        .setDefaultTTL(100) // Set the Default TTL to be 100 seconds
                                        .build();
}
```

Creating a new EVCache.Builder 

```property
import com.netflix.evcache;
public MyClass() {
        EVCache myCache =  new EVCache.Builder().setAppName(<EVCache App Name>)
                                        .setCachePrefix(<prefix>) // All keys will have this prefix or null 
                                        .enableRetries() // enable cross zone retries
                                        .setDefaultTTL(100) // Set the Default TTL to be 100 seconds
                                        .build();
}
```


###Adding Data to EVCache:
To set data you need to pass the key and value. You can also use the overloaded method and pass your custom transcoder and TTL(time to live) for you the data you are setting.
Note: key cannot contain space or new line character
Values are limited to 1MB maximum (minus serialization overhead)
```property
Future<Boolean>[] status = myCache.set("key","value");
```
You can inspect the status object to ensure if that the data you have set is successful or not. 
Note: Doing so will block the operation until we can determine if the operation was successful or not. 


###Retrieving data from EVCache
To synchronously retrieve the value associated with a key:
```property
String v = myCache.<String>get("key");
```

###Delete data from EVCache:
To delete the data from EVCache
```property
Future<Boolean>[] status = myCache.delete("key");
```
Similar to set operation you can inspect the status flag to determine if the operation was successful or not. 

###Writing Test Cases:

```property

//Init NFLifecycleUnitTester 
final Properties props = new Properties();
final NFLifecycleUnitTester unitTester = new NFLifecycleUnitTester(props);
 
//Get instance of EVCache.Builder
final EVCache.Builder evCacheBuilder = unitTester.getInjector().getInstance(EVCache.Builder.class);
 
//Get instance of EVCache. Now you can perform all operations on it
final EVCache evCache = evCacheBuilder.setAppName("EVCACHE").setCacheName("cid").enableZoneFallback().build();

//perform a write
final String key = "key_0";
final String value = "val_0";
Future<Boolean>[] status = evCache.set(key,value);
for(Future<Boolean> s : status) {
      System.out.println("SET : status " + s.get());
}

//perform a read
String val = evCache.get(key);
System.out.println("GET : key : " + key + "; value : " + val);

```