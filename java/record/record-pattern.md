### **Record Patterns in Java**

**Record Patterns** is a feature introduced in **Java 21** (after being previewed in earlier versions like Java 19). This feature simplifies the process of deconstructing records into their components while performing type checks. It allows developers to extract data from records in a concise and readable way, especially when combined with `instanceof` and other pattern-matching features.

---

### **1. What are Record Patterns?**

- **Purpose**: Record Patterns simplify the process of extracting components from a record without requiring manual casting or method calls.
- **How it works**: By combining **pattern matching** with `instanceof`, you can directly extract the components of a record into variables in a single step.
- **Key Benefit**: Eliminates boilerplate code for type-checking, casting, and component extraction.

---

### **2. Before Record Patterns**

Before the introduction of Record Patterns, the process of working with records required manual type-checking and casting. For example:

#### **Example: Without Record Patterns**

```java
public void printObject(Object object) {
    if (object instanceof Point) {
        Point point = (Point) object; // Manual casting
        System.out.println(point.x()); // Accessing record components
        System.out.println(point.y());
    } else {
        System.out.println(object);
    }
}
```

#### **Explanation**:
- You must first check if the object is an instance of `Point`.
- Then, you manually cast the object to the `Point` type.
- Finally, you access the components (`x` and `y`) using their getter methods.

---

### **3. With Record Patterns**

With Record Patterns, you can combine type-checking, casting, and component extraction into a single step.

#### **Example: Using Record Patterns**

```java
public void printObject(Object object) {
    if (object instanceof Point(int x, int y)) { // Record Pattern
        System.out.println(x); // Directly access components
        System.out.println(y);
    } else {
        System.out.println(object);
    }
}
```

#### **Explanation**:
- The `instanceof` keyword now matches against the `Point` record and deconstructs its components (`x` and `y`) into variables.
- No manual casting or method calls are required.

---

### **4. Key Features of Record Patterns**

1. **Deconstruction**:
   - Automatically extracts record components into variables.
   - Simplifies working with nested records.

2. **Pattern Matching with `instanceof`**:
   - Combines type-checking and deconstruction in a single step.

3. **Nested Patterns**:
   - Supports deconstruction of nested records or other patterns.

4. **Switch Expressions**:
   - Can be used in `switch` statements or expressions for cleaner and more readable code.

---

### **5. Examples of Record Patterns**

#### **Example 1: Basic Record Pattern**

```java
public record Point(int x, int y) {}

public void printObject(Object object) {
    if (object instanceof Point(int x, int y)) {
        System.out.println("Point coordinates: " + x + ", " + y);
    } else {
        System.out.println("Not a Point: " + object);
    }
}
```

**Output**:
```
Point coordinates: 10, 20
```

---

#### **Example 2: Nested Record Patterns**

```java
public record Point(int x, int y) {}
public record Rectangle(Point topLeft, Point bottomRight) {}

public void printRectangle(Object object) {
    if (object instanceof Rectangle(Point(int x1, int y1), Point(int x2, int y2))) {
        System.out.println("Rectangle corners: (" + x1 + ", " + y1 + ") to (" + x2 + ", " + y2 + ")");
    } else {
        System.out.println("Not a Rectangle: " + object);
    }
}
```

**Output**:
```
Rectangle corners: (0, 0) to (10, 10)
```

---

#### **Example 3: Using Record Patterns in `switch`**

```java
public record Point(int x, int y) {}
public record Circle(Point center, int radius) {}

public void handleShape(Object shape) {
    switch (shape) {
        case Point(int x, int y) -> System.out.println("Point at (" + x + ", " + y + ")");
        case Circle(Point(int x, int y), int radius) ->
            System.out.println("Circle with center (" + x + ", " + y + ") and radius " + radius);
        default -> System.out.println("Unknown shape: " + shape);
    }
}
```

**Output**:
```
Point at (5, 5)
Circle with center (10, 10) and radius 20
```

---

#### **Example 4: Combining Record Patterns with Guards**

```java
public record Point(int x, int y) {}

public void printPositivePoints(Object object) {
    if (object instanceof Point(int x, int y) && x > 0 && y > 0) {
        System.out.println("Positive Point: (" + x + ", " + y + ")");
    } else {
        System.out.println("Not a positive point: " + object);
    }
}
```

**Output**:
```
Positive Point: (10, 20)
Not a positive point: (-10, 20)
```

---

#### **Example 5: Pattern Matching with Collections**

You can use Record Patterns with collections to extract and process data.

```java
import java.util.List;

public record Point(int x, int y) {}

public void processShapes(List<Object> shapes) {
    for (Object shape : shapes) {
        if (shape instanceof Point(int x, int y)) {
            System.out.println("Point at (" + x + ", " + y + ")");
        } else {
            System.out.println("Unknown shape: " + shape);
        }
    }
}
```

**Input**:
```java
List<Object> shapes = List.of(new Point(1, 2), new Point(3, 4), "Not a Point");
```

**Output**:
```
Point at (1, 2)
Point at (3, 4)
Unknown shape: Not a Point
```

---

### **6. Advantages of Record Patterns**

1. **Conciseness**:
   - Reduces boilerplate code for type-checking, casting, and component extraction.

2. **Readability**:
   - Code is more intuitive and easier to understand.

3. **Safety**:
   - Eliminates the risk of casting errors by combining type-checking and deconstruction.

4. **Extensibility**:
   - Works seamlessly with nested records and complex data structures.

5. **Integration**:
   - Can be used with `switch` statements, guards, and other pattern-matching features.

---

### **7. Limitations of Record Patterns**

1. **Java 21 Requirement**:
   - Record Patterns require Java 21 or later.

2. **Record-Only**:
   - This feature works only with `record` types. It cannot be used with regular classes or other data types.

---

### **8. Summary**

| **Feature**               | **Description**                                                                 |
|----------------------------|---------------------------------------------------------------------------------|
| **Deconstruction**         | Automatically extracts components of a record into variables.                  |
| **Pattern Matching**       | Combines type-checking and deconstruction in a single step.                    |
| **Nested Patterns**        | Supports deconstruction of nested records or other patterns.                   |
| **Switch Integration**     | Can be used in `switch` statements for more concise and readable code.         |
| **Guards**                 | Allows adding conditions (guards) during pattern matching.                     |
| **Java Version**           | Introduced in Java 21 (previewed in Java 19).                                  |

