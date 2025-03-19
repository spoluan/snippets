### **Caching in Java Spring Boot**

Caching is a mechanism to store frequently used data in memory so that it can be retrieved faster, avoiding unnecessary computations or database calls. Spring Boot provides excellent support for caching with annotations and configuration options.

---

### **1. Why Use Caching?**
- **Performance Improvement**: Reduces the time taken for expensive operations like database queries, API calls, or computations.
- **Reduced Load**: Minimizes load on the database or external services.
- **Scalability**: Helps in scaling applications by reducing resource usage.

---

### **2. Spring Boot Cache Overview**
Spring Boot provides a caching abstraction through the `@Cacheable`, `@CachePut`, and `@CacheEvict` annotations. It supports multiple cache providers like:
- **In-Memory**: e.g., `ConcurrentHashMap` (default)
- **Distributed Caches**: e.g., Redis, Hazelcast, Ehcache, etc.

---

### **3. How to Enable Caching in Spring Boot**

#### **Step 1: Add Dependencies**
Add the following dependency to your `pom.xml` (for Maven):

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

If you plan to use a specific cache provider (e.g., Redis or Ehcache), include the corresponding dependency. For example, for Redis:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

#### **Step 2: Enable Caching**
Enable caching in your Spring Boot application by adding the `@EnableCaching` annotation to your main class:

```java
@SpringBootApplication
@EnableCaching
public class CachingApplication {
    public static void main(String[] args) {
        SpringApplication.run(CachingApplication.class, args);
    }
}
```

---

### **4. Spring Boot Caching Annotations**

#### **4.1. `@Cacheable`**
The `@Cacheable` annotation is used to cache the result of a method. If the method is called again with the same parameters, the cached result is returned instead of executing the method.

**Example**:
```java
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#productId")
    public Product getProductById(Long productId) {
        // Simulate a slow database call
        simulateSlowService();
        return new Product(productId, "Product " + productId);
    }

    private void simulateSlowService() {
        try {
            Thread.sleep(3000); // Simulate delay
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

- **`value`**: The name of the cache (e.g., `products`).
- **`key`**: The key used to store/retrieve the cache (e.g., `#productId`).

---

#### **4.2. `@CacheEvict`**
The `@CacheEvict` annotation is used to remove an entry from the cache. It is useful when the cached data becomes stale and needs to be updated.

**Example**:
```java
@Service
public class ProductService {

    @CacheEvict(value = "products", key = "#productId")
    public void deleteProduct(Long productId) {
        // Logic to delete the product
        System.out.println("Product with ID " + productId + " deleted.");
    }
}
```

- **`value`**: The name of the cache.
- **`key`**: The key to be evicted from the cache.
- **`allEntries = true`**: Evicts all entries in the cache.

**Evict All Entries Example**:
```java
@CacheEvict(value = "products", allEntries = true)
public void clearAllProductsCache() {
    System.out.println("All product caches cleared.");
}
```

---

#### **4.3. `@CachePut`**
The `@CachePut` annotation is used to update the cache. Unlike `@Cacheable`, the method is always executed, and the result is stored in the cache.

**Example**:
```java
@Service
public class ProductService {

    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        // Logic to update the product
        System.out.println("Product with ID " + product.getId() + " updated.");
        return product;
    }
}
```

---

#### **4.4. `@Caching`**
The `@Caching` annotation is used when you need to apply multiple cache annotations on a single method.

**Example**:
```java
@Service
public class ProductService {

    @Caching(
        put = { @CachePut(value = "products", key = "#product.id") },
        evict = { @CacheEvict(value = "categories", key = "#product.categoryId") }
    )
    public Product saveProduct(Product product) {
        // Logic to save the product
        return product;
    }
}
```

---

### **5. Cache Configuration**

#### **5.1. Default In-Memory Cache**
By default, Spring Boot uses `ConcurrentHashMap` as the cache provider. No additional configuration is needed.

---

#### **5.2. Using Redis as Cache Provider**
To use Redis as a cache provider:

1. Add the Redis dependency:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. Configure Redis in `application.properties`:
   ```properties
   spring.cache.type=redis
   spring.redis.host=localhost
   spring.redis.port=6379
   ```

3. Example Service with Redis Cache:
   ```java
   @Service
   public class ProductService {

       @Cacheable(value = "products", key = "#productId")
       public Product getProductById(Long productId) {
           return new Product(productId, "Product " + productId);
       }
   }
   ```

---

#### **5.3. Using Ehcache as Cache Provider**
To use Ehcache:

1. Add the Ehcache dependency:
   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-cache</artifactId>
   </dependency>
   <dependency>
       <groupId>org.ehcache</groupId>
       <artifactId>ehcache</artifactId>
   </dependency>
   ```

2. Create an `ehcache.xml` configuration file:
   ```xml
   <config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns="http://www.ehcache.org/v3"
           xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core-3.0.xsd">
       <cache alias="products">
           <expiry>
               <ttl unit="seconds">60</ttl>
           </expiry>
           <heap unit="entries">100</heap>
       </cache>
   </config>
   ```

3. Configure Ehcache in `application.properties`:
   ```properties
   spring.cache.jcache.config=classpath:ehcache.xml
   ```

---

### **6. Cache Key Customization**

By default, Spring uses SpEL (Spring Expression Language) to define cache keys. You can customize the key generation by implementing the `KeyGenerator` interface.

**Example**:
```java
@Component("customKeyGenerator")
public class CustomKeyGenerator implements KeyGenerator {

    @Override
    public Object generate(Object target, Method method, Object... params) {
        return method.getName() + "_" + Arrays.toString(params);
    }
}
```

Use the custom key generator:
```java
@Cacheable(value = "products", keyGenerator = "customKeyGenerator")
public Product getProductById(Long productId) {
    return new Product(productId, "Product " + productId);
}
```

---

### **7. Cache Expiration**

You can configure cache expiration in the cache provider (e.g., Redis, Ehcache). For example, in Redis, you can set a Time-to-Live (TTL) for cached entries.

**Redis Example**:
```properties
spring.cache.redis.time-to-live=60000 # 60 seconds
```

---

### **8. Complete Example**

**Controller**:
```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/{id}")
    public Product getProduct(@PathVariable Long id) {
        return productService.getProductById(id);
    }

    @PutMapping
    public Product updateProduct(@RequestBody Product product) {
        return productService.updateProduct(product);
    }

    @DeleteMapping("/{id}")
    public void deleteProduct(@PathVariable Long id) {
        productService.deleteProduct(id);
    }
}
```

**Service**:
```java
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#productId")
    public Product getProductById(Long productId) {
        simulateSlowService();
        return new Product(productId, "Product " + productId);
    }

    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        return product;
    }

    @CacheEvict(value = "products", key = "#productId")
    public void deleteProduct(Long productId) {
        System.out.println("Product with ID " + productId + " deleted.");
    }

    private void simulateSlowService() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

---

### **9. Summary**

- Use `@Cacheable` to cache method results.
- Use `@CacheEvict` to remove stale cache entries.
- Use `@CachePut` to update the cache.
- Use `@Caching` for complex cache operations.
- Choose a cache provider (e.g., Redis, Ehcache) based on your application's needs.
- Customize cache keys and expiration settings as needed.

