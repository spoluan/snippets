### **For Each Operations in Java Streams**

For Each Operations are used to iterate over each element in a stream and perform specific actions. Java Streams provide two main methods for this purpose: `forEach` and `peek`.

---

### **Key Points**

1. **Purpose**:
   - To iterate over and process each element in a stream.
   - Perform specific actions such as printing or modifying data.

2. **Methods**:
   - **`forEach`**: A terminal operation that consumes the stream.
   - **`peek`**: An intermediate operation that allows inspection without consuming the stream.

---

### **Comparison of Methods**

| **Method**           | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| **`forEach(T -> void)`** | Iterates over each element in the stream. This is a **terminal operation**.    |
| **`peek(T -> void)`**    | Iterates over each element but returns the stream for further operations. This is an **intermediate operation**. |

---

### **Examples**

#### 1. **Using `forEach`**
`forEach` is typically used to perform a final action on each element, such as printing or saving data.

```java
List<String> names = List.of("Alice", "Bob", "Charlie");

// Print each name using forEach
names.stream()
     .forEach(System.out::println);

// Output:
// Alice
// Bob
// Charlie
```

---

#### 2. **Using `peek`**
`peek` is used for debugging or inspecting elements during stream processing. It does not consume the stream, allowing further operations.

```java
List<String> names = List.of("Alice", "Bob", "Charlie");

// Convert names to uppercase and inspect using peek
names.stream()
     .map(String::toUpperCase) // Convert to uppercase
     .peek(name -> System.out.println("Upper Name: " + name)) // Inspect
     .forEach(System.out::println); // Final action

// Output:
// Upper Name: ALICE
// ALICE
// Upper Name: BOB
// BOB
// Upper Name: CHARLIE
// CHARLIE
```

---

### **Code Example from Images**

```java
names.stream()
     .map(String::toUpperCase) // Convert names to uppercase
     .peek(upper -> System.out.println("Upper Name: " + upper)) // Inspect each element
     .forEach(System.out::println); // Print each element
```

#### Explanation:
1. **`map(String::toUpperCase)`**:
   - Converts each name in the stream to uppercase.

2. **`peek(upper -> System.out.println("Upper Name: " + upper))`**:
   - Prints each name in uppercase for inspection.
   - Does not consume the stream.

3. **`forEach(System.out::println)`**:
   - Prints the final processed names.
   - This is the terminal operation that consumes the stream.

---

### **Key Differences Between `forEach` and `peek`**

| **Aspect**           | **`forEach`**                      | **`peek`**                        |
|-----------------------|-------------------------------------|------------------------------------|
| **Operation Type**    | Terminal Operation                | Intermediate Operation            |
| **Stream Consumption**| Consumes the stream               | Does not consume the stream       |
| **Purpose**           | Final action on elements          | Inspection or debugging           |
| **Return Type**       | `void`                            | Returns the modified stream       |

---

### **When to Use**

1. **Use `forEach`**:
   - When you need to perform a final action on each element (e.g., saving to a database, printing).

2. **Use `peek`**:
   - For debugging or inspecting elements during a stream pipeline.
   - When you want to continue processing the stream after inspection.
