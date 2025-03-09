### **Introduction to DAO in Spring Boot**

The **DAO (Data Access Object)** pattern is a structural pattern that provides an abstract interface to some type of database or other persistence mechanism. By using DAOs, you can separate the persistence logic (database operations) from the business logic. This makes your code cleaner, more modular, and easier to test.

In a Spring Boot application, DAOs are typically implemented using tools like **JDBC**, **JPA**, or **Spring Data**. However, the DAO concept is generic and can be implemented with any persistence technology.

---

### **Why Use DAO in Spring Boot?**

- **Encapsulation of Persistence Logic:**  
  All database-related operations are encapsulated in a single layer, making it easier to manage.
- **Separation of Concerns:**  
  It separates the persistence layer from the service/business layer.
- **Reusability:**  
  DAOs can be reused across multiple services.
- **Testability:**  
  DAOs can be easily mocked or tested independently.

---

### **Basic Structure of a DAO Layer**

1. **Model (Entity):** Represents data in the database.
2. **DAO Interface:** Defines the operations (e.g., CRUD methods).
3. **DAO Implementation:** Implements the DAO interface.
4. **Service Layer:** Calls the DAO methods to perform business logic.

---

### **Implementing DAO in Spring Boot**

#### **1. Using DAO with JDBC**

##### Example: User Entity
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

##### Example: DAO Interface
```java
import java.util.List;

public interface UserDao {
    void createUser(User user);
    User getUserById(int id);
    List<User> getAllUsers();
    void updateUser(User user);
    void deleteUser(int id);
}
```

##### Example: DAO Implementation Using `JdbcTemplate`
```java
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

@Repository
public class UserDaoImpl implements UserDao {

    private final JdbcTemplate jdbcTemplate;

    public UserDaoImpl(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public void createUser(User user) {
        String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
        jdbcTemplate.update(sql, user.getName(), user.getEmail());
    }

    @Override
    public User getUserById(int id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new UserRowMapper(), id);
    }

    @Override
    public List<User> getAllUsers() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, new UserRowMapper());
    }

    @Override
    public void updateUser(User user) {
        String sql = "UPDATE users SET name = ?, email = ? WHERE id = ?";
        jdbcTemplate.update(sql, user.getName(), user.getEmail(), user.getId());
    }

    @Override
    public void deleteUser(int id) {
        String sql = "DELETE FROM users WHERE id = ?";
        jdbcTemplate.update(sql, id);
    }

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

##### Example: Service Layer
```java
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserDao userDao;

    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }

    public void createUser(User user) {
        userDao.createUser(user);
    }

    public User getUserById(int id) {
        return userDao.getUserById(id);
    }

    public List<User> getAllUsers() {
        return userDao.getAllUsers();
    }

    public void updateUser(User user) {
        userDao.updateUser(user);
    }

    public void deleteUser(int id) {
        userDao.deleteUser(id);
    }
}
```

##### Example: Controller Layer
```java
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public String createUser(@RequestBody User user) {
        userService.createUser(user);
        return "User created successfully!";
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable int id) {
        return userService.getUserById(id);
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PutMapping("/{id}")
    public String updateUser(@PathVariable int id, @RequestBody User user) {
        user.setId(id);
        userService.updateUser(user);
        return "User updated successfully!";
    }

    @DeleteMapping("/{id}")
    public String deleteUser(@PathVariable int id) {
        userService.deleteUser(id);
        return "User deleted successfully!";
    }
}
```

---

#### **2. Using DAO with JPA**

If you prefer using JPA, Spring Data JPA can simplify DAO implementation.

##### Example: User Entity
```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    private String email;

    // Getters and Setters
}
```

##### Example: DAO Interface
```java
import org.springframework.data.jpa.repository.JpaRepository;

public interface UserDao extends JpaRepository<User, Integer> {
    // Spring Data JPA provides built-in CRUD methods
}
```

##### Example: Service Layer
```java
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserDao userDao;

    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }

    public User createUser(User user) {
        return userDao.save(user);
    }

    public User getUserById(int id) {
        return userDao.findById(id).orElse(null);
    }

    public List<User> getAllUsers() {
        return userDao.findAll();
    }

    public User updateUser(User user) {
        return userDao.save(user);
    }

    public void deleteUser(int id) {
        userDao.deleteById(id);
    }
}
```

##### Example: Controller Layer
```java
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.createUser(user);
    }

    @GetMapping("/{id}")
    public User getUserById(@PathVariable int id) {
        return userService.getUserById(id);
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }

    @PutMapping("/{id}")
    public User updateUser(@PathVariable int id, @RequestBody User user) {
        user.setId(id);
        return userService.updateUser(user);
    }

    @DeleteMapping("/{id}")
    public String deleteUser(@PathVariable int id) {
        userService.deleteUser(id);
        return "User deleted successfully!";
    }
}
```

---

### **Key Points About DAO in Spring Boot**
1. **DAO with JDBC:**  
   Use `JdbcTemplate` for low-level SQL operations.
2. **DAO with JPA:**  
   Use Spring Data JPA for higher-level abstraction and built-in CRUD methods.
3. **Service Layer:**  
   The service layer interacts with the DAO and contains business logic.
4. **Controller Layer:**  
   The controller layer handles HTTP requests and delegates tasks to the service layer.

---

### **When to Use DAO?**
- Use DAO when you want to **encapsulate database logic** in a reusable and testable way.
- It’s especially useful in **large applications** where separating concerns is critical.
- DAO is ideal for applications needing **flexibility** to switch between different persistence mechanisms in the future.
