### **Configuring MySQL Database Connection in Spring Boot**

---

### **1. Overview**

Spring Boot simplifies database configuration by providing built-in support for popular databases like MySQL. By adding the necessary dependencies and specifying configuration properties in the `application.properties` or `application.yml` file, you can establish a connection to a MySQL database.

---

### **2. Required Dependencies**

To connect to MySQL, you need to include the **MySQL JDBC Driver** and optionally a connection pool library like **HikariCP** (default in Spring Boot).

**Maven Dependency**:
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Gradle Dependency**:
```gradle
implementation 'mysql:mysql-connector-java'
```

---

### **3. Configuration Properties**

You need to configure the database connection in the `application.properties` or `application.yml` file. Below is an example for **MySQL** with **HikariCP** connection pooling.

#### **Example: `application.properties`**
```properties
# MySQL Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/belajar_spring_restful_api
spring.datasource.username=root
spring.datasource.password=

# JDBC Driver
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# Connection Pooling using HikariCP
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.minimum-idle=10
spring.datasource.hikari.maximum-pool-size=50
```

#### **Explanation of Properties**:
1. **`spring.datasource.url`**:  
   Specifies the JDBC URL for the database.  
   Format: `jdbc:mysql://<host>:<port>/<database_name>`

2. **`spring.datasource.username`**:  
   The username to connect to the database.

3. **`spring.datasource.password`**:  
   The password to connect to the database.

4. **`spring.datasource.driver-class-name`**:  
   The fully qualified class name of the JDBC driver (`com.mysql.cj.jdbc.Driver`).

5. **`spring.datasource.type`**:  
   Specifies the connection pool type. Spring Boot uses **HikariCP** as the default connection pool.

6. **`spring.datasource.hikari.minimum-idle`**:  
   The minimum number of idle connections maintained in the connection pool.

7. **`spring.datasource.hikari.maximum-pool-size`**:  
   The maximum number of connections allowed in the connection pool.

---

### **4. Testing the Database Connection**

To verify that the configuration works, you can create a simple repository or service to interact with the database.

#### **Example: Simple JPA Entity**
```java
import jakarta.persistence.Entity;
import jakarta.persistence.Id;

@Entity
public class User {
    @Id
    private Long id;
    private String name;
    private String email;

    // Getters and Setters
}
```

#### **Example: Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserRepository extends JpaRepository<User, Long> {
}
```

#### **Example: Test the Connection**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class DatabaseTestRunner implements CommandLineRunner {

    @Autowired
    private UserRepository userRepository;

    @Override
    public void run(String... args) throws Exception {
        // Save a new user
        User user = new User();
        user.setId(1L);
        user.setName("John Doe");
        user.setEmail("john.doe@example.com");
        userRepository.save(user);

        // Fetch all users
        userRepository.findAll().forEach(System.out::println);
    }
}
```

---

### **5. Connection Pooling with HikariCP**

HikariCP is the default connection pool in Spring Boot. It is fast, lightweight, and highly configurable.

#### **Key HikariCP Properties**:
- **`spring.datasource.hikari.minimum-idle`**: Minimum number of idle connections in the pool.
- **`spring.datasource.hikari.maximum-pool-size`**: Maximum number of connections in the pool.
- **`spring.datasource.hikari.idle-timeout`**: Maximum idle time for a connection in milliseconds.
- **`spring.datasource.hikari.connection-timeout`**: Maximum time to wait for a connection in milliseconds.

---

### **6. Common Issues and Solutions**

1. **Missing MySQL Driver**:  
   - Error: `No suitable driver found for jdbc:mysql://localhost:3306/...`  
   - Solution: Add the MySQL dependency in your project.

2. **Incorrect Database URL**:  
   - Error: `Communications link failure`  
   - Solution: Ensure the database URL is correct and the MySQL server is running.

3. **Authentication Issues**:  
   - Error: `Access denied for user 'root'@'localhost'`  
   - Solution: Verify the username, password, and database permissions.

4. **Connection Pool Exhaustion**:  
   - Error: `HikariPool-1 - Connection is not available, request timed out`  
   - Solution: Increase the `maximum-pool-size` or optimize query performance.
