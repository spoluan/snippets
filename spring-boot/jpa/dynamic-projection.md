### **Dynamic Projection in JPA**

Dynamic projection in JPA allows you to retrieve data in different formats (projections) from the same query method by specifying the desired projection type at runtime. This is particularly useful when you want to reuse the same repository method to fetch different subsets of data.

Dynamic projections are achieved by using **generics in query methods** and passing the desired projection type as a parameter (`Class<T>`). This approach works with both **interface-based projections** and **Java Records**.

---

### **How Dynamic Projection Works**

1. **Generic Return Type**:
   - The repository method uses a generic return type (`<T>`), allowing it to return different types of projections.

2. **Class Parameter**:
   - A `Class<T>` parameter is added to the method, which specifies the desired projection type at runtime.

3. **Spring Data JPA**:
   - Spring Data JPA automatically maps the query result to the specified projection type.

---

### **Advantages of Dynamic Projection**

1. **Reusability**:
   - A single repository method can serve multiple projection types, reducing code duplication.

2. **Flexibility**:
   - You can dynamically choose the projection type based on the use case.

3. **Performance**:
   - Only the required fields are fetched, improving query efficiency.

4. **Support for Java Records**:
   - Dynamic projections work seamlessly with both **interface-based projections** and **Java Records**.

---

### **Examples of Dynamic Projection**

#### **1. Entity Class**

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private Double price;

    // Getters and Setters
}
```

---

#### **2. Projections**

##### **Interface-Based Projections**
```java
public interface SimpleProduct {
    Long getId();
    String getName();
}

public interface ProductPrice {
    Long getId();
    Double getPrice();
}
```

##### **Java Record-Based Projections**
```java
public record SimpleProductRecord(Long id, String name) {}

public record ProductPriceRecord(Long id, Double price) {}
```

---

#### **3. Repository with Dynamic Projection**

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    <T> List<T> findAllByNameLike(String name, Class<T> type);
}
```

---

#### **4. Service Layer**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<SimpleProduct> getSimpleProducts(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", SimpleProduct.class);
    }

    public List<ProductPrice> getProductPrices(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", ProductPrice.class);
    }

    public List<SimpleProductRecord> getSimpleProductRecords(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", SimpleProductRecord.class);
    }

    public List<ProductPriceRecord> getProductPriceRecords(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", ProductPriceRecord.class);
    }
}
```

---

#### **5. Controller**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
public class ProductController {

    @Autowired
    private ProductService productService;

    @GetMapping("/simple-products")
    public List<SimpleProduct> getSimpleProducts(@RequestParam String name) {
        return productService.getSimpleProducts(name);
    }

    @GetMapping("/product-prices")
    public List<ProductPrice> getProductPrices(@RequestParam String name) {
        return productService.getProductPrices(name);
    }

    @GetMapping("/simple-product-records")
    public List<SimpleProductRecord> getSimpleProductRecords(@RequestParam String name) {
        return productService.getSimpleProductRecords(name);
    }

    @GetMapping("/product-price-records")
    public List<ProductPriceRecord> getProductPriceRecords(@RequestParam String name) {
        return productService.getProductPriceRecords(name);
    }
}
```

---

#### **6. Testing Dynamic Projections**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;

    @Test
    void testDynamicProjections() {
        // Test Interface-Based Projection
        List<SimpleProduct> simpleProducts = productRepository.findAllByNameLike("%Apple%", SimpleProduct.class);
        assertEquals(2, simpleProducts.size());

        List<ProductPrice> productPrices = productRepository.findAllByNameLike("%Apple%", ProductPrice.class);
        assertEquals(2, productPrices.size());

        // Test Java Record-Based Projection
        List<SimpleProductRecord> simpleProductRecords = productRepository.findAllByNameLike("%Apple%", SimpleProductRecord.class);
        assertEquals(2, simpleProductRecords.size());

        List<ProductPriceRecord> productPriceRecords = productRepository.findAllByNameLike("%Apple%", ProductPriceRecord.class);
        assertEquals(2, productPriceRecords.size());
    }
}
```

---

### **How Dynamic Projection Works Internally**

1. **Query Execution**:
   - Spring Data JPA executes the query and fetches the result set.

2. **Mapping Results**:
   - Depending on the `Class<T>` parameter provided, Spring maps the result set to the specified projection type:
     - **For Interface-Based Projections**: A proxy object is created to implement the interface.
     - **For Java Records**: The record's constructor is used to create instances.

3. **Return Results**:
   - The mapped results are returned to the caller.

---

### **Use Cases for Dynamic Projection**

1. **API Responses**:
   - Serve different levels of detail (e.g., summary vs. detailed views) using the same query.

2. **Performance Optimization**:
   - Fetch only the required fields for specific use cases.

3. **Reusability**:
   - Avoid duplicating query methods for different projections.

4. **Simplified Code**:
   - Reduce boilerplate by consolidating query logic into a single method.

---

### **Comparison: Static vs. Dynamic Projections**

| Feature                        | Static Projection                 | Dynamic Projection                |
|--------------------------------|------------------------------------|-----------------------------------|
| **Flexibility**                | Limited to one projection type    | Supports multiple projection types |
| **Reusability**                | Requires separate methods         | Single method for all projections |
| **Performance**                | Same as dynamic                   | Same as static                    |
| **Implementation Complexity**  | Simple                            | Slightly more complex             |

