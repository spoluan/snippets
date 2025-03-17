### **Java Record: Overview**

A **Record** in Java is a special kind of class introduced in **Java 14 (as a preview feature)** and officially released in **Java 16**. It is designed to represent **immutable data** in a simpler and more concise way, reducing boilerplate code. 

#### **Key Characteristics of Java Records:**
1. **Immutable Data**:
   - Fields in a record are `final` by default, meaning their values cannot be changed after the object is created.
   - There are no setter methods in a record.

2. **Concise Syntax**:
   - Records automatically generate the following methods:
     - **Getters** for all fields (named after the fields).
     - **`equals()`** method.
     - **`hashCode()`** method.
     - **`toString()`** method.

3. **Final Class**:
   - A record is implicitly `final`, meaning it cannot be extended by another class.

4. **Compact Declaration**:
   - All fields are declared in the record header (e.g., `record Point(int x, int y)`).

5. **Not Suitable for Mutable Data**:
   - Records are specifically designed for immutable data. If you need mutable objects, use regular classes.

---

### **Syntax of Java Records**

```java
public record RecordName(FieldType1 field1, FieldType2 field2, ...) { }
```

Example:

```java
public record Point(int x, int y) { }
```

---

### **Features of Java Records**

1. **Automatic Method Generation**:
   - A record automatically generates:
     - **Constructor**: Initializes all fields.
     - **Getters**: For accessing field values.
     - **`equals()`**: Compares two records based on their field values.
     - **`hashCode()`**: Computes a hash code based on field values.
     - **`toString()`**: Provides a string representation of the record.

2. **Custom Methods**:
   - You can add custom methods to a record, but you can't add mutable fields or setters.

3. **Customizing Behavior**:
   - You can override the generated methods like `toString()`, `equals()`, or `hashCode()` if needed.

---

### **Examples of Java Records**

#### **1. Basic Example**

```java
public record Point(int x, int y) { }

public class Main {
    public static void main(String[] args) {
        Point point = new Point(10, 20);
        System.out.println("Point: " + point); // toString() is auto-generated
        System.out.println("X: " + point.x()); // Getter for x
        System.out.println("Y: " + point.y()); // Getter for y
    }
}
```

**Output:**
```
Point: Point[x=10, y=20]
X: 10
Y: 20
```

---

#### **2. Custom Methods in a Record**

```java
public record Rectangle(int length, int width) {
    public int area() {
        return length * width;
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Rectangle rectangle = new Rectangle(5, 10);
        System.out.println("Area: " + rectangle.area());
    }
}
```

**Output:**
```
Area: 50
```

---

#### **3. Overriding Methods in a Record**

```java
public record Person(String name, int age) {
    @Override
    public String toString() {
        return "Person[name=" + name + ", age=" + age + "]";
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Person person = new Person("Alice", 25);
        System.out.println(person); // Custom toString()
    }
}
```

**Output:**
```
Person[name=Alice, age=25]
```

---

#### **4. Record with Validation**

You can include validation logic in the constructor of a record.

```java
public record Employee(String name, double salary) {
    public Employee {
        if (salary < 0) {
            throw new IllegalArgumentException("Salary cannot be negative");
        }
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        try {
            Employee emp = new Employee("John", -5000);
        } catch (IllegalArgumentException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**Output:**
```
Salary cannot be negative
```

---

#### **5. Nested Records**

Records can be nested within other records or classes.

```java
public record Address(String street, String city) { }

public record Person(String name, Address address) { }

public class Main {
    public static void main(String[] args) {
        Address address = new Address("123 Street", "New York");
        Person person = new Person("Alice", address);

        System.out.println(person);
    }
}
```

**Output:**
```
Person[name=Alice, address=Address[street=123 Street, city=New York]]
```

---

#### **6. Using Records in Collections**

Records work seamlessly with collections like `List` or `Map`.

```java
import java.util.*;

public record Product(String name, double price) { }

public class Main {
    public static void main(String[] args) {
        List<Product> products = List.of(
            new Product("Laptop", 1500),
            new Product("Phone", 800)
        );

        products.forEach(System.out::println);
    }
}
```

**Output:**
```
Product[name=Laptop, price=1500.0]
Product[name=Phone, price=800.0]
```

---

### **Advantages of Java Records**

1. **Reduced Boilerplate**:
   - No need to manually write constructors, getters, `equals()`, `hashCode()`, and `toString()`.

2. **Immutability**:
   - Helps in creating thread-safe and predictable data models.

3. **Readable Code**:
   - Compact and clean syntax improves code readability.

4. **Built-in Methods**:
   - Auto-generated methods save development time and reduce errors.

---

### **Limitations of Java Records**

1. **No Mutability**:
   - Records are immutable. If you need mutable fields, use regular classes.

2. **No Inheritance**:
   - Records are implicitly `final` and cannot be extended.

3. **Limited Use Cases**:
   - Best suited for data transfer objects (DTOs) and value objects, not for complex business logic.

---

### **When to Use Java Records**

- When you need **immutable data**.
- For **DTOs** (Data Transfer Objects).
- For simple **value objects**.
- When you want to reduce boilerplate code in your application.

---

### **Comparison: Regular Class vs. Record**

#### Regular Class:

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Point)) return false;
        Point point = (Point) o;
        return x == point.x && y == point.y;
    }

    @Override
    public int hashCode() {
        return Objects.hash(x, y);
    }

    @Override
    public String toString() {
        return "Point{" + "x=" + x + ", y=" + y + '}';
    }
}
```

#### Record:

```java
public record Point(int x, int y) { }
```

