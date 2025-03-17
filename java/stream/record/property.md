### **Properties in Java Records**

Java Records provide a concise way to define **immutable data objects**. Properties in a Java Record are the fields that are declared in the record's header. These properties are automatically handled by the compiler, reducing boilerplate code while ensuring immutability.

---

### **Key Characteristics of Properties in Java Records**

1. **Declared in the Record Header**:
   - Properties are defined in the parentheses of the record declaration.
   - Example:
     ```java
     public record Customer(String id, String name, String email, String phone) { }
     ```
     Here, `id`, `name`, `email`, and `phone` are the properties.

2. **Immutable and Final**:
   - Properties in a record are implicitly `final`, meaning they cannot be modified after the object is created.
   - No setter methods are generated for properties.

3. **Private by Default**:
   - Properties are private and cannot be accessed directly. However, the record automatically generates **getter-like methods** to retrieve their values.

4. **Getter-like Methods**:
   - For each property, a method with the same name as the property is automatically generated.
   - Example:
     ```java
     Customer customer = new Customer("1", "Eko", "eko@localhost", "088888");
     System.out.println(customer.id());   // Accesses the "id" property
     System.out.println(customer.name()); // Accesses the "name" property
     ```

5. **Constructor Generation**:
   - The properties in the record header automatically become parameters of the primary constructor.
   - Example:
     ```java
     var customer = new Customer("1", "Eko", "eko@localhost", "088888");
     ```

6. **No Getters in Standard Form**:
   - Unlike traditional classes, the getter methods in records are not prefixed with `get`. Instead, the method names match the property names.
   - For example:
     - Property: `id`
     - Getter: `id()`

---

### **Accessing Properties in Java Records**

Since properties are private, you cannot access them directly. Instead, you use the automatically generated methods to retrieve their values.

#### Example:

```java
public record Customer(String id, String name, String email, String phone) { }

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("1", "Eko", "eko@localhost", "088888");

        // Accessing properties using generated methods
        System.out.println("ID: " + customer.id());
        System.out.println("Name: " + customer.name());
        System.out.println("Email: " + customer.email());
        System.out.println("Phone: " + customer.phone());
    }
}
```

**Output:**
```
ID: 1
Name: Eko
Email: eko@localhost
Phone: 088888
```

---

### **Automatic Constructor for Properties**

The properties specified in a record's header automatically generate a constructor. This constructor is used to initialize the properties when creating an instance of the record.

#### Example:

```java
public record Customer(String id, String name, String email, String phone) { }

public class Main {
    public static void main(String[] args) {
        // Creating a new record instance
        Customer customer = new Customer("1", "Eko", "eko@localhost", "088888");

        System.out.println(customer);
    }
}
```

**Output:**
```
Customer[id=1, name=Eko, email=eko@localhost, phone=088888]
```

---

### **Customizing Property Behavior**

You can add validation or custom logic for properties by defining a **compact constructor** in the record.

#### Example: Adding Validation

```java
public record Customer(String id, String name, String email, String phone) {
    public Customer {
        if (id == null || id.isBlank()) {
            throw new IllegalArgumentException("ID cannot be null or blank");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email");
        }
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        try {
            Customer customer = new Customer("", "Eko", "invalid_email", "088888");
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**Output:**
```
ID cannot be null or blank
```

---

### **Property Behavior in Collections**

Properties in records work seamlessly with collections like `List` or `Map`. The automatically generated `equals()` and `hashCode()` methods ensure proper behavior in such scenarios.

#### Example:

```java
import java.util.*;

public record Product(String id, String name, double price) { }

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
            new Product("1", "Laptop", 1500.00),
            new Product("2", "Phone", 800.00)
        );

        products.forEach(product -> {
            System.out.println("ID: " + product.id());
            System.out.println("Name: " + product.name());
            System.out.println("Price: " + product.price());
        });
    }
}
```

**Output:**
```
ID: 1
Name: Laptop
Price: 1500.0
ID: 2
Name: Phone
Price: 800.0
```

---

### **Testing Property Access**

You can use unit tests to verify the behavior of properties in a record.

#### Example:

```java
import static org.junit.jupiter.api.Assertions.*;

public class CustomerTest {
    @Test
    void testCustomerProperties() {
        Customer customer = new Customer("1", "Eko", "eko@localhost", "088888");

        assertEquals("1", customer.id());
        assertEquals("Eko", customer.name());
        assertEquals("eko@localhost", customer.email());
        assertEquals("088888", customer.phone());
    }
}
```

---

### **Comparison: Properties in Regular Class vs. Record**

#### Regular Class:

```java
public class Customer {
    private final String id;
    private final String name;
    private final String email;
    private final String phone;

    public Customer(String id, String name, String email, String phone) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.phone = phone;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public String getPhone() {
        return phone;
    }
}
```

#### Record:

```java
public record Customer(String id, String name, String email, String phone) { }
```

**Advantages of Record:**
- No need to write boilerplate code for fields, getters, constructor, `equals()`, `hashCode()`, and `toString()`.

