### **Generics in Java Records**

Java Records, introduced in **Java 14 (preview)** and finalized in **Java 16**, support **Generics** just like regular classes. This allows records to be more flexible by enabling them to handle data of different types while maintaining type safety.

Generics are especially useful in records when the data type is not fixed but should still be type-safe. For example, a record can represent a wrapper for any type of object by using generics.

---

### **1. Key Features of Generics in Java Records**

1. **Generic Declaration**:
   - A record can declare type parameters using angle brackets (`<>`) just like a class or interface.
   - Example: `public record Data<T>(T data)`.

2. **Type Safety**:
   - Generics enforce type safety at compile time, ensuring that only the specified type can be used with the record.

3. **Multiple Type Parameters**:
   - A record can have multiple type parameters, such as `T`, `U`, etc.
   - Example: `public record Pair<T, U>(T first, U second)`.

4. **Bounded Type Parameters**:
   - You can restrict the type parameter to a specific type or its subclasses using bounds.
   - Example: `public record Box<T extends Number>(T value)`.

5. **Generic Methods**:
   - Records can also define generic methods in addition to having generic type parameters.

---

### **2. Basic Example of Generic Record**

#### **Generic Record Definition**

```java
public record Data<T>(T data) {}
```

#### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        // Using String as the type parameter
        Data<String> stringData = new Data<>("Hello, Generics!");
        System.out.println("String data: " + stringData.data());

        // Using Integer as the type parameter
        Data<Integer> integerData = new Data<>(42);
        System.out.println("Integer data: " + integerData.data());
    }
}
```

#### **Output**:
```
String data: Hello, Generics!
Integer data: 42
```

---

### **3. Testing Generic Records**

#### **JUnit Test Example**

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class DataTest {
    @Test
    void generic() {
        // Test with String type
        var stringData = new Data<String>("Sevendi");
        assertEquals("Sevendi", stringData.data());

        // Test with Integer type
        var integerData = new Data<Integer>(100);
        assertEquals(100, integerData.data());
    }
}
```

#### **Explanation**:
- The test ensures that the `Data` record can handle different types (`String` and `Integer`) while maintaining type safety.

---

### **4. Record with Multiple Generic Parameters**

#### **Definition**

```java
public record Pair<T, U>(T first, U second) {}
```

#### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        // Pair of String and Integer
        Pair<String, Integer> pair = new Pair<>("Age", 30);
        System.out.println("Key: " + pair.first() + ", Value: " + pair.second());

        // Pair of Double and Boolean
        Pair<Double, Boolean> pair2 = new Pair<>(99.99, true);
        System.out.println("Key: " + pair2.first() + ", Value: " + pair2.second());
    }
}
```

#### **Output**:
```
Key: Age, Value: 30
Key: 99.99, Value: true
```

---

### **5. Bounded Type Parameters**

#### **Definition**

You can restrict the type parameter to a specific type or its subclasses using the `extends` keyword.

```java
public record Box<T extends Number>(T value) {}
```

#### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        // Valid: Integer is a subclass of Number
        Box<Integer> intBox = new Box<>(10);
        System.out.println("Integer Box: " + intBox.value());

        // Valid: Double is a subclass of Number
        Box<Double> doubleBox = new Box<>(3.14);
        System.out.println("Double Box: " + doubleBox.value());

        // Invalid: String is not a subclass of Number
        // Box<String> stringBox = new Box<>("Hello"); // Compilation error
    }
}
```

#### **Output**:
```
Integer Box: 10
Double Box: 3.14
```

---

### **6. Generic Methods in Records**

Records can also define generic methods to add flexibility.

#### **Definition**

```java
public record Wrapper<T>(T value) {
    public <U> U transform(java.util.function.Function<T, U> function) {
        return function.apply(value);
    }
}
```

#### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Wrapper<String> wrapper = new Wrapper<>("Hello");

        // Transform the value to its length
        int length = wrapper.transform(String::length);
        System.out.println("Length: " + length);

        // Transform the value to uppercase
        String upper = wrapper.transform(String::toUpperCase);
        System.out.println("Uppercase: " + upper);
    }
}
```

#### **Output**:
```
Length: 5
Uppercase: HELLO
```

---

### **7. Nested Generic Records**

You can nest generic records within other records or classes.

#### **Definition**

```java
public record Result<T>(T value, String message) {}
```

#### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Result<Integer> success = new Result<>(200, "OK");
        Result<String> error = new Result<>("404", "Not Found");

        System.out.println("Success: " + success.value() + ", Message: " + success.message());
        System.out.println("Error: " + error.value() + ", Message: " + error.message());
    }
}
```

#### **Output**:
```
Success: 200, Message: OK
Error: 404, Message: Not Found
```

---

### **8. Generic Records with Collections**

Generic records can also work with collections like `List`, `Set`, or `Map`.

#### **Definition**

```java
import java.util.List;

public record CollectionWrapper<T>(List<T> items) {}
```

#### **Usage**

```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        CollectionWrapper<String> stringList = new CollectionWrapper<>(List.of("A", "B", "C"));
        System.out.println("Items: " + stringList.items());

        CollectionWrapper<Integer> intList = new CollectionWrapper<>(List.of(1, 2, 3));
        System.out.println("Items: " + intList.items());
    }
}
```

#### **Output**:
```
Items: [A, B, C]
Items: [1, 2, 3]
```

---

### **9. Summary**

| Feature                      | Example                                                                 |
|------------------------------|-------------------------------------------------------------------------|
| **Single Generic Parameter** | `public record Data<T>(T data) {}`                                      |
| **Multiple Generic Parameters** | `public record Pair<T, U>(T first, U second) {}`                      |
| **Bounded Type Parameters**  | `public record Box<T extends Number>(T value) {}`                      |
| **Generic Methods**          | `public <U> U transform(Function<T, U> function) {}`                   |
| **Nested Generics**          | `public record Result<T>(T value, String message) {}`                  |
| **Collections with Generics**| `public record CollectionWrapper<T>(List<T> items) {}`                 |

