### **Declarative Caching in Redis**

Declarative caching in Spring allows you to manage caching using annotations, which simplifies the caching logic and avoids boilerplate code. By using annotations, you can declaratively specify caching behavior for methods, such as storing, updating, or evicting cache entries.

Redis, as a high-performance distributed cache, works seamlessly with Spring’s declarative caching mechanism. The integration is done via **Spring Data Redis** and the **Spring Cache abstraction**.

---

### **Key Concepts of Declarative Caching**

1. **Annotations for Caching**:
   - Use annotations like `@Cacheable`, `@CachePut`, and `@CacheEvict` to manage cache operations.
   - These annotations are method-level and automatically handle caching logic.

2. **Redis as a Cache Backend**:
   - Redis is used as a caching provider, and Spring automatically integrates it using the `RedisCacheManager`.

3. **Serialization**:
   - Redis stores cache entries in binary format (`byte[]`). Hence, objects must implement `Serializable`.

4. **Automatic Management**:
   - Methods annotated with caching annotations automatically store, update, or remove data from the cache.

---

### **Key Annotations for Declarative Caching**

#### 1. **`@Cacheable`**
   - Indicates that the return value of a method should be cached.
   - If the cache already contains the value, the method is not executed; instead, the cached value is returned.

   **Example**:
   ```java
   @Cacheable(value = "products", key = "#id")
   public Product getProduct(String id) {
       // Simulate a database call
       return productRepository.findById(id);
   }
   ```

#### 2. **`@CachePut`**
   - Updates the cache with the latest data after the method execution.
   - The method is always executed, and the return value is stored in the cache.

   **Example**:
   ```java
   @CachePut(value = "products", key = "#product.id")
   public Product updateProduct(Product product) {
       // Update product in the database
       return productRepository.save(product);
   }
   ```

#### 3. **`@CacheEvict`**
   - Removes entries from the cache.
   - Can clear specific entries or all entries in a cache.

   **Example**:
   ```java
   @CacheEvict(value = "products", key = "#id")
   public void deleteProduct(String id) {
       // Delete product from the database
       productRepository.deleteById(id);
   }
   ```

   - **Clear all entries**:
     ```java
     @CacheEvict(value = "products", allEntries = true)
     public void clearCache() {
         // Clear the entire cache
     }
     ```

#### 4. **`@Caching`**
   - Combines multiple caching annotations for complex caching scenarios.

   **Example**:
   ```java
   @Caching(
       put = { @CachePut(value = "products", key = "#product.id") },
       evict = { @CacheEvict(value = "categories", key = "#product.categoryId") }
   )
   public Product saveProduct(Product product) {
       return productRepository.save(product);
   }
   ```

---

### **Steps to Implement Declarative Caching with Redis**

#### **1. Add Dependencies**

Add the required dependencies for Spring Boot, Redis, and caching.

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

#### **3. Configure Redis in `application.properties`**

Set up Redis as the caching provider.

```properties
spring.cache.type=redis
spring.cache.redis.use-key-prefix=true
spring.cache.redis.key-prefix=cache:
spring.cache.redis.cache-null-values=true
spring.cache.redis.time-to-live=60s
```

---

#### **4. Define the Cacheable Entity**

The entity must implement `Serializable` to be stored in Redis.

```java
import lombok.Builder;
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;
import org.springframework.data.redis.core.TimeToLive;

import java.io.Serializable;
import java.util.concurrent.TimeUnit;

@RedisHash("products")
@Builder
public class Product implements Serializable {

    @Id
    private String id;
    private String name;
    private Long price;

    @TimeToLive(unit = TimeUnit.SECONDS)
    private Long ttl = -1L; // Default TTL
}
```

---

#### **5. Create a Service with `@Cacheable`**

Here’s how you can use `@Cacheable` to cache the result of a method.

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Slf4j
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#id")
    public Product getProduct(String id) {
        log.info("Fetching product with ID: {}", id);
        return Product.builder().id(id).name("Sample Product").price(100L).build();
    }
}
```

---

#### **6. Test the Cache**

Write a test to verify that caching works as expected.

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
    public void testCacheable() {
        Product product1 = productService.getProduct("P-001");
        assertNotNull(product1);
        assertEquals("P-001", product1.getId());

        Product product2 = productService.getProduct("P-001");
        assertEquals(product1, product2); // Cached value should be returned
    }
}
```

---

### **Advanced Examples**

#### **1. Dynamic Cache Key**
You can use SpEL (Spring Expression Language) to create dynamic cache keys.

```java
@Cacheable(value = "products", key = "'Product_'.concat(#id)")
public Product getProduct(String id) {
    return productRepository.findById(id);
}
```

---

#### **2. Conditional Caching**
Cache the result only if a condition is met.

```java
@Cacheable(value = "products", key = "#id", condition = "#id.startsWith('P')")
public Product getProduct(String id) {
    return productRepository.findById(id);
}
```

---

#### **3. Cache Expiration**
Set a custom TTL for specific cache entries using `@TimeToLive`.

```java
@RedisHash("products")
public class Product implements Serializable {

    @Id
    private String id;

    @TimeToLive(unit = TimeUnit.SECONDS)
    private Long ttl = 30L; // Cache expires after 30 seconds
}
```

---

#### **4. Cache Eviction on Updates**
Automatically evict cache entries when the data is updated.

```java
@CacheEvict(value = "products", key = "#product.id")
public Product updateProduct(Product product) {
    return productRepository.save(product);
}
```

---

### **Common Use Cases**

1. **Database Query Results**:
   - Cache frequently accessed database results to reduce load.

2. **API Responses**:
   - Cache API responses for faster retrieval.

3. **Session Data**:
   - Store user sessions in Redis.

4. **Rate Limiting**:
   - Use Redis to implement rate-limiting logic.

5. **Temporary Data**:
   - Cache temporary data like OTPs or tokens with a short TTL.

---

### **Best Practices**

1. **Set TTL**:
   - Always configure a TTL to avoid stale data and memory overuse.

2. **Monitor Cache Performance**:
   - Use tools like RedisInsight to monitor cache usage and performance.

3. **Avoid Over-Caching**:
   - Cache only frequently accessed or computationally expensive data.

4. **Use Key Prefixes**:
   - Organize cache entries by using prefixes (e.g., `cache:products`).

5. **Handle Serialization**:
   - Ensure objects are `Serializable` before caching.

