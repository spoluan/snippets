### **ORM in Spring Boot**

---

### **What is ORM?**
ORM (**Object-Relational Mapping**) is a programming technique used to map Java objects to database tables and vice versa. It abstracts the database interactions by allowing developers to write database queries using Java objects instead of SQL queries.

In Spring Boot, ORM is implemented using frameworks like **Hibernate**, **JPA (Java Persistence API)**, or other ORM tools. Spring Boot provides excellent integration with JPA and Hibernate out of the box.

---

### **Key Features of ORM in Spring Boot**
1. **Abstraction of SQL**: Developers interact with the database using Java objects and methods instead of writing raw SQL.
2. **Entity Management**: ORM frameworks automatically manage object states (e.g., persist, merge, remove).
3. **Relationships**: ORM supports relationships between entities (e.g., `One-to-One`, `One-to-Many`, `Many-to-Many`).
4. **Automatic Table Creation**: Spring Boot ORM can automatically create database tables based on entity classes.
5. **Query Methods**: Supports JPQL (Java Persistence Query Language), Criteria API, and Native SQL.

---

### **Spring Boot ORM Frameworks**
1. **JPA (Java Persistence API)**: A specification for ORM.
2. **Hibernate**: The most popular implementation of JPA.
3. **Spring Data JPA**: A Spring-based abstraction over JPA for easier database interaction.

---

### **Key Annotations in ORM**
| Annotation        | Description                                                                 |
|-------------------|-----------------------------------------------------------------------------|
| `@Entity`         | Marks a class as a database entity (table).                                |
| `@Table`          | Specifies the table name for the entity.                                   |
| `@Id`             | Marks a field as the primary key.                                          |
| `@GeneratedValue` | Specifies how the primary key is generated (e.g., AUTO, IDENTITY, SEQUENCE).|
| `@Column`         | Maps a field to a specific column in the table.                            |
| `@OneToOne`       | Defines a one-to-one relationship between entities.                        |
| `@OneToMany`      | Defines a one-to-many relationship between entities.                       |
| `@ManyToOne`      | Defines a many-to-one relationship between entities.                       |
| `@ManyToMany`     | Defines a many-to-many relationship between entities.                      |
| `@JoinColumn`     | Specifies the foreign key column for a relationship.                      |
| `@Transient`      | Marks a field that should not be persisted in the database.                |

---

### **Examples of ORM in Spring Boot**

#### **1. Basic JPA Example**
This example demonstrates a simple entity and repository using Spring Data JPA.

**Entity Class: `User`**
```java
import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false, unique = true)
    private String email;

    // Getters and Setters
    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

**Repository Interface: `UserRepository`**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Custom query method
    User findByEmail(String email);
}
```

**Service Class: `UserService`**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    public User createUser(User user) {
        return userRepository.save(user);
    }

    public User getUserByEmail(String email) {
        return userRepository.findByEmail(email);
    }
}
```

**Controller Class: `UserController`**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @GetMapping("/{email}")
    public User getUserByEmail(@PathVariable String email) {
        return userService.getUserByEmail(email);
    }
}
```

---

#### **2. Relationships Example**

**Entities:**

- **User Entity**
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL)
    private List<Order> orders;

    // Getters and Setters
}
```

- **Order Entity**
```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String product;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    // Getters and Setters
}
```

---

#### **3. Native SQL Example**

**Repository Interface Using Native SQL**
```java
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;

public interface UserRepository extends JpaRepository<User, Long> {

    @Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)
    User findUserByEmail(@Param("email") String email);
}
```

---

#### **4. JPQL Example**

**Repository Interface Using JPQL**
```java
import org.springframework.data.jpa.repository.Query;

public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT u FROM User u WHERE u.email = ?1")
    User findUserByEmail(String email);
}
```

---

#### **5. Pagination and Sorting**

**Pagination Example**
```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;

public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findAll(Pageable pageable);
}
```

**Usage in Service**
```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;

Page<User> usersPage = userRepository.findAll(PageRequest.of(0, 10));
```

---

#### **6. CRUD Operations**

- **Create**:
  ```java
  User user = new User();
  user.setName("John Doe");
  user.setEmail("john.doe@example.com");
  userRepository.save(user);
  ```

- **Read**:
  ```java
  User user = userRepository.findById(1L).orElse(null);
  ```

- **Update**:
  ```java
  User user = userRepository.findById(1L).orElse(null);
  if (user != null) {
      user.setName("Updated Name");
      userRepository.save(user);
  }
  ```

- **Delete**:
  ```java
  userRepository.deleteById(1L);
  ```

---

### **7. Configuration for ORM in Spring Boot**

**Dependencies (`pom.xml`):**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Application Properties:**
```properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

### **8. Advanced ORM Features**
- **Caching**: Use Hibernate's second-level cache for better performance.
- **Custom Queries**: Write complex queries using JPQL, Criteria API, or Native SQL.
- **Auditing**: Use Spring Data JPA auditing to track creation and modification timestamps.
- **Transaction Management**: Use `@Transactional` for managing database transactions.
