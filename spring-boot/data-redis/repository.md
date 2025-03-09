### **Repository in Redis with Spring Data Redis**

Spring Data Redis supports the **Repository** pattern, similar to what is available in Spring Data JPA. This feature allows developers to interact with Redis data using simple repository interfaces, avoiding manual data manipulation with `RedisTemplate`. It simplifies CRUD operations and leverages Redis's **Hash** data structure to store objects.

---

### **Key Features**

1. **Simplified Data Access**:
   - No need to write boilerplate code for CRUD operations.

2. **Integration with Redis Hash**:
   - Data is stored in Redis as a **Hash** structure.

3. **Annotation Support**:
   - Use annotations like `@KeySpace` and `@Id` to define entities.

4. **Custom Query Support**:
   - Extend repository interfaces to define custom queries.

5. **Transactional Support**:
   - Spring transactions can be applied to repository operations.

---

### **Steps to Implement Redis Repository**

#### **1. Enable Redis Repositories**

To use Redis repositories, annotate your Spring Boot application class with `@EnableRedisRepositories`.

```java
@SpringBootApplication
@EnableScheduling
@EnableRedisRepositories
public class RedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
}
```

---

#### **2. Define an Entity**

Entities represent the data stored in Redis. Use the following annotations:

- **`@KeySpace`**: Specifies the Redis key prefix for the entity.
- **`@Id`**: Marks the field used as the unique identifier.

##### **Example: Product Entity**

```java
import lombok.*;
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@KeySpace("products")
public class Product {
    @Id
    private String id;  // Unique identifier
    private String name;
    private Long price;
}
```

---

#### **3. Define a Repository**

Extend the `KeyValueRepository` interface to create a repository for the entity.

##### **Example: Product Repository**

```java
import org.springframework.data.repository.CrudRepository;

public interface ProductRepository extends CrudRepository<Product, String> {
    // Additional custom query methods can be added here
}
```

---

#### **4. Perform CRUD Operations**

You can now use the repository to perform CRUD operations.

##### **Example: Save, Find, and Delete**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public void performOperations() {
        // Save a product
        Product product = new Product("1", "Laptop", 1000L);
        productRepository.save(product);

        // Find a product by ID
        Product retrievedProduct = productRepository.findById("1").orElse(null);
        System.out.println("Retrieved Product: " + retrievedProduct);

        // Delete a product
        productRepository.deleteById("1");
    }
}
```

---

#### **5. Testing the Repository**

##### **Example: Repository Test**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.Map;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @Test
    void repository() {
        // Save a product
        Product product = Product.builder()
                .id("1")
                .name("Mie Ayam Goreng")
                .price(20_000L)
                .build();
        productRepository.save(product);

        // Verify data in Redis
        Map<Object, Object> map = redisTemplate.opsForHash().entries("products:1");
        assertEquals(product.getId(), map.get("id"));
        assertEquals(product.getName(), map.get("name"));
        assertEquals(product.getPrice().toString(), map.get("price"));

        // Retrieve product from repository
        Product retrievedProduct = productRepository.findById("1").get();
        assertEquals(product, retrievedProduct);
    }
}
```

---

### **Custom Query Methods**

You can define custom query methods in your repository interface by following the naming conventions.

##### **Example: Custom Query**

```java
import java.util.List;

public interface ProductRepository extends CrudRepository<Product, String> {
    List<Product> findByName(String name);  // Find products by name
}
```

---

### **Advanced Features**

#### **1. Pagination and Sorting**

Spring Data Redis supports pagination and sorting for repository methods.

##### **Example: Pagination**

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

Page<Product> page = productRepository.findAll(PageRequest.of(0, 10));
page.getContent().forEach(System.out::println);
```

---

#### **2. TTL (Time-to-Live) for Entities**

You can set TTL for entities using the `@RedisHash` annotation.

##### **Example: Entity with TTL**

```java
import org.springframework.data.redis.core.RedisHash;

@RedisHash(value = "products", timeToLive = 60)  // TTL of 60 seconds
public class Product {
    @Id
    private String id;
    private String name;
    private Long price;
}
```

---

#### **3. Transactions**

Redis repositories can be used within Spring-managed transactions.

##### **Example: Transactional Repository Operations**

```java
import org.springframework.transaction.annotation.Transactional;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Transactional
    public void performTransactionalOperation() {
        Product product1 = new Product("1", "Laptop", 1000L);
        Product product2 = new Product("2", "Phone", 500L);

        productRepository.save(product1);
        productRepository.save(product2);

        // If an exception occurs here, both saves will be rolled back
        throw new RuntimeException("Simulating an error");
    }
}
```

---

### **Best Practices**

1. **Use Key Prefixes**:
   - Use meaningful prefixes (e.g., `products`, `users`) to organize data in Redis.

2. **Avoid Large Hashes**:
   - Keep hash sizes manageable to avoid performance issues.

3. **Monitor TTL**:
   - Use TTL for temporary data to prevent memory overuse.

4. **Leverage Custom Queries**:
   - Use repository methods to simplify complex queries.

5. **Test Thoroughly**:
   - Ensure your repository logic is well-tested, especially for transactional operations.

---

### **Comparison: Redis Repository vs RedisTemplate**

| **Feature**            | **Redis Repository**         | **RedisTemplate**           |
|-------------------------|------------------------------|-----------------------------|
| **Ease of Use**         | High (CRUD via interface)    | Medium (manual operations)  |
| **Custom Queries**      | Supported                   | Requires manual implementation |
| **Transaction Support** | Yes                         | Yes                         |
| **TTL Support**         | Yes                         | Yes                         |
| **Flexibility**         | Limited to repository methods | Full control over Redis commands |

---

### **Use Cases**

1. **Caching**:
   - Store frequently accessed data like product details, user sessions, etc.

2. **Real-Time Data**:
   - Use repositories for real-time leaderboard data or analytics.

3. **Temporary Data**:
   - Use TTL for storing temporary data like OTPs or session tokens.

4. **Key-Value Storage**:
   - Store simple key-value pairs like configurations or settings.

