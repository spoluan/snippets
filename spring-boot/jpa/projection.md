### **Projection in JPA**

Projection in JPA is a mechanism that allows you to **retrieve only specific fields** from the database instead of fetching the entire entity. This is useful when you don’t need all the fields of an entity, as it improves performance by reducing the amount of data transferred and processed.

Spring Data JPA provides a **built-in feature for projections**, allowing you to return custom objects or interfaces directly from your query methods.

---

### **Types of Projections in JPA**

1. **Interface-Based Projection**: 
   - Uses Java interfaces to define the structure of the result.
   - Spring Data JPA automatically maps the query result to the interface.

2. **Class-Based Projection (DTO Projection)**:
   - Uses a custom class (DTO) to hold the query result.
   - Requires a JPQL query to map the result to the DTO constructor.

3. **Dynamic Projection**:
   - Allows you to dynamically choose the projection type at runtime.

---

### **1. Interface-Based Projection**

This is the simplest and most commonly used type of projection in Spring Data JPA.

#### **Steps to Implement:**

1. Define an interface with getter methods matching the fields you want to retrieve.
2. Use this interface as the return type in your repository query method.

#### **Example**

##### **Entity**
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

##### **Projection Interface**
```java
public interface SimpleProduct {
    Long getId();
    String getName();
}
```

##### **Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<SimpleProduct> findAllByNameLike(String name);
}
```

##### **Usage**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<SimpleProduct> getProductsByName(String name) {
        return productRepository.findAllByNameLike("%" + name + "%");
    }
}
```

##### **Test**
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
    void testProjection() {
        List<SimpleProduct> simpleProducts = productRepository.findAllByNameLike("%Apple%");
        assertEquals(2, simpleProducts.size());
    }
}
```

---

### **2. Class-Based Projection (DTO Projection)**

In this approach, the query result is mapped to a custom DTO class using JPQL or native queries.

#### **Steps to Implement:**

1. Create a DTO class with a constructor matching the fields you want to retrieve.
2. Use a JPQL or native query to map the result to the DTO.

#### **Example**

##### **DTO Class**
```java
public class ProductDTO {
    private Long id;
    private String name;

    public ProductDTO(Long id, String name) {
        this.id = id;
        this.name = name;
    }

    // Getters and Setters
}
```

##### **Repository**
```java
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Query("SELECT new com.example.demo.ProductDTO(p.id, p.name) FROM Product p WHERE p.name LIKE ?1")
    List<ProductDTO> findAllByNameLike(String name);
}
```

##### **Usage**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<ProductDTO> getProductsByName(String name) {
        return productRepository.findAllByNameLike("%" + name + "%");
    }
}
```

---

### **3. Dynamic Projection**

Dynamic projection allows you to specify the projection type at runtime. This is useful when you have multiple projections for the same query.

#### **Steps to Implement:**

1. Add a generic parameter to your repository method for the projection type.
2. Pass the desired projection interface or DTO class at runtime.

#### **Example**

##### **Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    <T> List<T> findAllByNameLike(String name, Class<T> type);
}
```

##### **Usage**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<SimpleProduct> getSimpleProductsByName(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", SimpleProduct.class);
    }

    public List<ProductDTO> getProductDTOsByName(String name) {
        return productRepository.findAllByNameLike("%" + name + "%", ProductDTO.class);
    }
}
```

---

### **4. Native Query with Projection**

You can also use native SQL queries with projections. For interface-based projections, Spring Data automatically maps the result to the interface.

#### **Example**

##### **Repository**
```java
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Query(value = "SELECT id, name FROM product WHERE name LIKE :name", nativeQuery = true)
    List<SimpleProduct> findByNativeQuery(String name);
}
```

---

### **Use Cases for Projection**

1. **Performance Optimization**:
   - Reduce the amount of data fetched from the database by retrieving only the required fields.
   
2. **Custom API Responses**:
   - Return lightweight DTOs or interfaces for REST API responses.

3. **Dynamic Field Selection**:
   - Use dynamic projections to fetch different sets of fields based on requirements.

4. **Integration with Frontend**:
   - Simplify the data structure sent to the frontend by mapping entities to DTOs.

---

### **Comparison of Projections**

| Feature                        | Interface-Based Projection         | Class-Based Projection            | Dynamic Projection              |
|--------------------------------|------------------------------------|-----------------------------------|---------------------------------|
| **Ease of Use**                | Very simple                       | Requires DTO class and JPQL query | Flexible but more complex       |
| **Performance**                | Efficient                         | Efficient                         | Efficient                       |
| **Mapping**                    | Automatic                         | Manual (constructor-based)        | Automatic                       |
| **Dynamic Field Selection**    | No                                | No                                | Yes                             |
| **Use Case**                   | Simple queries                    | Custom DTOs                       | Multiple projections per query  |

---

### **Advantages of Projection**

1. **Improved Performance**:
   - Fetching only required fields reduces the amount of data transferred and processed.

2. **Simplified Data Structures**:
   - Avoid exposing entire entities in API responses.

3. **Flexibility**:
   - Use projections to customize the query result based on requirements.

4. **Separation of Concerns**:
   - Keeps entity classes separate from API response classes (DTOs).

---

### **Limitations of Projection**

1. **Limited to Read-Only**:
   - Projections are typically used for read-only operations and cannot be used for updates.

2. **Complex Queries**:
   - For very complex queries, creating and maintaining projections can become cumbersome.

3. **Dynamic Projection Complexity**:
   - Dynamic projections require additional boilerplate code and careful management.

---

### **Summary**

Projection in JPA is a powerful feature for optimizing query performance and customizing query results. By using **interface-based projections**, **class-based projections (DTOs)**, or **dynamic projections**, you can fetch only the required fields and reduce unnecessary data processing.

- Use **interface-based projections** for simple and automatic mapping.
- Use **class-based projections** for custom DTOs and complex mappings.
- Use **dynamic projections** for flexible, runtime field selection.
