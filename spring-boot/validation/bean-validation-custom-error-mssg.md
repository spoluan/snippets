

### **Example: Handling Validation Results Programmatically**

#### Code Example

```java
import jakarta.validation.ConstraintViolation;
import jakarta.validation.Validator;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Set;

@Service
public class PersonService {

    @Autowired
    private Validator validator;

    public void validatePerson(Person person) {
        // Validate the Person object
        Set<ConstraintViolation<Person>> constraintViolations = validator.validate(person);

        // Check if there are any validation errors
        if (!constraintViolations.isEmpty()) {
            System.out.println("Validation errors found:");
            for (ConstraintViolation<Person> violation : constraintViolations) {
                System.out.println("Field: " + violation.getPropertyPath() +
                        " - Error: " + violation.getMessage());
            }
        } else {
            System.out.println("Person is valid!");
        }
    }
}
```

---

#### **Person Class**

The `Person` class remains the same as in your original code:

```java
import jakarta.validation.constraints.NotBlank;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    @NotBlank(message = "ID cannot be blank")
    private String id;

    @NotBlank(message = "Name cannot be blank")
    private String name;
}
```

---

#### **Main Method or Controller Example**

You can call the `validatePerson` method from a controller or a main method:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements CommandLineRunner {

    @Autowired
    private PersonService personService;

    @Override
    public void run(String... args) {
        // Create a Person object with invalid data
        Person person = new Person("", "");

        // Validate the Person object
        personService.validatePerson(person);
    }
}
```

---

### **Output for Invalid Input**

If you create a `Person` with invalid inputs (both `id` and `name` are blank), the output will look like this:

```
Validation errors found:
Field: id - Error: ID cannot be blank
Field: name - Error: Name cannot be blank
```

---

### **Output for Valid Input**

For a valid `Person` object (e.g., `new Person("123", "John Doe")`), the output will be:

```
Person is valid!
```

---

### **Key Points in This Example**

1. **Programmatic Validation**:
   - Instead of relying on automatic validation (e.g., in a controller), this example shows how to use the `Validator` programmatically.
   
2. **Custom Error Messages**:
   - You can define custom error messages in the annotations (e.g., `@NotBlank(message = "ID cannot be blank")`).

3. **Processing Validation Results**:
   - The `ConstraintViolation` objects provide detailed information about each validation error, such as:
     - The invalid field (`violation.getPropertyPath()`).
     - The error message (`violation.getMessage()`).

4. **Flexible Use Cases**:
   - This approach is useful for non-web-based applications or when you want more control over validation logic.

---

### **Enhancements**

To make this even more robust, you could throw an exception for invalid objects and handle it globally:

1. **Custom Exception**:

```java
public class ValidationException extends RuntimeException {
    public ValidationException(String message) {
        super(message);
    }
}
```

2. **Throw Exception in Service**:

```java
if (!constraintViolations.isEmpty()) {
    StringBuilder errorMessage = new StringBuilder("Validation failed: ");
    for (ConstraintViolation<Person> violation : constraintViolations) {
        errorMessage.append(violation.getPropertyPath())
                    .append(" - ")
                    .append(violation.getMessage())
                    .append("; ");
    }
    throw new ValidationException(errorMessage.toString());
}
```

3. **Global Exception Handler**:

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ValidationException.class)
    public ResponseEntity<String> handleValidationException(ValidationException ex) {
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(ex.getMessage());
    }
}
```
