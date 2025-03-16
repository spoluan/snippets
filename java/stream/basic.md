### **Streams in Java**

Java **Streams** are a powerful feature introduced in **Java 8** as part of the Java Streams API. They provide a functional programming approach to process collections of data (like arrays, lists, sets, etc.) in a declarative and efficient way.

---

### **Key Features of Streams**
1. **Declarative**: Focus on *what* to do, not *how* to do it.
2. **Lazy Evaluation**: Operations are not executed until a terminal operation is invoked.
3. **Functional Programming**: Supports lambda expressions and method references.
4. **Parallelism**: Easy to process data in parallel using `parallelStream()`.
5. **Immutable**: Streams do not modify the original data source.

---

### **Types of Stream Operations**
Stream operations are divided into two categories:
1. **Intermediate Operations**: Transform the stream into another stream (lazy).
   - Examples: `filter()`, `map()`, `sorted()`, `distinct()`
2. **Terminal Operations**: Produce a result or a side-effect (eager).
   - Examples: `forEach()`, `collect()`, `reduce()`, `count()`

---

### **Creating Streams**

#### **1. Empty or Single Stream**
```java
Stream<String> stream1 = Stream.of("Sevendi"); // Single element stream
Stream<String> stream2 = Stream.empty();  // Empty stream
Stream<String> stream3 = Stream.ofNullable(null); // Null-safe stream
```

#### **2. From Arrays**
```java
Stream<String> streamString = Stream.of("Sevendi", "Eldrige", "Rifki");
Stream<Integer> streamInteger = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
Stream<String> streamArray = Arrays.stream(new String[]{"Sevendi", "Eldrige", "Rifki"});
```

#### **3. From Collections**
```java
Collection<String> collectionString = List.of("Sevendi", "Eldrige", "Rifki");
Stream<String> streamString = collectionString.stream();
```

#### **4. Infinite Streams**
```java
Stream<String> stream1 = Stream.generate(() -> "Sevendi"); // Infinite stream with constant value
Stream<String> stream2 = Stream.iterate("Sevendi", value -> value.toUpperCase()); // Infinite stream with transformation
```

#### **5. Using Stream Builder**
```java
Stream.Builder<String> builder = Stream.builder();
builder.accept("Sevendi");
builder.add("Eldrige").add("Rifki");
Stream<String> stream = builder.build();
stream.forEach(System.out::println);
```

---

### **Running a Stream**

Streams are **cold** by default, meaning they don't process data until a terminal operation is invoked. For example:
```java
Stream<String> stream = Stream.of("Sevendi", "Eldrige", "Rifki");
stream.forEach(value -> System.out.println(value)); // Prints each element
```

Key points:
- Streams can only be consumed once. Once a terminal operation is called, the stream cannot be reused.

---

### **Stream Operations**

#### **1. Intermediate Operations**

- **`filter()`**: Filters elements based on a condition.
  ```java
  List<String> names = List.of("Sevendi", "Eldrige", "Rifki");
  names.stream()
       .filter(name -> name.startsWith("K"))
       .forEach(System.out::println); // Output: Eldrige, Rifki
  ```

- **`map()`**: Transforms each element.
  ```java
  List<String> names = List.of("Sevendi", "Eldrige", "Rifki");
  names.stream()
       .map(String::toUpperCase)
       .forEach(System.out::println); // Output: Sevendi, Eldrige, Rifki
  ```

- **`sorted()`**: Sorts elements.
  ```java
  List<Integer> numbers = List.of(5, 1, 3, 2, 4);
  numbers.stream()
         .sorted()
         .forEach(System.out::println); // Output: 1, 2, 3, 4, 5
  ```

- **`distinct()`**: Removes duplicate elements.
  ```java
  List<Integer> numbers = List.of(1, 2, 2, 3, 4, 4, 5);
  numbers.stream()
         .distinct()
         .forEach(System.out::println); // Output: 1, 2, 3, 4, 5
  ```

- **`limit()` and `skip()`**: Limit the number of elements or skip elements.
  ```java
  Stream<Integer> numbers = Stream.iterate(1, n -> n + 1);
  numbers.skip(5).limit(3).forEach(System.out::println); // Output: 6, 7, 8
  ```

#### **2. Terminal Operations**

- **`forEach()`**: Performs an action for each element.
  ```java
  List<String> names = List.of("Sevendi", "Eldrige", "Rifki");
  names.stream().forEach(System.out::println);
  ```

- **`collect()`**: Collects elements into a collection.
  ```java
  List<String> names = List.of("Sevendi", "Eldrige", "Rifki");
  List<String> result = names.stream()
                             .filter(name -> name.startsWith("K"))
                             .collect(Collectors.toList());
  System.out.println(result); // Output: [Eldrige, Rifki]
  ```

- **`reduce()`**: Reduces elements to a single value.
  ```java
  List<Integer> numbers = List.of(1, 2, 3, 4, 5);
  int sum = numbers.stream()
                   .reduce(0, Integer::sum);
  System.out.println(sum); // Output: 15
  ```

- **`count()`**: Counts the number of elements.
  ```java
  List<String> names = List.of("Sevendi", "Eldrige", "Rifki");
  long count = names.stream().count();
  System.out.println(count); // Output: 3
  ```

- **`anyMatch()`, `allMatch()`, `noneMatch()`**: Checks conditions.
  ```java
  List<Integer> numbers = List.of(1, 2, 3, 4, 5);
  boolean anyMatch = numbers.stream().anyMatch(n -> n > 3); // true
  boolean allMatch = numbers.stream().allMatch(n -> n > 0); // true
  boolean noneMatch = numbers.stream().noneMatch(n -> n < 0); // true
  ```

---

### **Parallel Streams**

Parallel streams allow you to process data in multiple threads for better performance:
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
numbers.parallelStream()
       .forEach(System.out::println);
```

---

### **Best Practices**
1. Use **parallelStream()** only when processing large datasets.
2. Avoid modifying shared variables inside streams (to prevent concurrency issues).
3. Use **collect()** when you need to gather results into a collection.

---

### **Use Cases**
- **Data Filtering**: Extracting specific data from a collection.
- **Data Transformation**: Converting data to a new format.
- **Aggregation**: Summing, counting, or reducing data.
- **Batch Processing**: Processing large datasets efficiently.

