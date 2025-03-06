### **Introduction to Method Validation in Spring Boot**

Method validation in Spring Boot allows you to validate method parameters and return values using the Bean Validation API (`javax.validation` or `jakarta.validation`). It is useful for validating service-layer methods, controller methods, or any other Spring-managed components.

Spring Boot integrates method validation seamlessly with the `@Validated` annotation, which enables validation on method parameters, return values, and even nested objects.

---

### **Key Features of Method Validation**

1. **Parameter Validation**:
   - Validate input parameters of methods using annotations like `@NotNull`, `@Size`, `@Min`, etc.

2. **Return Value Validation**:
   - Ensure that the return value of a method meets certain constraints using `@Valid`.

3. **Nested Object Validation**:
   - Validate nested objects within method parameters or return values.

4. **Custom Constraints**:
   - Define custom validation annotations for specific use cases.

---

### **How Method Validation Works**

1. **Enable Validation**:
   - Add the `@Validated` annotation to the class or method where validation is required.
   - Use constraints from the Bean Validation API (e.g., `@NotNull`, `@Size`) on method parameters or return values.

2. **Validation Errors**:
   - If validation fails, Spring throws a `ConstraintViolationException`.

3. **Custom Error Handling**:
   - You can handle validation errors globally using a `@ControllerAdvice`.

---

### **Steps to Implement Method Validation**

#### **1. Add Dependencies**

Add the following dependencies to your `pom.xml` for Bean Validation:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

---

#### **2. Enable Method Validation**

To enable method validation, annotate the Spring-managed component (e.g., `@Service`, `@RestController`) with `@Validated`.

---

#### **3. Example of Method Validation**

##### **Service Layer Example**
```java
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class UserService {

    public String createUser(@NotNull @Size(min = 2, max = 30) String name) {
        return "User " + name + " created successfully!";
    }
}
```

##### **Controller Layer Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping
    public String createUser(@RequestParam String name) {
        return userService.createUser(name);
    }
}
```

##### **Request Example**
```bash
POST /users?name=J
```

##### **Response**
```json
{
  "error": "createUser.name: size must be between 2 and 30"
}
```

---

### **4. Validate Method Return Values**

You can also validate the return value of a method using constraints like `@Valid`.

##### **Example**
```java
import jakarta.validation.Valid;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class UserService {

    public @Valid User getUser() {
        return new User(null, "short");
    }

    public static class User {

        @NotNull
        private String id;

        @Size(min = 5)
        private String name;

        public User(String id, String name) {
            this.id = id;
            this.name = name;
        }

        // Getters and Setters
    }
}
```

##### **Error Response**
If the return value doesn't meet the constraints, a `ConstraintViolationException` is thrown.

---

### **5. Nested Object Validation**

If a method parameter or return value contains nested objects, you can use `@Valid` to validate the nested fields.

##### **Example**
```java
import jakarta.validation.Valid;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class UserService {

    public String registerUser(@Valid User user) {
        return "User " + user.getName() + " registered successfully!";
    }

    public static class User {

        @NotNull
        private String id;

        @Size(min = 5)
        private String name;

        public User(String id, String name) {
            this.id = id;
            this.name = name;
        }

        // Getters and Setters
    }
}
```

##### **Controller Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping("/register")
    public String registerUser(@RequestBody @Valid UserService.User user) {
        return userService.registerUser(user);
    }
}
```

##### **Request Example**
```json
{
  "id": null,
  "name": "John"
}
```

##### **Response**
```json
{
  "error": "registerUser.user.id: must not be null"
}
```

---

### **6. Custom Validation Annotation**

You can create custom validation annotations for specific use cases.

##### **Example: Custom Annotation**
```java
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Constraint(validatedBy = { })
@Target({ ElementType.METHOD, ElementType.FIELD, ElementType.PARAMETER })
@Retention(RetentionPolicy.RUNTIME)
public @interface ValidEmail {

    String message() default "Invalid email format";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

##### **Validator Implementation**
```java
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

public class EmailValidator implements ConstraintValidator<ValidEmail, String> {

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value != null && value.matches("^[A-Za-z0-9+_.-]+@(.+)$");
    }
}
```

##### **Usage**
```java
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class UserService {

    public String registerEmail(@ValidEmail String email) {
        return "Email " + email + " is valid!";
    }
}
```

---

### **7. Global Exception Handling**

Handle validation exceptions globally using `@ControllerAdvice`.

##### **Example**
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import jakarta.validation.ConstraintViolationException;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ConstraintViolationException.class)
    public ResponseEntity<String> handleValidationException(ConstraintViolationException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

---

### **8. Validate Multiple Parameters**

You can validate multiple parameters in a method.

##### **Example**
```java
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotNull;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class OrderService {

    public String placeOrder(@NotNull String productId, @Min(1) int quantity) {
        return "Order placed for product " + productId + " with quantity " + quantity;
    }
}
```

---

### **Complete Example**

#### **Service**
```java
import jakarta.validation.Valid;
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;
import org.springframework.stereotype.Service;
import org.springframework.validation.annotation.Validated;

@Service
@Validated
public class UserService {

    public String createUser(@NotNull @Size(min = 2, max = 30) String name) {
        return "User " + name + " created successfully!";
    }

    public @Valid User getUser() {
        return new User(null, "short");
    }

    public static class User {

        @NotNull
        private String id;

        @Size(min = 5)
        private String name;

        public User(String id, String name) {
            this.id = id;
            this.name = name;
        }

        // Getters and Setters
    }
}
```

#### **Controller**
```java
import jakarta.validation.Valid;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @PostMapping
    public String createUser(@RequestParam String name) {
        return userService.createUser(name);
    }

    @GetMapping
    public UserService.User getUser() {
        return userService.getUser();
    }
}
```
