### **Constructors in Java Records**

A **constructor** in a Java Record is used to initialize its properties. By default, a record generates a **primary constructor** based on the properties declared in its header. However, like regular classes, records also allow you to define **custom constructors** if additional logic or flexibility is needed.

---

### **Key Points about Constructors in Java Records**

1. **Primary Constructor**:
   - Automatically generated based on the properties declared in the record header.
   - Requires all properties to be initialized when creating an object.

2. **Custom Constructors**:
   - You can define additional constructors in a record.
   - All custom constructors **must call the primary constructor** (using `this(...)`).

3. **Compact Constructor**:
   - A special type of constructor in records that allows you to add logic for initializing properties without explicitly listing them as parameters.
   - The compact constructor uses the property names declared in the record header directly.

4. **Overloaded Constructors**:
   - You can define multiple constructors with different parameter combinations for flexibility.

5. **Validation in Constructors**:
   - Custom constructors are often used to validate property values before initializing the record object.

---

### **Examples of Constructors in Java Records**

#### **1. Primary Constructor (Default Behavior)**

The primary constructor is automatically generated and requires all properties to be initialized.

```java
public record Customer(String id, String name, String email, String phone) { }

public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("1", "Sevendi", "Sevendi@localhost", "088888");
        System.out.println(customer);
    }
}
```

**Output:**
```
Customer[id=1, name=Sevendi, email=Sevendi@localhost, phone=088888]
```

---

#### **2. Compact Constructor**

A compact constructor allows you to add custom logic (like validation) while initializing properties.

```java
public record Customer(String id, String name, String email, String phone) {
    public Customer {
        if (id == null || id.isBlank()) {
            throw new IllegalArgumentException("ID cannot be null or blank");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email address");
        }
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        try {
            Customer customer = new Customer("1", "Sevendi", "invalid_email", "088888");
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**Output:**
```
Invalid email address
```

---

#### **3. Overloaded Constructors**

You can define additional constructors with fewer parameters and call the primary constructor to initialize the record.

```java
public record Customer(String id, String name, String email, String phone) {
    // Overloaded constructor with 3 parameters
    public Customer(String id, String name, String email) {
        this(id, name, email, null); // Calls the primary constructor
    }

    // Overloaded constructor with 2 parameters
    public Customer(String id, String name) {
        this(id, name, null, null); // Calls the primary constructor
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Customer customer1 = new Customer("1", "Sevendi", "Sevendi@localhost");
        Customer customer2 = new Customer("2", "Budi");

        System.out.println(customer1);
        System.out.println(customer2);
    }
}
```

**Output:**
```
Customer[id=1, name=Sevendi, email=Sevendi@localhost, phone=null]
Customer[id=2, name=Budi, email=null, phone=null]
```

---

#### **4. Testing Constructors**

You can test the behavior of constructors using unit tests.

```java
import static org.junit.jupiter.api.Assertions.*;

public class CustomerTest {
    @Test
    void testPrimaryConstructor() {
        Customer customer = new Customer("1", "Sevendi", "Sevendi@localhost", "088888");
        assertEquals("1", customer.id());
        assertEquals("Sevendi", customer.name());
        assertEquals("Sevendi@localhost", customer.email());
        assertEquals("088888", customer.phone());
    }

    @Test
    void testOverloadedConstructor() {
        Customer customer = new Customer("1", "Sevendi", "Sevendi@localhost");
        assertEquals("1", customer.id());
        assertEquals("Sevendi", customer.name());
        assertEquals("Sevendi@localhost", customer.email());
        assertNull(customer.phone());
    }

    @Test
    void testCompactConstructorValidation() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            new Customer("", "Sevendi", "Sevendi@localhost", "088888");
        });
        assertEquals("ID cannot be null or blank", exception.getMessage());
    }
}
```

---

#### **5. Adding Default Values**

You can use overloaded constructors to provide default values for some properties.

```java
public record Product(String id, String name, double price, String category) {
    public Product(String id, String name, double price) {
        this(id, name, price, "General"); // Default category = "General"
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Product product1 = new Product("1", "Laptop", 1500.00, "Electronics");
        Product product2 = new Product("2", "Book", 10.00);

        System.out.println(product1);
        System.out.println(product2);
    }
}
```

**Output:**
```
Product[id=1, name=Laptop, price=1500.0, category=Electronics]
Product[id=2, name=Book, price=10.0, category=General]
```

---

#### **6. Combining Compact and Overloaded Constructors**

You can combine compact constructors with overloaded constructors for more flexibility.

```java
public record Order(String id, String product, int quantity) {
    public Order {
        if (quantity <= 0) {
            throw new IllegalArgumentException("Quantity must be greater than 0");
        }
    }

    public Order(String id, String product) {
        this(id, product, 1); // Default quantity = 1
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Order order1 = new Order("1", "Laptop", 2);
        Order order2 = new Order("2", "Book");

        System.out.println(order1);
        System.out.println(order2);
    }
}
```

**Output:**
```
Order[id=1, product=Laptop, quantity=2]
Order[id=2, product=Book, quantity=1]
```
