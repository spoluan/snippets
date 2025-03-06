### **Using `@Validated` with `@ConfigurationProperties` in Spring Boot**

In Spring Boot, the `@ConfigurationProperties` annotation is used to map external configuration properties (e.g., from `application.yml` or `application.properties`) to a Java object. When you combine it with `@Validated`, you can enforce validation rules on the properties being bound.

This approach ensures that the configuration properties satisfy the specified constraints before the application starts. If validation fails, the application will fail to start, ensuring invalid configurations are caught early.

---

### **Explanation of the Code**

#### **Code Breakdown**
```java
@Data
@NoArgsConstructor
@ConfigurationProperties("database")
@Validated
public class DatabaseProperties {

    @NotBlank
    private String username;

    @NotBlank
    private String password;
}
```

1. **`@ConfigurationProperties("database")`**:
   - Maps properties prefixed with `database` in the external configuration file to this class.
   - For example:
     ```yaml
     database:
       username: admin
       password: secret
     ```

2. **`@Validated`**:
   - Enables validation for the fields in the class using the Bean Validation API.
   - If any constraint (e.g., `@NotBlank`) is violated, Spring throws an exception during application startup.

3. **Validation Annotations**:
   - `@NotBlank`: Ensures the property is not null and not empty (after trimming whitespace).

4. **`@Data` and `@NoArgsConstructor`**:
   - Lombok annotations to generate boilerplate code like getters, setters, and a no-argument constructor.

---

### **How It Works**

1. During application startup, Spring binds the configuration properties (defined in `application.yml` or `application.properties`) to the `DatabaseProperties` class.
2. The `@Validated` annotation ensures that the constraints (like `@NotBlank`) are checked during this binding process.
3. If validation fails, Spring throws a `BindException` or `ConstraintViolationException`, and the application fails to start.

---

### **Example Configuration**

#### **application.yml**
```yaml
database:
  username: admin
  password: secret
```

#### **application.properties**
```properties
database.username=admin
database.password=secret
```

---

### **Example Usage**

You can inject the validated `DatabaseProperties` object into any Spring-managed component, like a service or a configuration class.

#### **Example Service**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class DatabaseService {

    private final DatabaseProperties databaseProperties;

    @Autowired
    public DatabaseService(DatabaseProperties databaseProperties) {
        this.databaseProperties = databaseProperties;
    }

    public void printDatabaseCredentials() {
        System.out.println("Username: " + databaseProperties.getUsername());
        System.out.println("Password: " + databaseProperties.getPassword());
    }
}
```

#### **Example Application**
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    CommandLineRunner commandLineRunner(DatabaseService databaseService) {
        return args -> {
            databaseService.printDatabaseCredentials();
        };
    }
}
```

---

### **Validation Failure Example**

#### **Invalid Configuration**
```yaml
database:
  username:   # Empty username
  password: secret
```

#### **Error During Startup**
If the `username` field is blank, Spring will throw an exception like this:
```
Caused by: org.springframework.boot.context.properties.bind.validation.BindValidationException: 
Binding validation errors on database
 - username: must not be blank
```

The application will fail to start, ensuring misconfigured properties are caught early.

---

### **Customizing Validation**

You can add more complex validation rules or custom annotations to enforce specific requirements.

#### **Example: Adding Custom Constraints**
```java
import jakarta.validation.constraints.Pattern;

@Validated
@ConfigurationProperties("database")
public class DatabaseProperties {

    @NotBlank
    private String username;

    @NotBlank
    @Pattern(regexp = "^(?=.*[A-Z])(?=.*\\d).{8,}$", message = "Password must be at least 8 characters long, contain one uppercase letter, and one number")
    private String password;

    // Getters and Setters
}
```

#### **Invalid Password Configuration**
```yaml
database:
  username: admin
  password: weakpass
```

#### **Error During Startup**
```
Caused by: org.springframework.boot.context.properties.bind.validation.BindValidationException: 
Binding validation errors on database
 - password: Password must be at least 8 characters long, contain one uppercase letter, and one number
```

---

### **Global Validation Exception Handling**

If you want to handle validation exceptions globally, you can create a `@ControllerAdvice` to catch and format validation errors.

#### **Example**
```java
import org.springframework.boot.context.properties.bind.validation.BindValidationException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BindValidationException.class)
    public ResponseEntity<String> handleBindValidationException(BindValidationException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

---

### **Key Points**

1. **`@Validated` with `@ConfigurationProperties`**:
   - Ensures that configuration properties are validated during binding.
   - Prevents the application from starting with invalid configurations.

2. **Validation Annotations**:
   - Use standard Bean Validation annotations like `@NotBlank`, `@Pattern`, `@Min`, etc.
   - Combine them for more complex validation requirements.

3. **Error Handling**:
   - Spring automatically throws validation exceptions if constraints are violated.
   - You can handle these exceptions globally for a better user experience.

4. **Custom Constraints**:
   - Add custom validation annotations for domain-specific requirements.

