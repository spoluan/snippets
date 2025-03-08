### **Locking in JPA**

Locking in JPA is a mechanism to handle **concurrent access** to database records. It ensures data consistency and integrity when multiple transactions or threads attempt to update or read the same data simultaneously. JPA provides two main types of locking:
1. **Optimistic Locking**
2. **Pessimistic Locking**

---

### **1. Optimistic Locking**

Optimistic locking assumes that most transactions will not conflict and allows multiple transactions to proceed without locking the data. It uses a **version field** (or timestamp) to detect conflicts during updates.

#### **How It Works**
1. A version field is added to the entity (e.g., `@Version`).
2. When a transaction reads the data, it includes the version number.
3. During an update, the version number is checked. If the version has changed, a conflict is detected, and an exception is thrown.

#### **Example: Optimistic Locking**

##### **Entity with Version Field**
```java
import jakarta.persistence.*;

@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private Double price;

    @Version
    private Integer version; // Used for optimistic locking

    // Getters and Setters
}
```

##### **Repository**
```java
@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {
}
```

##### **Service**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Transactional
    public void updateProduct(Long productId, Double newPrice) {
        Product product = productRepository.findById(productId)
                .orElseThrow(() -> new RuntimeException("Product not found"));

        product.setPrice(newPrice);

        productRepository.save(product); // Will check the version field
    }
}
```

##### **Conflict Detection**
If another transaction updates the product before the current one, a `OptimisticLockException` will be thrown.

---

### **2. Pessimistic Locking**

Pessimistic locking assumes that conflicts are likely and locks the data to prevent other transactions from accessing it. It uses database-level locks to ensure that only one transaction can access the data at a time.

#### **Types of Pessimistic Locks**
- **PESSIMISTIC_READ:** Prevents other transactions from writing to the locked data but allows reading.
- **PESSIMISTIC_WRITE:** Prevents other transactions from reading or writing to the locked data.
- **PESSIMISTIC_FORCE_INCREMENT:** Similar to `PESSIMISTIC_WRITE` but also increments the version field.

#### **How to Use Pessimistic Locking in Spring Data JPA**

Spring Data JPA provides the `@Lock` annotation to apply pessimistic locks to query methods.

---

#### **Example: Pessimistic Locking**

##### **Entity**
```java
import jakarta.persistence.*;

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

##### **Repository with `@Lock`**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Lock;
import jakarta.persistence.LockModeType;

import java.util.Optional;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE) // Apply pessimistic write lock
    Optional<Product> findFirstByIdEquals(Long id);
}
```

##### **Service**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    @Transactional
    public void updateProductWithLock(Long productId, Double newPrice) {
        Product product = productRepository.findFirstByIdEquals(productId)
                .orElseThrow(() -> new RuntimeException("Product not found"));

        product.setPrice(newPrice);

        productRepository.save(product);
    }
}
```

---

#### **Testing Pessimistic Locking**

##### **Test Case**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.support.TransactionTemplate;

@SpringBootTest
public class ProductRepositoryTest {

    @Autowired
    private ProductRepository productRepository;

    @Autowired
    private TransactionTemplate transactionTemplate;

    @Test
    void testPessimisticLocking() {
        transactionTemplate.executeWithoutResult(transactionStatus -> {
            Product product = productRepository.findFirstByIdEquals(1L)
                    .orElseThrow(() -> new RuntimeException("Product not found"));

            product.setPrice(500.0);

            try {
                Thread.sleep(30000); // Simulate long-running transaction
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }

            productRepository.save(product);
        });

        transactionTemplate.executeWithoutResult(transactionStatus -> {
            Product product = productRepository.findFirstByIdEquals(1L)
                    .orElseThrow(() -> new RuntimeException("Product not found"));

            product.setPrice(1000.0);

            productRepository.save(product);
        });
    }
}
```

##### **Explanation**
- The first transaction locks the product with `PESSIMISTIC_WRITE` and simulates a delay.
- The second transaction tries to access the same product but is blocked until the first transaction completes.

---

### **3. Locking in Native Queries**

You can also apply locks in native queries using the `@Query` annotation.

#### **Example**
```java
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.jpa.repository.Lock;
import jakarta.persistence.LockModeType;

@Repository
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT p FROM Product p WHERE p.id = :id")
    Product findProductWithLock(Long id);
}
```

---

### **4. Lock Timeout**

You can configure a lock timeout to avoid indefinite waiting for a lock. This is database-specific.

#### **Example for MySQL**
```properties
spring.jpa.properties.hibernate.jdbc.timeout=5
```

---

### **5. Differences Between Optimistic and Pessimistic Locking**

| Feature                     | Optimistic Locking                     | Pessimistic Locking                     |
|-----------------------------|-----------------------------------------|-----------------------------------------|
| **Conflict Detection**      | At commit time (version mismatch).     | At query time (database lock).          |
| **Performance**             | Better for low contention scenarios.   | Better for high contention scenarios.   |
| **Lock Type**               | Logical lock using a version field.    | Physical lock using database mechanisms.|
| **Use Case**                | Suitable for read-heavy applications.  | Suitable for write-heavy applications.  |

---

### **6. Real-World Use Cases**

#### **Optimistic Locking**
- **E-commerce:** Prevent overselling when multiple users try to purchase the same product.
- **Banking:** Prevent double withdrawal of funds.

#### **Pessimistic Locking**
- **Inventory Management:** Ensure no two transactions update stock levels simultaneously.
- **Ticket Booking:** Prevent double booking of the same seat.

---

### **7. Common Exceptions**

| Exception                     | Description                                                   |
|-------------------------------|---------------------------------------------------------------|
| `OptimisticLockException`     | Thrown when a version mismatch is detected in optimistic locking. |
| `PessimisticLockException`    | Thrown when a pessimistic lock cannot be acquired.            |
| `LockTimeoutException`        | Thrown when a lock cannot be acquired within the timeout period. |

---

### **Summary**

- **Optimistic Locking** is lightweight and suitable for scenarios with low contention.
- **Pessimistic Locking** is heavier but ensures stricter control over concurrent access.
- Spring Data JPA makes it easy to implement both types of locking using annotations like `@Version` (for optimistic locking) and `@Lock` (for pessimistic locking).
- Choose the locking mechanism based on your application's concurrency requirements and performance considerations.
