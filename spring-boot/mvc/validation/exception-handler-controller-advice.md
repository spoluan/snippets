### **Exception Handling and `@ControllerAdvice` in Spring Boot**

In Spring Boot, **exception handling** is a critical feature to ensure your application gracefully handles errors and provides meaningful responses to clients. The `@ControllerAdvice` annotation is a powerful tool for centralizing exception handling logic, making it easier to manage and customize error responses across your application.

---

### **1. What is `@ControllerAdvice`?**

- `@ControllerAdvice` is a specialized annotation in Spring that allows you to handle exceptions globally across multiple controllers.
- It acts as a centralized error handler for all controllers in your application.
- By using `@ControllerAdvice`, you can:
  - Catch specific exceptions.
  - Customize error responses.
  - Reduce boilerplate code in individual controllers.

---

### **2. How Does `@ControllerAdvice` Work?**

- A class annotated with `@ControllerAdvice` is automatically scanned by Spring and used to handle exceptions thrown by controllers.
- Inside this class, you can define methods annotated with `@ExceptionHandler` to handle specific exception types.

---

### **3. Key Annotations for Exception Handling**

| **Annotation**       | **Description**                                                                                   |
|-----------------------|---------------------------------------------------------------------------------------------------|
| `@ControllerAdvice`  | Marks a class as a centralized exception handler for multiple controllers.                        |
| `@ExceptionHandler`  | Marks a method to handle a specific type of exception.                                            |
| `@ResponseStatus`    | Sets the HTTP status code for the response when an exception is thrown.                           |
| `@RestControllerAdvice` | Combines `@ControllerAdvice` and `@ResponseBody` to return JSON responses for exceptions.       |

---

### **4. Example: Basic Exception Handling**

#### **Step 1: Create a `@ControllerAdvice` Class**

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleRuntimeException(RuntimeException ex) {
        return new ResponseEntity<>("Runtime Exception: " + ex.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
    }

    @ExceptionHandler(IllegalArgumentException.class)
    public ResponseEntity<String> handleIllegalArgumentException(IllegalArgumentException ex) {
        return new ResponseEntity<>("Illegal Argument: " + ex.getMessage(), HttpStatus.BAD_REQUEST);
    }
}
```

#### **Step 2: Trigger Exceptions in a Controller**

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class TestController {

    @GetMapping("/runtime")
    public String throwRuntimeException() {
        throw new RuntimeException("This is a runtime exception");
    }

    @GetMapping("/illegal")
    public String throwIllegalArgumentException() {
        throw new IllegalArgumentException("This is an illegal argument exception");
    }
}
```

#### **Responses:**

1. **Runtime Exception** (`/runtime`):
   ```json
   {
       "message": "Runtime Exception: This is a runtime exception"
   }
   ```

2. **Illegal Argument Exception** (`/illegal`):
   ```json
   {
       "message": "Illegal Argument: This is an illegal argument exception"
   }
   ```

---

### **5. Handling Validation Exceptions**

#### **Step 1: Handle `MethodArgumentNotValidException`**

When using `@Valid` for request validation, Spring throws `MethodArgumentNotValidException` if validation fails.

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import java.util.HashMap;
import java.util.Map;

@ControllerAdvice
public class ValidationExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, String>> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        for (FieldError error : ex.getBindingResult().getFieldErrors()) {
            errors.put(error.getField(), error.getDefaultMessage());
        }
        return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
    }
}
```

#### **Step 2: Controller with Validation**

```java
import jakarta.validation.constraints.NotBlank;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Validated
public class UserController {

    @PostMapping("/users")
    public String createUser(@Valid @RequestBody UserRequest request) {
        return "User created successfully!";
    }
}

class UserRequest {
    @NotBlank(message = "Name must not be blank")
    private String name;

    @NotBlank(message = "Email must not be blank")
    private String email;

    // Getters and setters
}
```

#### **Invalid Request:**
```json
{
    "email": ""
}
```

#### **Response:**
```json
{
    "name": "Name must not be blank",
    "email": "Email must not be blank"
}
```

---

### **6. Custom Exception Handling**

#### **Step 1: Create a Custom Exception**

```java
public class CustomNotFoundException extends RuntimeException {
    public CustomNotFoundException(String message) {
        super(message);
    }
}
```

#### **Step 2: Use the Exception in a Controller**

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ProductController {

    @GetMapping("/product")
    public String getProduct() {
        throw new CustomNotFoundException("Product not found");
    }
}
```

#### **Step 3: Handle the Custom Exception**

```java
@ControllerAdvice
public class CustomExceptionHandler {

    @ExceptionHandler(CustomNotFoundException.class)
    public ResponseEntity<String> handleCustomNotFoundException(CustomNotFoundException ex) {
        return new ResponseEntity<>("Error: " + ex.getMessage(), HttpStatus.NOT_FOUND);
    }
}
```

#### **Response:**
```json
{
    "message": "Error: Product not found"
}
```

---

### **7. Returning JSON Responses with `@RestControllerAdvice`**

If you want to return JSON responses without using `@ResponseBody` on every method, use `@RestControllerAdvice`.

#### **Example:**

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@RestControllerAdvice
public class JsonExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<Map<String, String>> handleRuntimeException(RuntimeException ex) {
        Map<String, String> response = new HashMap<>();
        response.put("error", ex.getMessage());
        return new ResponseEntity<>(response, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

### **8. Handling Multiple Exceptions in One Method**

You can handle multiple exception types in a single method.

```java
@ExceptionHandler({IllegalArgumentException.class, NullPointerException.class})
public ResponseEntity<String> handleMultipleExceptions(Exception ex) {
    return new ResponseEntity<>("Error: " + ex.getMessage(), HttpStatus.BAD_REQUEST);
}
```

---

### **9. Global Exception Handling for All Exceptions**

To catch all unhandled exceptions:

```java
@ExceptionHandler(Exception.class)
public ResponseEntity<String> handleGlobalException(Exception ex) {
    return new ResponseEntity<>("An unexpected error occurred: " + ex.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
}
```

---

### **10. Summary of Best Practices**

| **Scenario**                       | **Handler**                                                                                 |
|------------------------------------|---------------------------------------------------------------------------------------------|
| Validation Errors                  | Handle `MethodArgumentNotValidException` or `ConstraintViolationException`.                |
| Custom Exceptions                  | Create custom exceptions and handle them with `@ExceptionHandler`.                         |
| JSON Responses                     | Use `@RestControllerAdvice` for consistent JSON error responses.                           |
| Global Exception Handling          | Catch all exceptions with `@ExceptionHandler(Exception.class)`.                            |
| Centralized Error Handling         | Use `@ControllerAdvice` to centralize all exception handling logic.                        |

