### **Java Record in JPA**

Java Records, introduced in **Java 14 (Preview)** and made stable in **Java 16**, are a special kind of class that act as **immutable data carriers**. They are a concise way to model data and are particularly useful for projections in JPA.

When using **Java 17 or later**, you can leverage **Java Records** for projections in Spring Data JPA. Compared to interface-based projections, records provide a more **natural and concise way** to represent query results, with the added benefit of immutability.

---

### **Why Use Java Records for JPA Projections?**

1. **Automatic Instance Creation**:
   - With Java Records, Spring Data JPA automatically maps query results to the record fields without requiring proxies or reflection.

2. **Simpler Syntax**:
   - Records reduce boilerplate code by automatically generating constructors, getters, `equals()`, `hashCode()`, and `toString()` methods.

3. **Immutability**:
   - Records are immutable by design, making them a safer choice for read-only projections.

4. **Performance**:
   - Unlike interface-based projections, which rely on proxies (reflection), records are instantiated directly, improving performance.

---

### **How to Use Java Records in JPA**

#### **Steps to Implement:**

1. Define a record with fields matching the columns you want to retrieve.
2. Use the record as the return type in your repository query methods.

---

### **Examples of Java Records in JPA**

#### **1. Basic Example**

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

##### **Projection Using Java Record**
```java
public record SimpleProduct(Long id, String name) {}
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
    void testProjectionWithRecord() {
        List<SimpleProduct> simpleProducts = productRepository.findAllByNameLike("%Apple%");
        assertEquals(2, simpleProducts.size());
    }
}
```

---

#### **2. Projection with Multiple Fields**

You can create records with multiple fields to retrieve more data.

##### **Projection Record**
```java
public record DetailedProduct(Long id, String name, Double price) {}
```

##### **Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<DetailedProduct> findAllByPriceGreaterThan(Double price);
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

    public List<DetailedProduct> getExpensiveProducts(Double price) {
        return productRepository.findAllByPriceGreaterThan(price);
    }
}
```

---

#### **3. Native Query with Java Record**

You can also use Java Records with **native SQL queries**.

##### **Projection Record**
```java
public record SimpleProduct(Long id, String name) {}
```

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

##### **Usage**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    public List<SimpleProduct> getProductsUsingNativeQuery(String name) {
        return productRepository.findByNativeQuery("%" + name + "%");
    }
}
```

---

#### **4. Dynamic Projection with Java Records**

Dynamic projection allows you to use Java Records alongside other projection types.

##### **Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
    <T> List<T> findAllByPriceGreaterThan(Double price, Class<T> type);
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

    public List<SimpleProduct> getSimpleProducts(Double price) {
        return productRepository.findAllByPriceGreaterThan(price, SimpleProduct.class);
    }

    public List<DetailedProduct> getDetailedProducts(Double price) {
        return productRepository.findAllByPriceGreaterThan(price, DetailedProduct.class);
    }
}
```

---

### **Advantages of Using Java Records in JPA**

1. **Concise Code**:
   - Records eliminate boilerplate code for getters, constructors, and `toString()`.

2. **Immutability**:
   - Records are immutable, making them ideal for read-only projections.

3. **Better Performance**:
   - Unlike interface-based projections, records are instantiated directly without relying on proxies or reflection.

4. **Type-Safety**:
   - Records ensure type safety by enforcing the structure of the query result.

5. **Compatibility**:
   - Records work seamlessly with both JPQL and native SQL queries in Spring Data JPA.

---

### **Limitations of Using Java Records**

1. **Requires Java 16+**:
   - Java Records are only available in Java 16 or later.

2. **Read-Only**:
   - Records are immutable, so they cannot be used if you need to modify the projection data.

3. **No Dynamic Field Selection**:
   - Records require predefined fields, so they are less flexible compared to dynamic projections.

---

### **Comparison: Interface-Based vs Java Record Projections**

| Feature                        | Interface-Based Projection         | Java Record Projection            |
|--------------------------------|------------------------------------|-----------------------------------|
| **Ease of Use**                | Simple                            | Even simpler                      |
| **Performance**                | Relies on proxy (reflection)      | Direct instantiation              |
| **Immutability**               | Not guaranteed                    | Guaranteed                        |
| **Boilerplate Code**           | Minimal                           | None                              |
| **Dynamic Field Selection**    | No                                | No                                |
| **Compatibility**              | Works with all Java versions      | Requires Java 16+                 |

---

### **When to Use Java Records in JPA**

- **Use Java Records** when:
  - You are using Java 16 or later.
  - You need immutable, concise, and type-safe projections.
  - You want better performance without relying on proxies.

- **Use Interface-Based Projections** when:
  - You are working with older Java versions.
  - You need dynamic projections.

