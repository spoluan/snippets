### **Primitive Streams in Java**

Java provides specialized stream classes for primitive data types like `int`, `long`, and `double`. These are called **Primitive Streams**, and they are optimized for handling primitive data types without the overhead of boxing/unboxing.

---

### **Why Use Primitive Streams?**

1. **Efficiency**:
   - Avoids the overhead of boxing and unboxing when working with primitive types.
   - More memory-efficient compared to generic `Stream<T>`.

2. **Specialized Operations**:
   - Provides methods specifically designed for primitive types, such as `sum()`, `average()`, `range()`, etc.

---

### **Primitive Stream Classes**

| **Class**                  | **Description**                  |
|----------------------------|----------------------------------|
| `java.util.stream.IntStream` | Stream for `int` data type       |
| `java.util.stream.LongStream`| Stream for `long` data type      |
| `java.util.stream.DoubleStream`| Stream for `double` data type   |

---

### **Primitive Stream Operations**

1. **Available Operators**:
   - Primitive streams support most of the operators available in regular streams.
   - Specialized aggregate methods like `sum()`, `average()`, and `max()` are provided for simplicity.

2. **Simplified Aggregation**:
   - No need for comparators or custom collectors for common operations.

3. **Stream Creation**:
   - Primitive streams can be created using static methods like:
     - `IntStream.of(...)`
     - `IntStream.range(...)`
     - `IntStream.builder()`

---

### **Code Example: Creating Primitive Streams**

```java
@Test
void testCreatePrimitiveStream() {
    // Create a stream from specific values
    IntStream intStream = IntStream.of(1, 2, 3, 4, 5);
    
    // Create a range of integers (1 to 9)
    IntStream range = IntStream.range(1, 10);
    
    // Using a builder to create a stream
    IntStream.Builder builder = IntStream.builder();
    builder.add(1).add(2).add(3);
    IntStream builtStream = builder.build();
}
```

---

### **Common Operations on Primitive Streams**

#### 1. **Aggregate Operations**
```java
IntStream intStream = IntStream.of(1, 2, 3, 4, 5);

// Sum of elements
int sum = intStream.sum(); // Output: 15

// Average of elements
OptionalDouble average = IntStream.of(1, 2, 3, 4, 5).average();
System.out.println(average.orElse(0.0)); // Output: 3.0
```

#### 2. **Range and Iteration**
```java
// Range of integers from 1 to 10 (exclusive)
IntStream.range(1, 10)
         .forEach(System.out::println);

// Output: 1 2 3 4 5 6 7 8 9
```

#### 3. **Mapping and Filtering**
```java
IntStream.range(1, 10)
         .filter(n -> n % 2 == 0) // Keep only even numbers
         .map(n -> n * n)         // Square each number
         .forEach(System.out::println);

// Output: 4 16 36 64
```

---

### **Key Advantages of Primitive Streams**

1. **Specialized Methods**:
   - Methods like `sum()`, `average()`, and `range()` simplify common operations.

2. **Memory Efficiency**:
   - Eliminates the need for boxing/unboxing, making operations faster and less memory-intensive.

3. **Readable Code**:
   - Direct methods for common tasks like summing or finding averages make the code cleaner.

