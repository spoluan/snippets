### **Static in Java Records**

In Java, the `static` keyword is used to define members (fields and methods) that belong to the **class** rather than any specific instance of the class. Static members are shared across all instances of the class. This principle also applies to **Java Records**, which are immutable data classes.

Although records are designed to represent immutable data, they can still have `static` fields and methods. This is because static members are associated with the class itself, not its instances.

---

### **1. Key Features of Static in Java Records**

1. **Static Fields and Methods Are Allowed**:
   - Records can define `static` fields and methods just like regular classes.
   - Static members are not tied to any specific instance of the record.

2. **Static Members Are Immutable**:
   - Since records emphasize immutability, it's common to use `static` fields for constants or shared immutable values.

3. **Static Members Cannot Access Instance Fields**:
   - Static methods and fields cannot directly access instance fields (`x`, `y`, etc.) because they are not tied to any specific record instance.

4. **Use Cases**:
   - Defining constants (e.g., `ZERO` point in a 2D space).
   - Factory methods for creating instances of the record.

---

### **2. Example of Static Members in a Record**

#### **Defining Static Members**

```java
public record Point(int x, int y) {
    // Constructor
    public Point {
        System.out.println("Create Point");
    }

    // Static field
    public static final Point ZERO = new Point(0, 0);

    // Static method
    public static Point create(int x, int y) {
        return new Point(x, y);
    }
}
```

#### **Explanation**:
1. **Static Field**:
   - `ZERO` is a constant shared across all instances of `Point`.
   - It represents a point at the origin `(0, 0)`.

2. **Static Method**:
   - `create(int x, int y)` is a factory method for creating new `Point` instances.

3. **Constructor**:
   - A custom constructor is used to print a message whenever a new `Point` instance is created.

---

### **3. Testing Static Members in a Record**

#### **JUnit Test Example**

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class PointTest {
    @Test
    void staticMember() {
        // Access static field
        assertEquals(0, Point.ZERO.x());
        assertEquals(0, Point.ZERO.y());

        // Use static method to create a new Point
        var point = Point.create(10, 10);
        assertEquals(10, point.x());
        assertEquals(10, point.y());
    }
}
```

#### **Output**:
```
Create Point
Create Point
```

**Explanation**:
- The `ZERO` field is accessed directly as `Point.ZERO`.
- The `create` method is used to generate a new `Point` instance.

---

### **4. Static Methods in Java Records**

Static methods in records are often used for:
1. **Factory Methods**:
   - To create instances of the record with specific logic.
2. **Utility Methods**:
   - To perform operations related to the record without requiring an instance.

#### **Example: Factory Method**

```java
public record Circle(double radius) {
    public static Circle unitCircle() {
        return new Circle(1.0); // A circle with radius 1
    }
}
```

#### **Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Circle unit = Circle.unitCircle();
        System.out.println("Radius: " + unit.radius());
    }
}
```

**Output**:
```
Radius: 1.0
```

---

### **5. Static Fields in Java Records**

Static fields are commonly used to store constants or shared values.

#### **Example: Constants**

```java
public record Color(String name, String hex) {
    public static final Color RED = new Color("Red", "#FF0000");
    public static final Color GREEN = new Color("Green", "#00FF00");
    public static final Color BLUE = new Color("Blue", "#0000FF");
}
```

#### **Usage**:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Color: " + Color.RED.name() + ", Hex: " + Color.RED.hex());
    }
}
```

**Output**:
```
Color: Red, Hex: #FF0000
```

---

### **6. Static Blocks in Java Records**

Static blocks are used to initialize static fields or execute static logic when the class is loaded.

#### **Example: Static Block**

```java
public record Config(String key, String value) {
    public static final Config DEFAULT;

    static {
        System.out.println("Loading default configuration...");
        DEFAULT = new Config("defaultKey", "defaultValue");
    }
}
```

#### **Usage**:

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Key: " + Config.DEFAULT.key() + ", Value: " + Config.DEFAULT.value());
    }
}
```

**Output**:
```
Loading default configuration...
Key: defaultKey, Value: defaultValue
```

---

### **7. Static and Instance Members in Records**

Static members cannot directly access instance members because they are not tied to a specific instance.

#### **Incorrect Example**:

```java
public record Point(int x, int y) {
    public static String describe() {
        return "Point at (" + x + ", " + y + ")"; // Error: Cannot access instance fields
    }
}
```

#### **Correct Example**:

```java
public record Point(int x, int y) {
    public static String describe(Point point) {
        return "Point at (" + point.x() + ", " + point.y() + ")";
    }
}
```

#### **Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Point p = new Point(5, 7);
        System.out.println(Point.describe(p));
    }
}
```

**Output**:
```
Point at (5, 7)
```

---

### **8. Summary**

| Feature                  | Behavior in Records                                                                 |
|--------------------------|-------------------------------------------------------------------------------------|
| **Static Fields**        | Allowed; used for constants or shared values.                                       |
| **Static Methods**       | Allowed; used for factory methods, utility methods, or static logic.                |
| **Static Blocks**        | Allowed; used for initializing static fields or executing static logic.             |
| **Accessing Instance Fields** | Not allowed directly from static methods/fields.                                |
| **Use Cases**            | Factory methods, constants, shared configurations, and utility functions.           |

