### **Compact Constructor in Java Records**

A **Compact Constructor** in Java Records is a simplified way to define a constructor without explicitly declaring its parameters. It is useful when you want to add custom logic (like validation or logging) during object creation but do not need to modify or transform the property values.

---

### **Key Features of Compact Constructor**

1. **No Need to Declare Parameters**:
   - Unlike the canonical constructor, you do not need to explicitly declare the parameters.
   - The parameters are implicitly available based on the fields defined in the record header.

2. **Automatic Initialization**:
   - The compact constructor automatically assigns the values from the parameters to the record fields.
   - You do not need to explicitly initialize the fields unless you want to override or transform them.

3. **Use Cases**:
   - Adding validation logic for the record fields.
   - Logging or debugging during object creation.
   - Keeping the constructor code concise.

4. **Simpler than Canonical Constructor**:
   - If you do not need to modify the property values, the compact constructor is more concise and easier to use compared to the canonical constructor.

---

### **Examples of Compact Constructor**

#### **1. Basic Compact Constructor**

In this example, the compact constructor is used to log a message when an object is created.

```java
public record Point(int x, int y) {
    public Point {
        System.out.println("Create Point");
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Point point = new Point(10, 20);
        System.out.println(point);
    }
}
```

**Output**:
```
Create Point
Point[x=10, y=20]
```

---

#### **2. Compact Constructor with Validation**

You can use a compact constructor to validate the input values.

```java
public record Rectangle(int width, int height) {
    public Rectangle {
        if (width <= 0 || height <= 0) {
            throw new IllegalArgumentException("Width and height must be greater than 0");
        }
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        try {
            Rectangle rectangle = new Rectangle(10, -5);
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**Output**:
```
Width and height must be greater than 0
```

---

#### **3. Compact Constructor with Normalization**

You can normalize or preprocess the input values in the compact constructor.

```java
public record Customer(String name, String email) {
    public Customer {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("Name cannot be null or blank");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email address");
        }

        // Normalize email to lowercase
        email = email.toLowerCase();
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("Sevendi", "Sevendi@LOCALHOST");
        System.out.println(customer);
    }
}
```

**Output**:
```
Customer[name=Sevendi, email=Sevendi@localhost]
```

---

#### **4. Compact Constructor with Logging and Validation**

You can combine logging and validation in the compact constructor.

```java
public record Product(String id, String name, double price) {
    public Product {
        System.out.println("Creating Product...");
        if (id == null || id.isBlank()) {
            throw new IllegalArgumentException("ID cannot be null or blank");
        }
        if (price < 0) {
            throw new IllegalArgumentException("Price cannot be negative");
        }
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Product product = new Product("1", "Laptop", 1500.00);
        System.out.println(product);
    }
}
```

**Output**:
```
Creating Product...
Product[id=1, name=Laptop, price=1500.0]
```

---

#### **5. Compact Constructor with Derived Properties**

You can derive additional properties based on the input fields.

```java
public record Circle(double radius, double area) {
    public Circle {
        if (radius <= 0) {
            throw new IllegalArgumentException("Radius must be greater than 0");
        }
        area = Math.PI * radius * radius; // Calculate area based on radius
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Circle circle = new Circle(5);
        System.out.println(circle);
    }
}
```

**Output**:
```
Circle[radius=5.0, area=78.53981633974483]
```

---

#### **6. Compact Constructor with Unit Tests**

You can write unit tests to verify the behavior of the compact constructor.

```java
import static org.junit.jupiter.api.Assertions.*;

public class PointTest {
    @Test
    void testCompactConstructor() {
        Point point = new Point(10, 20);

        assertEquals(10, point.x());
        assertEquals(20, point.y());
    }

    @Test
    void testValidationInCompactConstructor() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            new Rectangle(-10, 20);
        });
        assertEquals("Width and height must be greater than 0", exception.getMessage());
    }
}
```

---

### **When to Use Compact Constructor**

- Use a **canonical constructor** when you need to explicitly declare parameters or perform transformations on the input values.
- Use a **compact constructor** when:
  - You do not need to modify or transform the property values.
  - You only want to add validation or logging.
  - You want to keep the constructor code concise and simple.

---

### **Summary**

1. **What is a Compact Constructor?**
   - A constructor in a record that does not explicitly declare parameters.
   - Automatically initializes the record fields using the parameter values.

2. **Why Use Compact Constructor?**
   - To add validation, logging, or preprocessing logic without explicitly declaring parameters.
   - To simplify the constructor code.

3. **Key Features**:
   - No explicit parameter declaration.
   - Automatic field initialization.
   - Ideal for adding validation or logging.

4. **Examples of Use Cases**:
   - Logging during object creation.
   - Validating input values.
   - Normalizing or preprocessing input values.

