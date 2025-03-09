### **Spring Caching with Redis**

Spring Caching is a powerful abstraction provided by the Spring Framework to cache data and improve application performance. By using caching, frequently accessed data can be stored in memory, reducing database calls and improving response times. Spring Caching integrates seamlessly with Redis, allowing you to use Redis as a caching layer.

---

### **Key Features of Spring Caching**

1. **Annotation-based Caching**:
   - Use simple annotations like `@Cacheable`, `@CachePut`, and `@CacheEvict` to enable caching functionality.

2. **Integration with Redis**:
   - Spring Data Redis allows Redis to be used as a caching provider.

3. **Cache Manager**:
   - Spring provides a `CacheManager` interface to manage cache operations.

4. **Customizable TTL**:
   - Configure Time-to-Live (TTL) for cached data to control cache expiration.

5. **Support for Multiple Backends**:
   - In addition to Redis, Spring Caching supports in-memory caching, EhCache, Hazelcast, and others.

---

### **Steps to Implement Redis Caching in Spring**

#### **1. Add Dependencies**

Add the required dependencies for Spring Boot, Spring Data Redis, and the Redis client.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
    <groupId>io.lettuce.core</groupId>
    <artifactId>lettuce-core</artifactId>
</dependency>
```

---

#### **2. Enable Caching**

Use the `@EnableCaching` annotation in your Spring Boot application class to enable caching.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@SpringBootApplication
@EnableCaching
public class RedisCachingApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisCachingApplication.class, args);
    }
}
```

---

#### **3. Configure Redis Cache in `application.properties`**

Set up Redis as the caching provider by configuring the following properties.

```properties
# Specify Redis as the cache type
spring.cache.type=redis

# Use key prefixes for better organization
spring.cache.redis.use-key-prefix=true
spring.cache.redis.key-prefix=cache:

# Enable statistics for cache management
spring.cache.redis.enable-statistics=true

# Allow caching of null values
spring.cache.redis.cache-null-values=true

# Set default TTL (Time-to-Live) for cached data
spring.cache.redis.time-to-live=60s
```

---

#### **4. Create a Cacheable Service**

Define a service with methods annotated for caching.

```java
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public String getProductById(String id) {
        // Simulate a slow database call
        simulateSlowService();
        return "Product-" + id;
    }

    private void simulateSlowService() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new IllegalStateException(e);
        }
    }
}
```

- **`@Cacheable`**: Caches the method's return value.
  - `value`: The cache name (e.g., `products`).
  - `key`: The key used to store the cache entry (e.g., `#id`).

---

#### **5. Testing the Cache**

Create a simple REST controller to test the caching functionality.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/product/{id}")
    public String getProduct(@PathVariable String id) {
        return productService.getProductById(id);
    }
}
```

---

### **Advanced Caching Annotations**

#### **1. `@CachePut`**

- Updates the cache with the latest data after a method execution.

```java
@CachePut(value = "products", key = "#product.id")
public Product updateProduct(Product product) {
    // Update product in the database
    return product;
}
```

---

#### **2. `@CacheEvict`**

- Removes entries from the cache.

```java
@CacheEvict(value = "products", key = "#id")
public void deleteProduct(String id) {
    // Delete product from the database
}
```

- **`allEntries = true`**: Clears all entries in the cache.

---

#### **3. `@Caching`**

- Combines multiple caching annotations.

```java
@Caching(
    put = { @CachePut(value = "products", key = "#product.id") },
    evict = { @CacheEvict(value = "products", key = "#product.id") }
)
public Product saveOrUpdateProduct(Product product) {
    // Save or update product
    return product;
}
```

---

### **Using CacheManager**

Spring uses the `CacheManager` interface to manage caching. When using Redis, the `RedisCacheManager` is automatically configured.

#### **Example: Using CacheManager**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.Cache;
import org.springframework.cache.CacheManager;
import org.springframework.stereotype.Service;

@Service
public class CustomCacheService {

    @Autowired
    private CacheManager cacheManager;

    public void manipulateCache() {
        Cache cache = cacheManager.getCache("products");
        cache.put("123", "Product-123");
        String product = cache.get("123", String.class);
        System.out.println("Cached Product: " + product);
    }
}
```

---

### **Testing Redis Caching**

#### **JUnit Test Example**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest
public class ProductServiceTest {

    @Autowired
    private ProductService productService;

    @Test
    public void testCaching() {
        long start = System.currentTimeMillis();
        String product1 = productService.getProductById("1");
        long timeTaken1 = System.currentTimeMillis() - start;

        start = System.currentTimeMillis();
        String product2 = productService.getProductById("1");
        long timeTaken2 = System.currentTimeMillis() - start;

        assertEquals(product1, product2);
        assertTrue(timeTaken2 < timeTaken1); // Cached call should be faster
    }
}
```

---

### **Cache TTL (Time-to-Live) Example**

You can dynamically set TTL for specific caches.

```java
@Configuration
public class CacheConfig {

    @Bean
    public RedisCacheManager redisCacheManager(RedisConnectionFactory redisConnectionFactory) {
        RedisCacheConfiguration cacheConfig = RedisCacheConfiguration.defaultCacheConfig()
            .entryTtl(Duration.ofMinutes(5)) // Set TTL to 5 minutes
            .disableCachingNullValues();

        return RedisCacheManager.builder(redisConnectionFactory)
            .cacheDefaults(cacheConfig)
            .build();
    }
}
```

---

### **Common Use Cases for Redis Caching**

1. **Database Query Results**:
   - Cache frequently accessed query results to reduce database load.

2. **Sessions**:
   - Store user session data in Redis for fast retrieval.

3. **API Responses**:
   - Cache API responses to improve performance and reduce latency.

4. **Rate Limiting**:
   - Use Redis to implement rate-limiting logic.

5. **Temporary Data**:
   - Store temporary data like OTPs or tokens with a short TTL.

---

### **Best Practices**

1. **Set TTL for Caches**:
   - Always set a TTL to avoid stale data and memory overuse.

2. **Monitor Cache Usage**:
   - Use tools like RedisInsight or Spring Actuator to monitor cache performance.

3. **Use Key Prefixes**:
   - Organize cache keys with prefixes for better management.

4. **Avoid Over-Caching**:
   - Cache only frequently accessed data to prevent unnecessary memory usage.

5. **Evict Stale Data**:
   - Use `@CacheEvict` to clear old or unused cache entries.

