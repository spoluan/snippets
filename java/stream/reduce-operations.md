### **Manual Aggregation Using `reduce` in Java Streams**

The `reduce` operation in Java Streams is a powerful tool that allows you to manually perform aggregation operations. It is a terminal operation that combines elements of a stream into a single result by repeatedly applying a combining function.

---

### **Key Points**

1. **Purpose**:
   - The `reduce` method is used for custom aggregation operations.
   - It is similar to the "fold" operation in other programming languages.

2. **Common Use Cases**:
   - Summing numbers.
   - Concatenating strings.
   - Finding the product of numbers.
   - Combining complex objects.

---

### **Syntax of `reduce`**

The `reduce` method has three common variations:
1. **Identity and Accumulator**:
   ```java
   T reduce(T identity, BinaryOperator<T> accumulator);
   ```
   - **Identity**: The initial value for the reduction (e.g., `0` for sum, `1` for product).
   - **Accumulator**: A function that combines two elements.

2. **Accumulator Without Identity**:
   ```java
   Optional<T> reduce(BinaryOperator<T> accumulator);
   ```
   - Returns an `Optional` because the stream might be empty.

3. **Identity, Accumulator, and Combiner** (used in parallel streams):
   ```java
   <U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);
   ```

---

### **Examples**

#### 1. **Summing Numbers Using `reduce`**
```java
@Test
void testSumUsingReduce() {
    List<Integer> numbers = List.of(1, 2, 3, 4, 5);

    // Using identity and accumulator
    int sum = numbers.stream()
                     .reduce(0, Integer::sum);
    System.out.println("Sum: " + sum); // Output: Sum: 15
}
```

#### 2. **Finding the Product of Numbers**
```java
@Test
void testProductUsingReduce() {
    List<Integer> numbers = List.of(1, 2, 3, 4, 5);

    // Using identity and accumulator
    int product = numbers.stream()
                         .reduce(1, (a, b) -> a * b);
    System.out.println("Product: " + product); // Output: Product: 120
}
```

#### 3. **Concatenating Strings**
```java
@Test
void testConcatenateStringsUsingReduce() {
    List<String> words = List.of("Java", "Stream", "Reduce");

    // Using identity and accumulator
    String concatenated = words.stream()
                                .reduce("", (a, b) -> a + " " + b);
    System.out.println("Concatenated: " + concatenated.trim()); 
    // Output: Concatenated: Java Stream Reduce
}
```

#### 4. **Finding Maximum Value**
```java
@Test
void testFindMaxUsingReduce() {
    List<Integer> numbers = List.of(10, 20, 30, 5, 15);

    // Without identity
    Optional<Integer> max = numbers.stream()
                                   .reduce(Integer::max);
    System.out.println("Max: " + max.orElse(0)); // Output: Max: 30
}
```

#### 5. **Combining Custom Objects**
```java
class Person {
    String name;
    int age;

    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

@Test
void testCombineObjectsUsingReduce() {
    List<Person> people = List.of(
        new Person("Eko", 30),
        new Person("Budi", 25),
        new Person("Joko", 35)
    );

    // Combine ages into a single sum
    int totalAge = people.stream()
                         .map(person -> person.age)
                         .reduce(0, Integer::sum);
    System.out.println("Total Age: " + totalAge); // Output: Total Age: 90
}
```

---

### **How `reduce` Works**

- **Step-by-step Example** (Summing Numbers):
  ```java
  List<Integer> numbers = List.of(1, 2, 3, 4, 5);

  int sum = numbers.stream()
                   .reduce(0, (a, b) -> a + b);
  ```
  - **Initial Value**: `0` (identity).
  - **First Step**: `0 + 1 = 1`.
  - **Second Step**: `1 + 2 = 3`.
  - **Third Step**: `3 + 3 = 6`.
  - **Fourth Step**: `6 + 4 = 10`.
  - **Fifth Step**: `10 + 5 = 15`.

---

### **Key Considerations**

1. **Identity**:
   - The identity value should be the neutral element for the operation:
     - `0` for addition.
     - `1` for multiplication.
     - `""` for string concatenation.

2. **Empty Streams**:
   - If the stream is empty:
     - With an identity: The result is the identity value.
     - Without an identity: The result is an empty `Optional`.

3. **Parallel Streams**:
   - Use the third variation of `reduce` with a combiner for parallel streams.

4. **Performance**:
   - `reduce` can be less efficient than specialized methods like `sum()` or `max()` for simple operations.

---

### **Comparison with Aggregate Operations**

| **Feature**          | **Aggregate Methods**               | **Reduce**                     |
|-----------------------|--------------------------------------|---------------------------------|
| **Purpose**           | Predefined operations (e.g., `sum`) | Custom aggregation logic       |
| **Flexibility**       | Limited to predefined operations    | Highly flexible                |
| **Ease of Use**       | Easier, straightforward            | Requires more manual logic     |
| **Performance**       | Optimized for specific use cases    | May be less efficient          |

