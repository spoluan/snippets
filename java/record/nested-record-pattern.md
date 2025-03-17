### **Nested Record Patterns in Java**

**Nested Record Patterns** is an extension of the **Record Patterns** feature in Java. It allows developers to deconstruct nested records (records containing other records as components) in a concise and readable manner. This feature is especially useful when working with complex data structures, as it eliminates the need for manual type-checking and casting.

---

### **1. What are Nested Record Patterns?**

- **Definition**: A **Nested Record Pattern** is a pattern within a record pattern that matches and extracts components from a nested record.
- **Purpose**: Simplifies the process of accessing and working with deeply nested data structures.
- **Key Benefit**: Reduces boilerplate code and improves readability when working with nested records.

---

### **2. Example of a Nested Record**

Consider the following records:

```java
public record Point(int x, int y) {}
public record Line(Point start, Point end) {}
```

Here:
- `Line` is a record that contains two `Point` records (`start` and `end`) as its components.
- This is an example of a **nested record** structure.

---

### **3. Accessing Nested Records Without Nested Record Patterns**

Before the introduction of Nested Record Patterns, accessing components of a nested record required manual type-checking and casting:

#### **Example: Without Nested Record Patterns**

```java
public void printObject(Object object) {
    if (object instanceof Line) {
        Line line = (Line) object; // Manual casting
        Point start = line.start();
        Point end = line.end();
        
        System.out.println("Start: (" + start.x() + ", " + start.y() + ")");
        System.out.println("End: (" + end.x() + ", " + end.y() + ")");
    } else {
        System.out.println("Not a Line: " + object);
    }
}
```

#### **Explanation**:
- You must manually cast the `object` to `Line`.
- Then, extract the `start` and `end` components, and further extract their `x` and `y` values.

---

### **4. Accessing Nested Records With Nested Record Patterns**

With **Nested Record Patterns**, you can match and deconstruct nested records directly, without manual type-checking or casting.

#### **Example: Using Nested Record Patterns**

```java
public void printObject(Object object) {
    if (object instanceof Line(Point(int x1, int y1), Point(int x2, int y2))) {
        System.out.println("Start: (" + x1 + ", " + y1 + ")");
        System.out.println("End: (" + x2 + ", " + y2 + ")");
    } else {
        System.out.println("Not a Line: " + object);
    }
}
```

#### **Explanation**:
- The `Line` record is matched and deconstructed into its `start` and `end` components.
- The `Point` records (`start` and `end`) are further deconstructed into their `x` and `y` components in a single step.

---

### **5. Key Features of Nested Record Patterns**

1. **Deconstruction of Nested Records**:
   - Automatically extracts components from nested records.

2. **Improved Readability**:
   - Simplifies working with deeply nested data structures.

3. **Pattern Matching**:
   - Combines type-checking, casting, and deconstruction into a single step.

4. **Integration with Guards**:
   - Supports additional conditions (guards) during pattern matching.

5. **Switch Expressions**:
   - Can be used in `switch` statements or expressions for cleaner code.

---

### **6. Examples of Nested Record Patterns**

#### **Example 1: Basic Nested Record Pattern**

```java
public record Point(int x, int y) {}
public record Line(Point start, Point end) {}

public void printLine(Object object) {
    if (object instanceof Line(Point(int x1, int y1), Point(int x2, int y2))) {
        System.out.println("Start: (" + x1 + ", " + y1 + ")");
        System.out.println("End: (" + x2 + ", " + y2 + ")");
    } else {
        System.out.println("Not a Line: " + object);
    }
}
```

**Output**:
```
Start: (0, 0)
End: (10, 10)
```

---

#### **Example 2: Nested Record Patterns with Guards**

```java
public record Point(int x, int y) {}
public record Line(Point start, Point end) {}

public void printPositiveLine(Object object) {
    if (object instanceof Line(Point(int x1, int y1), Point(int x2, int y2))
            && x1 > 0 && y1 > 0 && x2 > 0 && y2 > 0) {
        System.out.println("Positive Line: Start (" + x1 + ", " + y1 + "), End (" + x2 + ", " + y2 + ")");
    } else {
        System.out.println("Not a Positive Line: " + object);
    }
}
```

**Input**:
```java
Line line = new Line(new Point(1, 2), new Point(3, 4));
```

**Output**:
```
Positive Line: Start (1, 2), End (3, 4)
```

---

#### **Example 3: Switch Expression with Nested Record Patterns**

```java
public record Point(int x, int y) {}
public record Line(Point start, Point end) {}

public void handleShape(Object shape) {
    switch (shape) {
        case Point(int x, int y) -> System.out.println("Point at (" + x + ", " + y + ")");
        case Line(Point(int x1, int y1), Point(int x2, int y2)) ->
            System.out.println("Line from (" + x1 + ", " + y1 + ") to (" + x2 + ", " + y2 + ")");
        default -> System.out.println("Unknown shape: " + shape);
    }
}
```

**Input**:
```java
Object shape1 = new Point(5, 5);
Object shape2 = new Line(new Point(0, 0), new Point(10, 10));
```

**Output**:
```
Point at (5, 5)
Line from (0, 0) to (10, 10)
```

---

#### **Example 4: Handling Collections with Nested Record Patterns**

```java
import java.util.List;

public record Point(int x, int y) {}
public record Line(Point start, Point end) {}

public void processShapes(List<Object> shapes) {
    for (Object shape : shapes) {
        if (shape instanceof Line(Point(int x1, int y1), Point(int x2, int y2))) {
            System.out.println("Line from (" + x1 + ", " + y1 + ") to (" + x2 + ", " + y2 + ")");
        } else if (shape instanceof Point(int x, int y)) {
            System.out.println("Point at (" + x + ", " + y + ")");
        } else {
            System.out.println("Unknown shape: " + shape);
        }
    }
}
```

**Input**:
```java
List<Object> shapes = List.of(
    new Point(1, 2),
    new Line(new Point(0, 0), new Point(3, 4)),
    "Not a Shape"
);
```

**Output**:
```
Point at (1, 2)
Line from (0, 0) to (3, 4)
Unknown shape: Not a Shape
```

---

### **7. Advantages of Nested Record Patterns**

1. **Conciseness**:
   - Reduces boilerplate code for nested record deconstruction.

2. **Readability**:
   - Makes complex data structures easier to understand and work with.

3. **Safety**:
   - Combines type-checking, casting, and deconstruction, reducing the risk of errors.

4. **Versatility**:
   - Works seamlessly with guards, `switch` expressions, and collections.

---

### **8. Limitations of Nested Record Patterns**

1. **Java 21 Requirement**:
   - Nested Record Patterns require Java 21 or later.

2. **Record-Only**:
   - This feature works only with `record` types and not with regular classes.

---

### **9. Summary**

| **Feature**                     | **Description**                                                                 |
|----------------------------------|---------------------------------------------------------------------------------|
| **Deconstruction of Nested Records** | Extracts components from nested records in a single step.                      |
| **Pattern Matching**             | Combines type-checking, casting, and deconstruction into one concise operation. |
| **Guards**                       | Supports additional conditions during pattern matching.                         |
| **Switch Integration**           | Can be used in `switch` statements for cleaner code.                            |
| **Java Version**                 | Introduced in Java 21.                                                          |
