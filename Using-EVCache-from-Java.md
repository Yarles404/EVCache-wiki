## Initializing EVCache
The first step to use EVCache is to create the client instance, which will optionally eagerly connect to the memcached servers. You can choose to create the connections either at the start of your application or lazily when you first get the request. If the memcached cluster size is large or spread across multiple availability zones then it is recommended to setup the connections at start up. 

### Creating EVCache Instance
To interact with EVCache Server you need to create an instance of the EVCache class.
You can do that by:

#### Injecting the EVCache.Builder:

```java
import com.netflix.evcache.*;

@Inject
public MyClass(EVCache.Builder evCacheBuilder) {
        EVCache evCache = evCacheBuilder.setAppName(<EVCache App Name>)
                                        .setCachePrefix("prefix") // All keys will have this prefix (optional) 
                                        .enableRetries() // enable cross zone retries
                                        .setDefaultTTL(100) // Set the Default TTL to be 100 seconds
                                        .build();
}
```

#### Creating a new EVCache.Builder 

```java
import com.netflix.evcache.*;

public MyClass() {
        EVCache myCache =  new EVCache.Builder().setAppName(<EVCache App Name>)
                                        .setCachePrefix(<prefix>) // All keys will have this prefix or null 
                                        .enableRetries() // enable cross zone retries
                                        .setDefaultTTL(100) // Set the Default TTL to be 100 seconds
                                        .build();
}
```


### Adding Data to EVCache
To set data you need to pass a key and a value. You can also use the overloaded methods to pass a custom transcoder and TTL (time to live).

#### Notes:
* The key cannot contain spaces or new lines
* Values are limited to 1 MB maximum (minus serialization overhead)

#### Use the EVCacheLatch
If you want to wait for set completion, the latch is a simple API to allow checking for different levels of completion.

```java
EVCacheLatch latch = client.set("key", "value", Policy.ALL_MINUS_1)
latch.await(20, TimeUnit.MILLISECONDS) // wait for 20 ms or until the policy is fulfilled, whichever is shorter
```

The latch object will act as a single future to wait on if waiting is required. The policy has several values:

```java
public static enum Policy {
    ONE, QUORUM, ALL_MINUS_1, ALL
}
```

* `ONE` will release the latch as soon as one copy is successfully written
* `QUORUM` will release the latch when a quorum of copies are successful
* `ALL_MINUS_1` will release the latch when all copies minus 1 are successful. E.g. if you run 3 copies, only 2 will be needed.
* `ALL` will release only when all copies have confirmed success. Normally in a dynamic system this is discouraged because a single bad node can cause high latency.

#### Using Futures directly
If the app does not need to wait for the completion of the request or wants to deal with individual copies' requests directly, the futures API can be used.

```java
Future<Boolean>[] status = myCache.set("key","value");
```
You can inspect the status futures to see if the sets to individual copies were successful. If you do call `get()` on the future, your code will block until that request has returned.


### Retrieving data from EVCache
To synchronously retrieve the value associated with a key:

```java
String v = myCache.<String>get("key");
```

You can also use the async API if you require a non-blocking call:

```java
Future<String> f = myCache.getAsynchronous("key");
 
try {
    String v = f.get(100, TimeUnit.MILLISECONDS); // Don't wait more than 100 ms
} catch (EVCacheException e) {
    f.cancel(false); //optional
}
```

### Delete data from EVCache

The preferred way to delete from EVCache, when you want to wait for completion, is to use the `EVCacheLatch`:

```java
EVCacheLatch latch = myCache.delete("key", Policy.ALL_MINUS_1);
latch.await(20, TimeUnit.MILLISECONDS); // optional
```

Otherwise, use the futures API:

```property
Future<Boolean>[] status = myCache.delete("key");
```

Similar to the set operation you can inspect the status flag of the futures to determine if the operation was successful or not. 

### Writing Test Cases:

```java
// Init NFLifecycleUnitTester 
final Properties props = new Properties();
final NFLifecycleUnitTester unitTester = new NFLifecycleUnitTester(props);
 
// Get instance of EVCache.Builder
final EVCache.Builder evCacheBuilder = unitTester.getInjector().getInstance(EVCache.Builder.class);
 
// Get instance of EVCache. Now you can perform all operations on it
final EVCache evCache = evCacheBuilder.setAppName("EVCACHE").setCacheName("cid").enableZoneFallback().build();

// perform a write
final String key = "key_0";
final String value = "val_0";
Future<Boolean>[] status = evCache.set(key,value);
for(Future<Boolean> s : status) {
      System.out.println("SET : status " + s.get());
}

// perform a read
String val = evCache.get(key);
System.out.println("GET : key : " + key + "; value : " + val);

```