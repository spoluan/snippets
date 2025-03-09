### **CacheEvict in Redis**

The `@CacheEvict` annotation in Spring is used to **remove data from the cache**. It is particularly useful when you want to ensure that the cache does not hold stale or outdated data after an update or delete operation. This annotation works well with Redis as a caching backend, allowing for efficient cache eviction and synchronization with the data source.

---

### **Key Characteristics of `@CacheEvict`**

1. **Removes Specific Cache Entries**:
   - Deletes specific entries from the cache based on the provided key.

2. **Supports Clearing Entire Cache**:
   - You can clear all entries in a cache using the `allEntries` attribute.

3. **Conditional Eviction**:
   - Cache eviction can be controlled using conditions defined with Spring Expression Language (SpEL).

4. **No Return Value**:
   - Unlike `@Cacheable` or `@CachePut`, `@CacheEvict` does not store any return value in the cache.

---

### **Syntax of `@CacheEvict`**

```java
@CacheEvict(value = "cacheName", key = "#parameterName")
public void methodName(ParameterType parameter) {
    // Method logic
}
```

- **`value`**: Specifies the name of the cache.
- **`key`**: Defines the cache key to evict using SpEL.
- **`allEntries`**: If `true`, clears all entries in the cache.
- **`beforeInvocation`**: If `true`, the cache eviction happens before the method execution (default is `false`).

---

### **When to Use `@CacheEvict`**

- To remove outdated data from the cache after a delete or update operation.
- To clear the entire cache when necessary.
- To conditionally remove cache entries based on business logic.

---

### **Basic Example of `@CacheEvict`**

#### **1. Remove a Specific Cache Entry**

```java
import org.springframework.cache.annotation.CacheEvict;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @CacheEvict(value = "products", key = "#id")
    public void remove(String id) {
        System.out.println("Removing product with ID: " + id);
        // Simulate removing the product from the database
    }
}
```

- **Explanation**:
  - When the `remove` method is called, the cache entry with the specified `id` is deleted from the `products` cache.

---

#### **2. Test the `@CacheEvict` Functionality**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertNull;

@SpringBootTest
public class ProductServiceTest {

    @Autowired
    private ProductService productService;

    @Test
    public void testRemoveProduct() {
        productService.remove("P003");

        // Attempt to fetch the product from the cache
        Product product = productService.getProduct("P003");
        assertNull(product); // Verify that the cache entry is removed
    }
}
```

---

### **Advanced Examples**

#### **1. Clear All Entries in a Cache**

Use `allEntries = true` to remove all entries from the specified cache.

```java
@CacheEvict(value = "products", allEntries = true)
public void clearCache() {
    System.out.println("Clearing all entries from the products cache.");
}
```

- **Explanation**:
  - This method clears the entire `products` cache, regardless of the keys.

---

#### **2. Evict Before Method Execution**

By default, cache eviction happens **after** the method is executed. Use `beforeInvocation = true` to evict the cache **before** the method is executed.

```java
@CacheEvict(value = "products", key = "#id", beforeInvocation = true)
public void remove(String id) {
    throw new RuntimeException("Simulating an exception during method execution.");
}
```

- **Explanation**:
  - Even if the method throws an exception, the cache entry with the specified `id` will still be removed.

---

#### **3. Conditional Cache Eviction**

You can use the `condition` attribute to define when the cache should be evicted.

```java
@CacheEvict(value = "products", key = "#id", condition = "#id.startsWith('P')")
public void remove(String id) {
    System.out.println("Removing product with ID: " + id);
}
```

- **Explanation**:
  - The cache is evicted only if the `id` starts with the letter "P".

---

#### **4. Combine `@CacheEvict` with Other Annotations**

You can combine `@CacheEvict` with other caching annotations, such as `@Caching`.

```java
@Caching(
    evict = {
        @CacheEvict(value = "products", key = "#id"),
        @CacheEvict(value = "categories", key = "#categoryId")
    }
)
public void removeProductAndCategory(String id, String categoryId) {
    System.out.println("Removing product and category from the cache.");
}
```

- **Explanation**:
  - This method removes the cache entry for the product and the associated category.

---

#### **5. Evict Multiple Cache Entries**

You can evict multiple cache entries by specifying multiple keys.

```java
@CacheEvict(value = "products", key = "{#id1, #id2}")
public void removeMultiple(String id1, String id2) {
    System.out.println("Removing products with IDs: " + id1 + " and " + id2);
}
```

- **Explanation**:
  - This method removes cache entries for both `id1` and `id2`.

---

### **Common Use Cases for `@CacheEvict`**

1. **Delete Operations**:
   - Remove the cache entry after deleting an entity from the database.

2. **Update Operations**:
   - Evict the cache entry after updating an entity to ensure the cache contains fresh data.

3. **Clear Cache on Demand**:
   - Provide an endpoint or method to clear the cache when needed.

4. **Data Synchronization**:
   - Evict cache entries to synchronize the cache with external systems or data sources.

---

### **Best Practices for `@CacheEvict`**

1. **Use `allEntries` with Caution**:
   - Clearing the entire cache can be expensive and should be used sparingly.

2. **Set TTL for Cache Entries**:
   - Combine `@CacheEvict` with TTL (Time-to-Live) for automatic cache expiration.

3. **Monitor Cache Performance**:
   - Use tools like RedisInsight to monitor cache eviction and performance.

4. **Test Cache Eviction**:
   - Ensure proper testing of cache eviction scenarios to avoid stale data issues.

5. **Combine with `@CachePut` or `@Cacheable`**:
   - Use `@CacheEvict` alongside other caching annotations for a comprehensive caching strategy.

---

### **Configuration for Redis Cache**

To use Redis as the caching provider with `@CacheEvict`, configure Spring Boot as follows:

#### **`application.properties`**

```properties
spring.cache.type=redis
spring.cache.redis.time-to-live=60s
spring.cache.redis.use-key-prefix=true
spring.cache.redis.key-prefix=cache:
```

#### **Custom Redis Cache Manager**

```java
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;

import java.time.Duration;

@Configuration
@EnableCaching
public class RedisConfig {

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofMinutes(5)) // Set TTL to 5 minutes
                .disableCachingNullValues();

        return RedisCacheManager.builder(connectionFactory)
                .cacheDefaults(config)
                .build();
    }
}
```
