### **Reflection in Java Records**

Reflection in Java Records allows developers to inspect and interact with the structure and components of records at runtime. Java introduced new methods in the `java.lang.Class` and `java.lang.reflect` packages to support reflection for records.

---

### **1. Key Features of Reflection in Java Records**

1. **`isRecord()` Method**:
   - A new method in the `java.lang.Class` class.
   - Used to check if a given class is a record.

2. **`getRecordComponents()` Method**:
   - A new method in the `java.lang.Class` class.
   - Returns an array of `java.lang.reflect.RecordComponent` objects, representing the components (fields) of the record.
   - Each `RecordComponent` contains metadata about the record's properties, such as name, type, and annotations.

3. **`RecordComponent` Class**:
   - Represents a single component of a record.
   - Provides methods to retrieve metadata, such as:
     - `getName()`: Returns the name of the component.
     - `getType()`: Returns the type of the component.
     - `getAnnotations()`: Returns annotations applied to the component.

---

### **2. Methods in Reflection for Records**

| **Method**                        | **Description**                                                                                   |
|-----------------------------------|---------------------------------------------------------------------------------------------------|
| `Class.isRecord()`                | Checks if a class is a record.                                                                    |
| `Class.getRecordComponents()`     | Retrieves an array of `RecordComponent` objects representing the components of the record.        |
| `RecordComponent.getName()`       | Returns the name of the record component (field).                                                |
| `RecordComponent.getType()`       | Returns the type of the record component.                                                        |
| `RecordComponent.getAnnotations()`| Retrieves annotations applied to the record component.                                           |

---

### **3. Example: Basic Reflection with Records**

#### **Record Definition**

```java
public record Customer(String id, String name, String email) {
}
```

#### **Reflection Example**

```java
import java.lang.reflect.RecordComponent;

public class Main {
    public static void main(String[] args) {
        // Create a record instance
        Customer customer = new Customer("1", "John Doe", "john@example.com");

        // Check if the class is a record
        if (customer.getClass().isRecord()) {
            System.out.println("Customer is a record.");

            // Get record components
            RecordComponent[] components = customer.getClass().getRecordComponents();
            for (RecordComponent component : components) {
                System.out.println("Component Name: " + component.getName());
                System.out.println("Component Type: " + component.getType());
            }
        } else {
            System.out.println("Customer is not a record.");
        }
    }
}
```

#### **Output**:
```
Customer is a record.
Component Name: id
Component Type: class java.lang.String
Component Name: name
Component Type: class java.lang.String
Component Name: email
Component Type: class java.lang.String
```

---

### **4. Example: Checking Annotations on Record Components**

#### **Record with Annotations**

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// Define a custom annotation
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface Valid {
}

public record Customer(
    @Valid String id,
    @Valid String name,
    String email
) {
}
```

#### **Reflection Example**

```java
import java.lang.reflect.RecordComponent;

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("1", "John Doe", "john@example.com");

        // Get record components
        RecordComponent[] components = customer.getClass().getRecordComponents();
        for (RecordComponent component : components) {
            System.out.println("Component Name: " + component.getName());
            System.out.println("Component Type: " + component.getType());

            // Check if the component has the @Valid annotation
            if (component.isAnnotationPresent(Valid.class)) {
                System.out.println("Annotation @Valid is present on " + component.getName());
            } else {
                System.out.println("No annotations found on " + component.getName());
            }
        }
    }
}
```

#### **Output**:
```
Component Name: id
Component Type: class java.lang.String
Annotation @Valid is present on id
Component Name: name
Component Type: class java.lang.String
Annotation @Valid is present on name
Component Name: email
Component Type: class java.lang.String
No annotations found on email
```

---

### **5. Example: Using Reflection to Dynamically Access Record Components**

#### **Record Definition**

```java
public record Customer(String id, String name, String email) {
}
```

#### **Dynamic Access Example**

```java
import java.lang.reflect.RecordComponent;
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws Exception {
        Customer customer = new Customer("1", "John Doe", "john@example.com");

        // Get record components
        RecordComponent[] components = customer.getClass().getRecordComponents();
        for (RecordComponent component : components) {
            System.out.println("Component Name: " + component.getName());

            // Dynamically invoke the getter method for each component
            Method getter = customer.getClass().getMethod(component.getName());
            Object value = getter.invoke(customer);
            System.out.println("Value: " + value);
        }
    }
}
```

#### **Output**:
```
Component Name: id
Value: 1
Component Name: name
Value: John Doe
Component Name: email
Value: john@example.com
```

---

### **6. Example: Validating Record Data Using Reflection**

#### **Record Definition**

```java
public record User(String username, String email) {
}
```

#### **Validation Example**

```java
import java.lang.reflect.RecordComponent;

public class Main {
    public static void main(String[] args) {
        User user = new User("johndoe", "john@example.com");

        // Validate record components
        RecordComponent[] components = user.getClass().getRecordComponents();
        for (RecordComponent component : components) {
            try {
                Method getter = user.getClass().getMethod(component.getName());
                Object value = getter.invoke(user);

                if (value == null || value.toString().isEmpty()) {
                    System.out.println("Validation failed for " + component.getName());
                } else {
                    System.out.println(component.getName() + " is valid: " + value);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### **Output**:
```
username is valid: johndoe
email is valid: john@example.com
```

---

### **7. Example: Checking if a Class is a Record**

#### **Example**

```java
public class Main {
    public static void main(String[] args) {
        Class<?> recordClass = Customer.class;

        if (recordClass.isRecord()) {
            System.out.println(recordClass.getName() + " is a record.");
        } else {
            System.out.println(recordClass.getName() + " is not a record.");
        }
    }
}
```

#### **Output**:
```
Customer is a record.
```

---

### **8. Summary**

| **Feature**                     | **Description**                                                                                  |
|----------------------------------|--------------------------------------------------------------------------------------------------|
| `Class.isRecord()`               | Checks if a class is a record.                                                                   |
| `Class.getRecordComponents()`    | Retrieves metadata about record components (fields).                                             |
| `RecordComponent.getName()`      | Gets the name of a record component.                                                             |
| `RecordComponent.getType()`      | Gets the type of a record component.                                                             |
| `RecordComponent.getAnnotations()`| Retrieves annotations applied to a record component.                                            |
| Reflection for Getter Methods    | Allows dynamic invocation of getter methods for record components.                              |
| Validation with Reflection       | Enables runtime validation of record data using reflection.                                     |

