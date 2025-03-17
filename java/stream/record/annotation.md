### **Annotations in Java Records**

Annotations in Java Records work similarly to annotations in regular classes. However, there are some unique behaviors when annotations are applied to record components. These behaviors make records particularly powerful when combined with annotations for validation, documentation, or runtime processing.

---

### **1. Key Features of Annotations in Java Records**

1. **Annotations on Record Components**:
   - When an annotation is applied to a record component (e.g., `@Valid int x`), it is automatically applied to:
     - The **constructor parameter**.
     - The **field/property**.
     - The **getter method**.

2. **Automatic Propagation**:
   - The annotation on the record component is propagated to all the relevant parts of the record:
     - Constructor parameters.
     - Generated fields.
     - Getter methods.

3. **Annotations on the Record Class**:
   - You can annotate the record class itself for use cases like marking it as a specific type or enabling certain behaviors.

4. **Custom Annotations**:
   - Custom annotations can be created and applied to record components or the record class.

5. **Validation and Reflection**:
   - Annotations in records are useful for runtime validation (e.g., using frameworks like Hibernate Validator).
   - Reflection can be used to retrieve annotations from fields, methods, or constructors.

---

### **2. Example: Custom Annotation**

#### **Defining a Custom Annotation**

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// Define a custom annotation
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface Valid {
}
```

- **`@Target`**: Specifies where the annotation can be applied (e.g., fields, methods, parameters).
- **`@Retention`**: Specifies how long the annotation is retained (e.g., at runtime).

---

### **3. Applying Annotations to a Record**

#### **Record Definition**

```java
public record Point(@Valid int x, @Valid int y) {
    public Point {
        System.out.println("Creating Point");
    }

    public static final Point ZERO = new Point(0, 0);

    public static Point create(int x, int y) {
        return new Point(x, y);
    }
}
```

- The `@Valid` annotation is applied to the `x` and `y` components of the `Point` record.
- The annotation will automatically propagate to:
  - The **fields** `x` and `y`.
  - The **constructor parameters**.
  - The **getter methods** `x()` and `y()`.

---

### **4. Testing Annotations in Records**

#### **JUnit Test Example**

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertNotNull;

public class PointTest {
    @Test
    void annotation() throws NoSuchMethodException, NoSuchFieldException {
        var point = new Point(10, 10);

        // Check annotations on fields
        assertNotNull(point.getClass().getDeclaredField("x").getAnnotation(Valid.class));
        assertNotNull(point.getClass().getDeclaredField("y").getAnnotation(Valid.class));

        // Check annotations on getter methods
        assertNotNull(point.getClass().getDeclaredMethod("x").getAnnotation(Valid.class));
        assertNotNull(point.getClass().getDeclaredMethod("y").getAnnotation(Valid.class));

        // Check annotations on constructor parameters
        assertNotNull(point.getClass().getConstructors()[0].getParameters()[0].getAnnotation(Valid.class));
        assertNotNull(point.getClass().getConstructors()[0].getParameters()[1].getAnnotation(Valid.class));
    }
}
```

#### **Explanation**:
- The test validates that the `@Valid` annotation is present on:
  - The fields `x` and `y`.
  - The getter methods `x()` and `y()`.
  - The constructor parameters.

---

### **5. Annotations on the Record Class**

You can annotate the entire record class for specific purposes.

#### **Example**

```java
@Deprecated
public record DeprecatedRecord(String data) {
}
```

- The `@Deprecated` annotation marks the entire record as deprecated.

---

### **6. Combining Annotations with Validation Frameworks**

Annotations in records are often used with frameworks like **Hibernate Validator** for runtime validation.

#### **Example with Hibernate Validator**

```java
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;

public record User(
    @NotNull @Size(min = 3, max = 20) String username,
    @NotNull @Size(min = 6) String password
) {
}
```

#### **Validation Example**

```java
import jakarta.validation.Validation;
import jakarta.validation.Validator;
import jakarta.validation.ValidatorFactory;
import jakarta.validation.ConstraintViolation;

import java.util.Set;

public class Main {
    public static void main(String[] args) {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        Validator validator = factory.getValidator();

        User user = new User("AB", "12345"); // Invalid username and password
        Set<ConstraintViolation<User>> violations = validator.validate(user);

        violations.forEach(violation -> System.out.println(violation.getMessage()));
    }
}
```

#### **Output**:
```
size must be between 3 and 20
size must be between 6 and 2147483647
```

---

### **7. Record Annotations with Reflection**

Annotations in records can be retrieved at runtime using reflection.

#### **Example**

```java
import java.lang.reflect.Field;

public class Main {
    public static void main(String[] args) throws NoSuchFieldException {
        Point point = new Point(10, 20);

        Field fieldX = point.getClass().getDeclaredField("x");
        if (fieldX.isAnnotationPresent(Valid.class)) {
            System.out.println("Field 'x' is annotated with @Valid");
        }

        Field fieldY = point.getClass().getDeclaredField("y");
        if (fieldY.isAnnotationPresent(Valid.class)) {
            System.out.println("Field 'y' is annotated with @Valid");
        }
    }
}
```

#### **Output**:
```
Field 'x' is annotated with @Valid
Field 'y' is annotated with @Valid
```

---

### **8. Nested Annotations in Records**

You can use nested annotations to group related metadata.

#### **Example**

```java
public @interface Metadata {
    String author();
    String version();
}

@Metadata(author = "John Doe", version = "1.0")
public record Document(String title, String content) {
}
```

#### **Reflection Example**

```java
import java.lang.annotation.Annotation;

public class Main {
    public static void main(String[] args) {
        Annotation[] annotations = Document.class.getAnnotations();
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }
    }
}
```

#### **Output**:
```
@Metadata(author=John Doe, version=1.0)
```

---

### **9. Summary**

| Feature                           | Behavior                                                                 |
|-----------------------------------|-------------------------------------------------------------------------|
| **Annotations on Components**     | Automatically applied to fields, constructor parameters, and getters.   |
| **Annotations on Class**          | Used for marking the record class (e.g., `@Deprecated`).                |
| **Custom Annotations**            | Custom annotations can be created and applied to record components.     |
| **Validation Frameworks**         | Works seamlessly with frameworks like Hibernate Validator.              |
| **Reflection**                    | Annotations can be retrieved at runtime using reflection.               |
| **Nested Annotations**            | Allows grouping of related metadata.                                    |

