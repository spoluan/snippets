### **Using JDBC for Database Management in PostgreSQL with Spring Boot**

Yes, you can absolutely use **JDBC (Java Database Connectivity)** to manage a PostgreSQL database in a Spring Boot application. While Spring Boot provides higher-level abstractions like JPA (Java Persistence API) and Spring Data, JDBC is still widely used for direct database access when you need fine-grained control or want to avoid the overhead of ORM frameworks.

---

### **Introduction to JDBC in Spring Boot**

**JDBC (Java Database Connectivity)** is a low-level API in Java that allows you to interact with relational databases. In Spring Boot, you can use the `JdbcTemplate` class, which simplifies working with JDBC by handling boilerplate code such as establishing connections, executing queries, and closing resources.

#### Why Use JDBC in Spring Boot?
1. **Fine-grained Control:**  
   You have complete control over SQL queries and database interactions.
2. **Lightweight:**  
   No need for ORM frameworks like Hibernate.
3. **Flexibility:**  
   You can write custom SQL queries tailored to your requirements.

---

### **Setting Up JDBC with PostgreSQL in Spring Boot**

#### **1. Add Dependencies**
Add the following dependencies to your `pom.xml` file for Maven:

```xml
<dependencies>
    <!-- Spring Boot Starter for JDBC -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    
    <!-- PostgreSQL Driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

For Gradle, use:
```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-jdbc'
    runtimeOnly 'org.postgresql:postgresql'
}
```

---

#### **2. Configure Database Connection**
Add the PostgreSQL database configuration to your `application.properties` or `application.yml` file:

**`application.properties`**
```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/your_database_name
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=org.postgresql.Driver
```

**`application.yml`**
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/your_database_name
    username: your_username
    password: your_password
    driver-class-name: org.postgresql.Driver
```

---

#### **3. Using `JdbcTemplate` in Spring Boot**

Spring Boot automatically configures a `JdbcTemplate` bean if you include the JDBC starter dependency. You can use it to execute SQL queries.

---

### **Examples of Using JDBC with PostgreSQL in Spring Boot**

#### **1. Basic CRUD Operations with `JdbcTemplate`**

##### Example: Create a Repository Class
```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

@Repository
public class UserRepository {

    private final JdbcTemplate jdbcTemplate;

    public UserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    // Create a new user
    public void createUser(String name, String email) {
        String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
        jdbcTemplate.update(sql, name, email);
    }

    // Read all users
    public List<User> getAllUsers() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, new UserRowMapper());
    }

    // Read a user by ID
    public User getUserById(int id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new UserRowMapper(), id);
    }

    // Update a user
    public void updateUser(int id, String name, String email) {
        String sql = "UPDATE users SET name = ?, email = ? WHERE id = ?";
        jdbcTemplate.update(sql, name, email, id);
    }

    // Delete a user
    public void deleteUser(int id) {
        String sql = "DELETE FROM users WHERE id = ?";
        jdbcTemplate.update(sql, id);
    }

    // RowMapper for mapping SQL rows to User objects
    private static class UserRowMapper implements RowMapper<User> {
        @Override
        public User mapRow(ResultSet rs, int rowNum) throws SQLException {
            User user = new User();
            user.setId(rs.getInt("id"));
            user.setName(rs.getString("name"));
            user.setEmail(rs.getString("email"));
            return user;
        }
    }
}
```

##### Example: User Model
```java
public class User {
    private int id;
    private String name;
    private String email;

    // Getters and Setters
    public int getId() {
        return id;
    }
    public void setId(int id) {
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

##### Example: Using the Repository in a Service or Controller
```java
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserRepository userRepository;

    public UserController(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @PostMapping
    public String createUser(@RequestParam String name, @RequestParam String email) {
        userRepository.createUser(name, email);
        return "User created successfully!";
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userRepository.getAllUsers();
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable int id) {
        return userRepository.getUserById(id);
    }

    @PutMapping("/{id}")
    public String updateUser(@PathVariable int id, @RequestParam String name, @RequestParam String email) {
        userRepository.updateUser(id, name, email);
        return "User updated successfully!";
    }

    @DeleteMapping("/{id}")
    public String deleteUser(@PathVariable int id) {
        userRepository.deleteUser(id);
        return "User deleted successfully!";
    }
}
```

---

#### **2. Executing Custom Queries**

You can execute custom queries using `JdbcTemplate`.

##### Example: Count Rows in a Table
```java
public int countUsers() {
    String sql = "SELECT COUNT(*) FROM users";
    return jdbcTemplate.queryForObject(sql, Integer.class);
}
```

##### Example: Fetch Specific Columns
```java
public List<String> getAllEmails() {
    String sql = "SELECT email FROM users";
    return jdbcTemplate.queryForList(sql, String.class);
}
```

---

#### **3. Batch Updates (Insert Multiple Records)**
If you want to insert multiple rows in one go, you can use `batchUpdate`.

```java
public void batchInsertUsers(List<User> users) {
    String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
    jdbcTemplate.batchUpdate(sql, users, users.size(), (ps, user) -> {
        ps.setString(1, user.getName());
        ps.setString(2, user.getEmail());
    });
}
```

---

### **Advantages of Using JDBC with Spring Boot**
1. **Direct SQL Access:**  
   You can write raw SQL queries without the abstraction of ORM.
2. **Faster Performance:**  
   Avoids the overhead of ORM frameworks.
3. **Custom Query Support:**  
   Perfect for complex queries that are hard to achieve with JPA.

---

### **When to Use JDBC Instead of JPA?**
- When you need **fine-grained control** over SQL queries.
- When performance is critical, and you want to avoid the overhead of ORM.
- When working with **legacy databases** or databases that don’t fit well with JPA’s abstraction.
