### Bean Validation in Spring Boot

Bean Validation is a Java standard (part of the Jakarta EE specification) for validating the properties of Java Beans. Spring Boot supports Bean Validation using the **javax.validation** (or **jakarta.validation** in newer versions) API, which includes annotations like `@NotNull`, `@Size`, `@Min`, `@Max`, and more. This allows you to validate input data at the model level, ensuring correctness before processing.

Spring Boot integrates Bean Validation seamlessly with its web layer, making it easy to validate request payloads, query parameters, and more.

---

### How Bean Validation Works in Spring Boot

1. **Java Bean**: A simple POJO with fields and corresponding getters/setters.
2. **Validation Annotations**: Add constraints to the fields of the Java Bean using annotations like `@NotNull`, `@Size`, etc.
3. **Validator**: The validation framework automatically validates the bean when it is used in a Spring context (e.g., as a request body in a controller).
4. **Error Handling**: If validation fails, Spring Boot automatically generates appropriate error responses or allows you to customize them.

---

### Common Validation Annotations

Here are some commonly used validation annotations:

| **Annotation**       | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `@NotNull`           | Ensures the field is not null.                                                  |
| `@NotEmpty`          | Ensures the field is not null and not empty (for Strings or collections).       |
| `@NotBlank`          | Ensures the field is not null and contains at least one non-whitespace character.|
| `@Size`              | Ensures the field's size is within a given range (for Strings, collections, etc.).|
| `@Min`               | Ensures the field's value is greater than or equal to the specified minimum.    |
| `@Max`               | Ensures the field's value is less than or equal to the specified maximum.       |
| `@Positive`          | Ensures the field's value is positive.                                          |
| `@PositiveOrZero`    | Ensures the field's value is zero or positive.                                  |
| `@Negative`          | Ensures the field's value is negative.                                          |
| `@NegativeOrZero`    | Ensures the field's value is zero or negative.                                  |
| `@Email`             | Ensures the field contains a valid email address.                              |
| `@Pattern`           | Ensures the field matches a given regular expression pattern.                  |
| `@Future`            | Ensures the field contains a date in the future.                               |
| `@Past`              | Ensures the field contains a date in the past.                                 |

---

### Example 1: Validating a Request Body

#### 1. Define a DTO (Data Transfer Object)

```java
import jakarta.validation.constraints.*;

public class UserDTO {

    @NotNull(message = "Name cannot be null")
    @Size(min = 2, max = 50, message = "Name must be between 2 and 50 characters")
    private String name;

    @NotNull(message = "Email cannot be null")
    @Email(message = "Email should be valid")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    @Max(value = 100, message = "Age must be less than or equal to 100")
    private int age;

    // Getters and setters
}
```

#### 2. Use the DTO in a Controller

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import jakarta.validation.Valid;

@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public ResponseEntity<String> createUser(@Valid @RequestBody UserDTO user) {
        // If validation passes, process the user
        return ResponseEntity.status(HttpStatus.CREATED).body("User created successfully");
    }
}
```

#### 3. Example Request and Response

- **Request**:
  ```json
  {
      "name": "John",
      "email": "john.doe@example.com",
      "age": 25
  }
  ```

- **Response for Valid Input**:
  ```json
  {
      "message": "User created successfully"
  }
  ```

- **Response for Invalid Input**:
  ```json
  {
      "timestamp": "2025-03-06T16:35:00.000+00:00",
      "status": 400,
      "errors": [
          {
              "field": "email",
              "message": "Email should be valid"
          },
          {
              "field": "age",
              "message": "Age must be at least 18"
          }
      ]
  }
  ```

---

### Example 2: Validating Query Parameters

#### 1. Define a Controller Method

```java
import jakarta.validation.constraints.*;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/products")
@Validated // Required for validating query parameters
public class ProductController {

    @GetMapping
    public String getProduct(
            @RequestParam @NotNull(message = "Product ID cannot be null") Long productId,
            @RequestParam @Min(value = 1, message = "Quantity must be at least 1") int quantity) {
        return "Product retrieved successfully";
    }
}
```

#### 2. Example Request and Response

- **Request**:
  ```
  GET /products?productId=123&quantity=5
  ```

- **Response for Valid Input**:
  ```
  Product retrieved successfully
  ```

- **Response for Invalid Input**:
  ```
  {
      "timestamp": "2025-03-06T16:35:00.000+00:00",
      "status": 400,
      "errors": [
          {
              "field": "productId",
              "message": "Product ID cannot be null"
          },
          {
              "field": "quantity",
              "message": "Quantity must be at least 1"
          }
      ]
  }
  ```

---

### Example 3: Custom Validation Constraint

#### 1. Create a Custom Annotation

```java
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Constraint(validatedBy = CustomValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomConstraint {
    String message() default "Invalid value";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

#### 2. Implement the Validator

```java
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

public class CustomValidator implements ConstraintValidator<CustomConstraint, String> {

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        // Custom validation logic
        return value != null && value.startsWith("CUSTOM");
    }
}
```

#### 3. Use the Custom Constraint

```java
public class CustomDTO {

    @CustomConstraint(message = "Value must start with 'CUSTOM'")
    private String customField;

    // Getters and setters
}
```

---

### Example 4: Global Validation Error Handling

You can customize the error response for validation failures by creating a global exception handler.

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.HashMap;
import java.util.Map;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> 
            errors.put(error.getField(), error.getDefaultMessage())
        );
        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }
}
```
 
 
