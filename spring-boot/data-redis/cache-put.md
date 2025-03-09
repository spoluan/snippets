### **CachePut in Redis**

The `@CachePut` annotation in Spring is used to update or store data in the cache **without skipping the method execution**. Unlike `@Cacheable`, which skips the method execution if the cache already contains the value, `@CachePut` ensures that the method is always executed and the returned value is stored (or updated) in the cache.

This is particularly useful when you need to update the cache with new data after modifying or saving it in the database.

---

### **Key Characteristics of `@CachePut`**

1. **Always Executes the Method**:
   - The annotated method is always executed, regardless of whether the cache contains the data or not.

2. **Updates the Cache**:
   - The return value of the method is stored or updated in the cache.

3. **Key and Value**:
   - You can specify the cache key using the `key` attribute.
   - The `value` attribute specifies the cache name.

4. **No Cache Lookup**:
   - Unlike `@Cacheable`, `@CachePut` does not check the cache for existing data. It directly updates the cache with the method's return value.

---

### **Syntax of `@CachePut`**

```java
@CachePut(value = "cacheName", key = "#parameterName")
public ReturnType methodName(ParameterType parameter) {
    // Method logic
    return returnValue;
}
```

- **`value`**: Specifies the name of the cache.
- **`key`**: Defines the cache key using Spring Expression Language (SpEL).

---

### **When to Use `@CachePut`**

- When you want to update the cache after modifying the data in the database.
- When you want to ensure the cache always contains the latest data.
- When saving new entries in the cache.

---

### **Basic Example of `@CachePut`**

#### **1. Update the Cache After Saving Data**

```java
import org.springframework.cache.annotation.CachePut;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @CachePut(value = "products", key = "#product.id")
    public Product save(Product product) {
        // Simulate saving the product to the database
        System.out.println("Saving product to the database: " + product);
        return product;
    }
}
```

- **Explanation**:
  - Every time the `save` method is called, the product is saved to the database and the cache is updated with the product's data.

---

#### **2. Test the `@CachePut` Functionality**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class ProductServiceTest {

    @Autowired
    private ProductService productService;

    @Test
    public void testSaveProduct() {
        Product product = new Product("P002", "Sample Product", 100L);
        productService.save(product);

        Product cachedProduct = productService.getProduct("P002");
        assertEquals(product, cachedProduct); // Ensure the cache is updated
    }
}
```

---

### **Advanced Examples**

#### **1. Dynamic Cache Key**

Use SpEL to dynamically generate the cache key.

```java
@CachePut(value = "products", key = "'Product_'.concat(#product.id)")
public Product save(Product product) {
    return productRepository.save(product);
}
```

- **Key Example**: If `product.id = "P001"`, the cache key will be `Product_P001`.

---

#### **2. Conditional Cache Update**

You can conditionally update the cache using the `condition` attribute.

```java
@CachePut(value = "products", key = "#product.id", condition = "#product.price > 100")
public Product save(Product product) {
    return productRepository.save(product);
}
```

- **Explanation**:
  - The cache is updated only if the product's price is greater than 100.

---

#### **3. Using Multiple Caches**

You can update multiple caches simultaneously.

```java
@CachePut(value = {"products", "featuredProducts"}, key = "#product.id")
public Product save(Product product) {
    return productRepository.save(product);
}
```

- **Explanation**:
  - The product is stored in both the `products` and `featuredProducts` caches.

---

#### **4. Combining `@CachePut` with `@CacheEvict`**

In some cases, you may need to update one cache and evict another.

```java
@Caching(
    put = @CachePut(value = "products", key = "#product.id"),
    evict = @CacheEvict(value = "categories", key = "#product.categoryId")
)
public Product save(Product product) {
    return productRepository.save(product);
}
```

- **Explanation**:
  - The `products` cache is updated with the product data.
  - The `categories` cache is evicted for the associated category.

---

#### **5. Handling Null Values**

By default, Spring Cache does not store `null` values in the cache. You can configure Spring to allow caching of null values.

```properties
spring.cache.redis.cache-null-values=true
```

---

### **Common Use Cases for `@CachePut`**

1. **Updating Cache After Database Operations**:
   - When a product is saved or updated, the cache is updated to reflect the latest data.

2. **Synchronizing Cache with External Systems**:
   - If the data source is updated by an external system, you can use `@CachePut` to update the cache when the method is called.

3. **Ensuring Consistency Between Cache and Database**:
   - Use `@CachePut` to ensure that the cache always contains the latest data after a database update.

---

### **Best Practices for `@CachePut`**

1. **Use Meaningful Cache Keys**:
   - Use descriptive and unique cache keys to avoid conflicts.

2. **Avoid Overwriting Critical Data**:
   - Be cautious when updating shared caches to avoid overwriting critical data.

3. **Combine with `@CacheEvict` if Necessary**:
   - In scenarios where the cache contains derived data, consider evicting related caches to maintain consistency.

4. **Monitor Cache Performance**:
   - Use tools like RedisInsight or Spring Actuator to monitor cache usage and performance.

5. **Set TTL (Time-to-Live)**:
   - Configure a TTL for cached data to prevent stale data from persisting indefinitely.

---

### **Configuration for Redis Cache**

To use Redis as the caching provider with `@CachePut`, configure Spring Boot as follows:

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

