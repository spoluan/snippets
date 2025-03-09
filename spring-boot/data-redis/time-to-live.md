### **Time to Live (TTL) in Redis**

Time to Live (TTL) is a feature in Redis that specifies how long a key should exist before it is automatically deleted. This is particularly useful for managing temporary data like session tokens, OTPs, or cache entries. In Spring Data Redis, TTL can be configured at the **entity level** using the `@TimeToLive` annotation.

---

### **Key Features of TTL in Redis**

1. **Automatic Expiry**:
   - Keys with TTL are automatically removed by Redis after the specified duration.

2. **Granularity**:
   - TTL can be set in seconds or milliseconds.

3. **Efficient Memory Management**:
   - Helps prevent memory overuse by automatically cleaning up expired data.

4. **Dynamic TTL**:
   - TTL can be dynamically set per entity or key.

5. **Supported in Spring Data Redis**:
   - TTL can be defined for entities using the `@TimeToLive` annotation.

---

### **How TTL Works in Spring Data Redis**

#### **1. `@TimeToLive` Annotation**

The `@TimeToLive` annotation is used on an entity field to specify its expiration time. The field must be of a numeric type (e.g., `Long` or `Integer`), representing the time-to-live duration.

- **Default Value**:
  - A value of `-1` means the key will not expire.
  
- **Unit**:
  - You can specify the unit of time (e.g., seconds, minutes) using the `unit` attribute.

---

### **Steps to Use TTL in Spring Data Redis**

#### **Step 1: Enable Redis Repositories**

Ensure your Spring Boot application is annotated with `@EnableRedisRepositories`.

```java
@SpringBootApplication
@EnableRedisRepositories
public class RedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
}
```

---

#### **Step 2: Define an Entity with TTL**

Use the `@TimeToLive` annotation to define the TTL for the entity.

##### **Example: Product Entity**

```java
import lombok.*;
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;
import org.springframework.data.redis.core.TimeToLive;

import java.util.concurrent.TimeUnit;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@RedisHash("products")
public class Product {
    @Id
    private String id;
    private String name;
    private Long price;

    @TimeToLive(unit = TimeUnit.SECONDS)
    private Long ttl = -1L;  // Default: no expiration
}
```

- **`@RedisHash`**: Specifies the key prefix for the entity.
- **`@TimeToLive`**: Defines the expiration time in seconds.

---

#### **Step 3: Create a Repository**

Create a repository interface for the entity.

```java
import org.springframework.data.repository.CrudRepository;

public interface ProductRepository extends CrudRepository<Product, String> {
}
```

---

#### **Step 4: Save and Test TTL Functionality**

##### **Example: Saving an Entity with TTL**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public void saveProductWithTTL() {
        Product product = Product.builder()
                .id("1")
                .name("Laptop")
                .price(1000L)
                .ttl(10L)  // Expires in 10 seconds
                .build();

        productRepository.save(product);
        System.out.println("Product saved with TTL of 10 seconds.");
    }
}
```

---

### **Testing TTL**

#### **JUnit Test for TTL**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.time.Duration;

import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.junit.jupiter.api.Assertions.assertTrue;

@SpringBootTest
public class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;

    @Test
    void testTTL() throws InterruptedException {
        // Save a product with a TTL of 3 seconds
        Product product = Product.builder()
                .id("1")
                .name("Mie Ayam Goreng")
                .price(20_000L)
                .ttl(3L)  // Expires in 3 seconds
                .build();

        productRepository.save(product);

        // Ensure the product exists immediately after saving
        assertTrue(productRepository.findById("1").isPresent());

        // Wait for 5 seconds (TTL is 3 seconds)
        Thread.sleep(Duration.ofSeconds(5));

        // Ensure the product is no longer available
        assertFalse(productRepository.findById("1").isPresent());
    }
}
```

---

### **Setting TTL Dynamically**

You can dynamically set the TTL for each entity instance based on specific use cases.

##### **Example: Dynamic TTL**

```java
Product productShortTTL = Product.builder()
        .id("2")
        .name("Short-lived Product")
        .price(500L)
        .ttl(5L)  // Expires in 5 seconds
        .build();

Product productLongTTL = Product.builder()
        .id("3")
        .name("Long-lived Product")
        .price(1000L)
        .ttl(60L)  // Expires in 60 seconds
        .build();

productRepository.save(productShortTTL);
productRepository.save(productLongTTL);
```

---

### **TTL in Redis Commands**

When you save an entity with TTL, Redis automatically sets an expiration time for the key.

- **Command to check TTL**:
  ```bash
  TTL key
  ```

- **Example Output**:
  ```
  10  # Key will expire in 10 seconds
  ```

- **Command to remove TTL**:
  ```bash
  PERSIST key
  ```

---

### **Use Cases for TTL**

1. **Session Management**:
   - Automatically expire user sessions after a specific period.

2. **Caching**:
   - Store frequently accessed data with an expiration time to keep the cache fresh.

3. **OTP and Token Management**:
   - Store OTPs or authentication tokens with a short lifespan.

4. **Temporary Data**:
   - Use TTL for temporary or one-time-use data like file upload links.

5. **Rate Limiting**:
   - Use TTL to track and expire rate-limiting counters.

---

### **Best Practices for Using TTL**

1. **Set Appropriate TTL Values**:
   - Ensure the TTL duration aligns with your use case (e.g., short TTL for OTPs, longer TTL for cache).

2. **Monitor Expired Keys**:
   - Use Redis monitoring tools to track and analyze expired keys.

3. **Avoid Overuse**:
   - Avoid setting TTL for every key unless necessary, as it may increase Redis's workload.

4. **Fallback Mechanism**:
   - Have a fallback mechanism in case a key expires unexpectedly.

5. **Use TTL for Cleanup**:
   - Use TTL to automatically clean up temporary or unused data.

---

### **Comparison: TTL with RedisTemplate vs Repository**

| **Feature**             | **RedisTemplate**            | **Repository with @TimeToLive** |
|--------------------------|------------------------------|----------------------------------|
| **Ease of Use**          | Manual (requires code)       | Automatic (annotation-based)    |
| **Dynamic TTL**          | Fully configurable           | Limited to entity-level TTL     |
| **Flexibility**          | High                        | Medium                          |
| **Integration**          | Requires manual handling     | Integrated with Spring Data     |

