 

### **1. RegisterUserRequest Class**
This class represents the user registration request with validation annotations to ensure input data is valid.

```java
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class RegisterUserRequest {

    @NotBlank
    @Size(max = 100)
    private String username;

    @NotBlank
    @Size(max = 100)
    private String password;

    @NotBlank
    @Size(max = 100)
    private String name;
}
```

---

### **2. User Entity**
This class represents the database entity for storing user information.

```java
import jakarta.persistence.*;
import lombok.*;

import java.util.List;

@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@Entity
@Table(name = "users")
public class User {

    @Id
    private String username;

    private String password;

    private String name;

    private String token;

    @Column(name = "token_expired_at")
    private Long tokenExpiredAt;

    @OneToMany(mappedBy = "user")
    private List<Contact> contacts;
}
```

---

### **3. UserRepository**
This interface handles database operations for the `User` entity.

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, String> {
    boolean existsByUsername(String username); // Custom method to check if a username exists
}
```

---

### **4. UserService**
This service contains the business logic for user registration.

```java
import jakarta.validation.ConstraintViolation;
import jakarta.validation.ConstraintViolationException;
import jakarta.validation.Validator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.security.crypto.bcrypt.BCrypt;

import java.util.Set;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private Validator validator;

    @Transactional
    public void register(RegisterUserRequest request) {
        // Validate the request
        Set<ConstraintViolation<RegisterUserRequest>> constraintViolations = validator.validate(request);
        if (!constraintViolations.isEmpty()) {
            throw new ConstraintViolationException(constraintViolations);
        }

        // Check if the username is already registered
        if (userRepository.existsByUsername(request.getUsername())) {
            throw new ApiException("Username already registered");
        }

        // Create a new User entity
        User user = new User();
        user.setUsername(request.getUsername());
        user.setPassword(BCrypt.hashpw(request.getPassword(), BCrypt.gensalt())); // Hash the password
        user.setName(request.getName());

        // Save the user to the database
        userRepository.save(user);
    }
}
```

---

### **5. UserController**
This controller handles HTTP requests for user registration.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping("/register")
    public ResponseEntity<String> registerUser(@RequestBody @Validated RegisterUserRequest request) {
        userService.register(request);
        return ResponseEntity.ok("User registered successfully!");
    }
}
```

---

### **6. Exception Handling**
To handle exceptions like validation errors or custom exceptions, you can create a global exception handler.

#### **ApiException Class**
```java
public class ApiException extends RuntimeException {
    public ApiException(String message) {
        super(message);
    }
}
```

#### **GlobalExceptionHandler**
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import jakarta.validation.ConstraintViolationException;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<String> handleValidationException(ConstraintViolationException ex) {
        return ResponseEntity.badRequest().body("Validation error: " + ex.getMessage());
    }

    @ExceptionHandler(ApiException.class)
    public ResponseEntity<String> handleApiException(ApiException ex) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(ex.getMessage());
    }
}
```

---

### **7. Sample Request and Response**

#### **Request:**
**POST** `/api/users/register`  
**Body (JSON):**
```json
{
  "username": "john_doe",
  "password": "password123",
  "name": "John Doe"
}
```

#### **Response (Success):**
**Status:** `200 OK`  
**Body:**
```json
"User registered successfully!"
```

#### **Response (Error - Username Exists):**
**Status:** `400 Bad Request`  
**Body:**
```json
"Username already registered"
```

#### **Response (Error - Validation):**
**Status:** `400 Bad Request`  
**Body:**
```json
"Validation error: ..."
```

