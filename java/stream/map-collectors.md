### **Creating a Map from a Stream Using `Collectors`**

The provided images explain how to use Java Streams and the `Collectors` utility class to create a `Map` from a stream of elements. Here's a detailed explanation and additional examples.

---

### **1. Key Concepts**

- **Difference from `List` or `Set`**:  
  When creating a `Map`, you must specify two functions:
  - **Key Mapper**: Defines how to generate the key for each element.
  - **Value Mapper**: Defines how to generate the value for each element.

- **Utility Methods**:  
  The `Collectors` class provides methods such as:
  - `toMap()`: Creates a mutable `Map`.
  - `toConcurrentMap()`: Creates a thread-safe `ConcurrentMap`.
  - `toUnmodifiableMap()`: Creates an immutable `Map`.

---

### **2. Example from the Image**

#### **Code**
```java
Map<String, Integer> map = names.stream()
    .collect(Collectors.toMap(
        name -> name,                // Key Mapper: Use the name itself as the key
        name -> name.length()        // Value Mapper: Use the length of the name as the value
    ));
System.out.println(map);
```

#### **Explanation**
- **Input**: A `Stream` of `names` (e.g., `["Alice", "Bob", "Charlie"]`).
- **Key Mapper**: Each name becomes the key of the `Map` (e.g., `"Alice"`, `"Bob"`, `"Charlie"`).
- **Value Mapper**: The length of each name becomes the value (e.g., `5`, `3`, `7`).
- **Output**:  
  ```java
  {Alice=5, Bob=3, Charlie=7}
  ```

---

### **3. Common Methods for Creating Maps**

#### **a. `Collectors.toMap()`**
- Creates a mutable `Map`.
- **Example**:
  ```java
  Map<String, Integer> map = names.stream()
      .collect(Collectors.toMap(
          name -> name,                // Key Mapper
          name -> name.length()        // Value Mapper
      ));
  System.out.println(map);
  ```

#### **b. `Collectors.toConcurrentMap()`**
- Creates a thread-safe `ConcurrentMap`.
- **Example**:
  ```java
  Map<String, Integer> concurrentMap = names.stream()
      .collect(Collectors.toConcurrentMap(
          name -> name,                // Key Mapper
          name -> name.length()        // Value Mapper
      ));
  System.out.println(concurrentMap);
  ```

#### **c. `Collectors.toUnmodifiableMap()`**
- Creates an immutable `Map`.
- **Example**:
  ```java
  Map<String, Integer> unmodifiableMap = names.stream()
      .collect(Collectors.toUnmodifiableMap(
          name -> name,                // Key Mapper
          name -> name.length()        // Value Mapper
      ));
  System.out.println(unmodifiableMap);
  ```

---

### **4. Handling Duplicate Keys**

If the key mapper generates duplicate keys, you must specify a **merge function** to resolve conflicts.

#### **Example: Handling Duplicates**
```java
List<String> names = Arrays.asList("Alice", "Bob", "Alice");

Map<String, Integer> map = names.stream()
    .collect(Collectors.toMap(
        name -> name,                // Key Mapper
        name -> name.length(),       // Value Mapper
        (existing, replacement) -> existing // Merge Function: Keep the existing value
    ));
System.out.println(map);
```

#### **Output**:
```java
{Alice=5, Bob=3}
```

---

### **5. Advanced Examples**

#### **a. Creating a Map with Custom Keys and Values**
```java
Map<Character, List<String>> groupedByFirstLetter = names.stream()
    .collect(Collectors.toMap(
        name -> name.charAt(0),      // Key Mapper: First letter of the name
        name -> new ArrayList<>(List.of(name)), // Value Mapper: List containing the name
        (existing, replacement) -> { // Merge Function: Combine lists
            existing.addAll(replacement);
            return existing;
        }
    ));
System.out.println(groupedByFirstLetter);
```

#### **Output**:
For `names = ["Alice", "Bob", "Charlie", "Anna"]`:
```java
{A=[Alice, Anna], B=[Bob], C=[Charlie]}
```

---

#### **b. Grouping by Length**
```java
Map<Integer, List<String>> groupedByLength = names.stream()
    .collect(Collectors.groupingBy(String::length));
System.out.println(groupedByLength);
```

#### **Output**:
For `names = ["Alice", "Bob", "Charlie", "Anna"]`:
```java
{3=[Bob], 4=[Anna], 5=[Alice], 7=[Charlie]}
```

---

#### **c. Counting Elements by Key**
```java
Map<Character, Long> countByFirstLetter = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0),      // Key Mapper: First letter of the name
        Collectors.counting()        // Downstream Collector: Count occurrences
    ));
System.out.println(countByFirstLetter);
```

#### **Output**:
For `names = ["Alice", "Bob", "Charlie", "Anna"]`:
```java
{A=2, B=1, C=1}
```

---

#### **d. Joining Strings in a Map**
```java
Map<Character, String> joinedNamesByFirstLetter = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0),      // Key Mapper: First letter of the name
        Collectors.mapping(          // Downstream Collector: Transform values
            name -> name.toUpperCase(),
            Collectors.joining(", ") // Join transformed values with ", "
        )
    ));
System.out.println(joinedNamesByFirstLetter);
```

#### **Output**:
For `names = ["Alice", "Bob", "Charlie", "Anna"]`:
```java
{A=ALICE, ANNA, B=BOB, C=CHARLIE}
```

---

### **6. Summary**

| **Method**                  | **Description**                                                                                   |
|-----------------------------|---------------------------------------------------------------------------------------------------|
| `toMap()`                   | Creates a mutable `Map`.                                                                         |
| `toConcurrentMap()`         | Creates a thread-safe `ConcurrentMap`.                                                           |
| `toUnmodifiableMap()`       | Creates an immutable `Map`.                                                                      |
| `groupingBy()`              | Groups elements by a classifier function.                                                        |
| `mapping()`                 | Transforms values before collecting them.                                                        |
| `counting()`                | Counts the number of elements in each group.                                                     |
| `joining()`                 | Concatenates strings with a delimiter.                                                           |

