 
 1. **Obtain a `CachingProvider`**:
   ```java
   CachingProvider provider = Caching.getCachingProvider(JobDao.class.getClassLoader());
   ```
   - Retrieves the caching provider (e.g., Hazelcast, Ehcache, or another JSR-107-compliant implementation).
   - The `JobDao.class.getClassLoader()` ensures the correct class loader is used.

2. **Get or Create a CacheManager**:
   ```java
   CacheManager cacheManager = provider.getCacheManager();
   ```
   - Obtains the `CacheManager` instance, which is responsible for managing caches.

3. **Retrieve or Create a Cache**:
   ```java
   Cache<CacheKey, List> tempCache = cacheManager.getCache(JobDao.class.getName(), CacheKey.class, List.class);
   ```
   - Checks if a cache with the name `JobDao.class.getName()` exists.
   - If the cache does not exist, it creates one with a custom configuration:
     ```java
     MutableConfiguration<CacheKey, List> configuration = new MutableConfiguration<CacheKey, List>()
             .setTypes(CacheKey.class, List.class)
             .setStoreByValue(false)
             .setExpiryPolicyFactory(CreatedExpiryPolicy.factoryOf(new Duration(TimeUnit.MINUTES, 2)));
     tempCache = cacheManager.createCache(JobDao.class.getName(), configuration);
     ```
     - **Key Type**: `CacheKey`
     - **Value Type**: `List`
     - **Store By Value**: `false` (stores references instead of serialized copies).
     - **Expiration Policy**: Entries expire 2 minutes after creation.

---

### **How to Use This Cache**

Here are some examples of how you can interact with the cache (`tempCache`).

#### **1. Adding Data to the Cache**
```java
CacheKey key = new CacheKey("exampleKey");
List<String> value = Arrays.asList("Item1", "Item2", "Item3");

// Put the key-value pair into the cache
tempCache.put(key, value);
System.out.println("Data added to cache!");
```

---

#### **2. Retrieving Data from the Cache**
```java
CacheKey key = new CacheKey("exampleKey");

// Retrieve the value associated with the key
List<String> cachedValue = tempCache.get(key);

if (cachedValue != null) {
    System.out.println("Retrieved from cache: " + cachedValue);
} else {
    System.out.println("Key not found in cache.");
}
```

---

#### **3. Updating Data in the Cache**
```java
CacheKey key = new CacheKey("exampleKey");
List<String> updatedValue = Arrays.asList("UpdatedItem1", "UpdatedItem2");

// Update the value associated with the key
tempCache.put(key, updatedValue);
System.out.println("Cache updated!");
```

---

#### **4. Removing Data from the Cache**
```java
CacheKey key = new CacheKey("exampleKey");

// Remove the entry from the cache
tempCache.remove(key);
System.out.println("Key removed from cache.");
```

---

#### **5. Clearing the Cache**
```java
// Remove all entries from the cache
tempCache.clear();
System.out.println("Cache cleared!");
```

---

#### **6. Checking if a Key Exists in the Cache**
```java
CacheKey key = new CacheKey("exampleKey");

// Check if the key exists in the cache
if (tempCache.containsKey(key)) {
    System.out.println("Key exists in cache.");
} else {
    System.out.println("Key does not exist in cache.");
}
```

---

### **Advanced Examples**

#### **1. Using Multiple Caches**
You can create multiple caches for different purposes by repeating the process with different cache names.

```java
Cache<CacheKey, String> userCache = cacheManager.getCache("userCache", CacheKey.class, String.class);
if (userCache == null) {
    MutableConfiguration<CacheKey, String> userConfig = new MutableConfiguration<CacheKey, String>()
            .setTypes(CacheKey.class, String.class)
            .setStoreByValue(true)
            .setExpiryPolicyFactory(AccessedExpiryPolicy.factoryOf(new Duration(TimeUnit.HOURS, 1)));
    userCache = cacheManager.createCache("userCache", userConfig);
}

// Add data to the user cache
userCache.put(new CacheKey("user1"), "John Doe");
System.out.println("User added to cache!");
```

---

#### **2. Custom Expiry Policies**
The example uses a `CreatedExpiryPolicy` to expire entries 2 minutes after creation. You can use other expiry policies:

- **AccessedExpiryPolicy**: Expire entries after a specified time since the last access.
- **ModifiedExpiryPolicy**: Expire entries after a specified time since the last modification.
- **EternalPolicy**: Entries never expire.

**Example**:
```java
MutableConfiguration<CacheKey, List> configuration = new MutableConfiguration<CacheKey, List>()
        .setTypes(CacheKey.class, List.class)
        .setExpiryPolicyFactory(AccessedExpiryPolicy.factoryOf(new Duration(TimeUnit.SECONDS, 30))); // Expires 30 seconds after last access
tempCache = cacheManager.createCache("accessCache", configuration);
```

---

#### **3. Preloading the Cache**
You can preload the cache with data at application startup.

```java
public void preloadCache(Cache<CacheKey, List> cache) {
    cache.put(new CacheKey("preload1"), Arrays.asList("Item1", "Item2"));
    cache.put(new CacheKey("preload2"), Arrays.asList("Item3", "Item4"));
    System.out.println("Cache preloaded with data!");
}
```

---

#### **4. Iterating Over Cache Entries**
JSR-107 caches allow you to iterate over entries if the underlying implementation supports it.

```java
for (Cache.Entry<CacheKey, List> entry : tempCache) {
    System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
}
```

---

### **Real-World Use Cases**

1. **Caching Expensive Database Queries**:
   - Cache query results for a fixed period to reduce database load.
   ```java
   CacheKey key = new CacheKey("queryResult");
   List<String> result = tempCache.get(key);
   if (result == null) {
       result = databaseService.executeQuery("SELECT * FROM table");
       tempCache.put(key, result);
   }
   ```

2. **Session Management**:
   - Store user session data in the cache with an expiration policy.
   ```java
   CacheKey sessionKey = new CacheKey("session123");
   tempCache.put(sessionKey, sessionData);
   ```

3. **Rate Limiting**:
   - Track API usage per user and reset the count after a specific duration.
   ```java
   CacheKey apiKey = new CacheKey("user123_api");
   Integer currentCount = tempCache.get(apiKey);
   if (currentCount == null) {
       tempCache.put(apiKey, 1);
   } else {
       tempCache.put(apiKey, currentCount + 1);
   }
   ```

4. **Preloading Static Data**:
   - Cache frequently used static data (e.g., dropdown options, product categories).
   ```java
   tempCache.put(new CacheKey("categories"), categoryService.getAllCategories());
   ```

---

### **Best Practices**

1. **Choose the Right Expiry Policy**:
   - Use `CreatedExpiryPolicy` for time-sensitive data like session tokens.
   - Use `AccessedExpiryPolicy` for frequently accessed data.

2. **Avoid Overloading the Cache**:
   - Limit the size of the cache to prevent memory issues.

3. **Use Strong Key Classes**:
   - Ensure `CacheKey` implements `equals` and `hashCode` properly to avoid collisions.

4. **Monitor Cache Usage**:
   - Use monitoring tools provided by the cache provider (e.g., Ehcache or Hazelcast dashboards).

---

### **Summary**

This approach is highly flexible and allows you to configure and manage caches programmatically using JSR-107. Here's a quick recap:

- **Key Concepts**:
  - `CachingProvider`: Entry point for obtaining a `CacheManager`.
  - `CacheManager`: Manages caches and provides access to them.
  - `Cache`: Stores key-value pairs.
  - `MutableConfiguration`: Defines cache settings like types and expiration policies.

- **Common Use Cases**:
  - Caching database queries, session data, or API responses.
  - Implementing rate limiting or preloading static data.

- **Customization**:
  - You can define custom expiration policies, preload data, or use multiple caches for different purposes.

