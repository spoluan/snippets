### **How Does Caching Work in Spring Boot?**

Caching in Spring Boot works by storing the results of method calls in a cache (in-memory, Redis, Ehcache, etc.). When the same method is called again with the same parameters, the cached result is returned instead of executing the method again. This saves time and resources, especially for expensive operations like database queries or API calls.

---

### **How Data Is Retrieved from Cache?**

1. **First Call**:
   - When a method annotated with `@Cacheable` is called for the first time, the method executes, and the result is stored in the cache.
   - The cache is identified by the `value` (cache name), and the key is generated based on the method parameters or a custom key generator.

2. **Subsequent Calls**:
   - When the same method is called again with the same parameters, Spring checks if the result is already in the cache.
   - If the result exists in the cache, it is returned directly without executing the method.
   - If the result is not in the cache, the method is executed, and the result is cached.

---

### **Real Examples**

#### **Example 1: Basic Caching with `@Cacheable`**

Imagine you have a service to fetch a product from a database:

```java
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#productId")
    public Product getProductById(Long productId) {
        System.out.println("Fetching product from database...");
        // Simulate a database call
        return new Product(productId, "Product " + productId);
    }
}
```

---

#### **Step-by-Step Execution**

1. **First Request**:
   ```java
   Product product = productService.getProductById(1L);
   ```
   - The method `getProductById(1L)` is called.
   - Since the cache (`products`) does not contain an entry for the key `1L`, the method executes.
   - The result is stored in the cache with the key `1L`.
   - Output:
     ```
     Fetching product from database...
     ```

2. **Second Request**:
   ```java
   Product product = productService.getProductById(1L);
   ```
   - The method `getProductById(1L)` is called again.
   - This time, Spring checks the cache (`products`) and finds the result for the key `1L`.
   - The cached result is returned without executing the method.
   - Output:
     *(No "Fetching product from database..." message because the result is retrieved from the cache.)*

---

#### **Example 2: Updating the Cache with `@CachePut`**

Now, suppose you want to update the product and also update the cache.

```java
@Service
public class ProductService {

    @CachePut(value = "products", key = "#product.id")
    public Product updateProduct(Product product) {
        System.out.println("Updating product in database...");
        // Simulate updating the product in the database
        return product;
    }
}
```

---

#### **Step-by-Step Execution**

1. **Update Product**:
   ```java
   Product updatedProduct = productService.updateProduct(new Product(1L, "Updated Product"));
   ```
   - The method `updateProduct` is called.
   - The cache (`products`) is updated with the new result for the key `1L`.
   - Output:
     ```
     Updating product in database...
     ```

2. **Fetch Updated Product**:
   ```java
   Product product = productService.getProductById(1L);
   ```
   - The method `getProductById(1L)` is called.
   - The updated product is retrieved from the cache (`products`) without executing the method.

---

#### **Example 3: Removing Data from Cache with `@CacheEvict`**

Suppose you delete a product and want to remove it from the cache.

```java
@Service
public class ProductService {

    @CacheEvict(value = "products", key = "#productId")
    public void deleteProduct(Long productId) {
        System.out.println("Deleting product from database...");
        // Simulate deleting the product from the database
    }
}
```

---

#### **Step-by-Step Execution**

1. **Delete Product**:
   ```java
   productService.deleteProduct(1L);
   ```
   - The method `deleteProduct(1L)` is called.
   - The cache (`products`) entry for the key `1L` is removed.
   - Output:
     ```
     Deleting product from database...
     ```

2. **Fetch Deleted Product**:
   ```java
   Product product = productService.getProductById(1L);
   ```
   - The method `getProductById(1L)` is called.
   - Since the cache no longer contains an entry for the key `1L`, the method executes and fetches the product from the database.

---

#### **Example 4: Custom Cache Key**

By default, the cache key is generated based on the method parameters. However, you can customize the key.

```java
@Service
public class ProductService {

    @Cacheable(value = "products", key = "#productId + '_' + #categoryId")
    public Product getProductByCategory(Long productId, Long categoryId) {
        System.out.println("Fetching product by category from database...");
        // Simulate a database call
        return new Product(productId, "Product " + productId, categoryId);
    }
}
```

---

#### **Step-by-Step Execution**

1. **First Request**:
   ```java
   Product product = productService.getProductByCategory(1L, 100L);
   ```
   - The cache key is generated as `1_100`.
   - The method executes, and the result is stored in the cache with the key `1_100`.

2. **Second Request**:
   ```java
   Product product = productService.getProductByCategory(1L, 100L);
   ```
   - The cache key `1_100` is found in the cache.
   - The cached result is returned.

---

### **How Cache Providers Store Data**

- **In-Memory Cache (Default)**:
  - Spring Boot uses `ConcurrentHashMap` to store cached data.
  - Example: 
    ```
    Cache 'products' -> Key: 1L -> Value: Product(1L, "Product 1")
    ```

- **Redis**:
  - Data is stored in Redis as key-value pairs.
  - Example (Redis CLI):
    ```
    GET products::1
    ```
    Output:
    ```
    {"id":1,"name":"Product 1"}
    ```

- **Ehcache**:
  - Data is stored in an Ehcache-managed memory space.
  - Example:
    ```
    Cache 'products' -> Key: 1L -> Value: Product(1L, "Product 1")
    ```

---

### **Real-World Scenarios**

1. **Database Query Optimization**:
   - Instead of querying the database repeatedly for the same data, cache the results.
   - Example: Caching frequently accessed user profiles.

2. **API Rate Limiting**:
   - Cache the response of third-party API calls to reduce the number of requests.
   - Example: Caching weather data for 10 minutes.

3. **Session Management**:
   - Use caching to store session data, reducing the load on the database.
   - Example: Storing user authentication tokens in Redis.

4. **E-Commerce Applications**:
   - Cache product details, inventory, pricing, and category data to improve performance.
   - Example: Caching product search results.

---

### **Summary**

- **Caching Workflow**:
  - **First Call**: Executes the method and stores the result in the cache.
  - **Subsequent Calls**: Retrieves the result from the cache.
  - **Cache Updates**: Use `@CachePut` to update the cache.
  - **Cache Eviction**: Use `@CacheEvict` to remove stale data.

- **Key Features**:
  - Use `@Cacheable` for caching method results.
  - Use `@CachePut` to update the cache after a method call.
  - Use `@CacheEvict` to remove specific or all cache entries.

- **Cache Providers**:
  - In-memory (default), Redis, Ehcache, Hazelcast, etc.

