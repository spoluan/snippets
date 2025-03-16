### **Retrieving Operations in Java Streams**

Retrieving operations in Java Streams allow you to extract specific subsets of data from a stream. These operations are particularly useful when you need to limit, skip, or conditionally retrieve elements from a collection.

---

### **Key Retrieving Operations**

1. **`limit(n)`**
   - Retrieves the first `n` elements from the stream.
   - Example:
     ```java
     List<String> names = List.of("Sevendi", "Eldrige", "Rifki", "Budi", "Nugraha", "Joko");
     names.stream()
          .limit(2)
          .forEach(System.out::println); // Output: Sevendi, Eldrige
     ```

2. **`skip(n)`**
   - Skips the first `n` elements and retrieves the rest.
   - Example:
     ```java
     names.stream()
          .skip(2)
          .forEach(System.out::println); // Output: Rifki, Budi, Nugraha, Joko
     ```

3. **`takeWhile(predicate)`**
   - Retrieves elements from the stream as long as the condition (`predicate`) is true. Once the condition is false, it stops processing.
   - Example:
     ```java
     names.stream()
          .takeWhile(name -> name.length() != 4)
          .forEach(System.out::println); // Output: Sevendi, Eldrige, Rifki
     ```
     (Stops at "Budi" because its length is 4.)

4. **`dropWhile(predicate)`**
   - Skips elements from the stream as long as the condition (`predicate`) is true. Once the condition is false, it starts retrieving the remaining elements.
   - Example:
     ```java
     names.stream()
          .dropWhile(name -> name.length() < 4)
          .forEach(System.out::println); // Output: Eldrige, Rifki, Budi, Nugraha, Joko
     ```
     (Drops "Sevendi" because its length is less than 4.)

---

### **Code Example**

Here’s how all these operations work together:
```java
@Test
void testRetrievingOperation() {
    List<String> names = List.of("Sevendi", "Eldrige", "Rifki", "Budi", "Nugraha", "Joko");

    // Limit: Get the first 2 elements
    names.stream()
         .limit(2)
         .forEach(System.out::println); // Output: Sevendi, Eldrige

    // Skip: Skip the first 2 elements
    names.stream()
         .skip(2)
         .forEach(System.out::println); // Output: Rifki, Budi, Nugraha, Joko

    // TakeWhile: Take elements while condition is true
    names.stream()
         .takeWhile(name -> name.length() != 4)
         .forEach(System.out::println); // Output: Sevendi, Eldrige, Rifki

    // DropWhile: Drop elements while condition is true
    names.stream()
         .dropWhile(name -> name.length() < 4)
         .forEach(System.out::println); // Output: Eldrige, Rifki, Budi, Nugraha, Joko
}
```

---

### **When to Use Retrieving Operations**

- **`limit(n)`**: When you only need the first few elements of a stream.
- **`skip(n)`**: When you want to ignore a certain number of elements at the start.
- **`takeWhile(predicate)`**: When you need elements up to a certain condition.
- **`dropWhile(predicate)`**: When you want to ignore elements until a condition is met.

---

### **Points to Remember**
1. **Order Matters**: Operations like `takeWhile` and `dropWhile` depend on the order of elements in the stream.
2. **Short-circuiting**: Both `limit` and `takeWhile` are short-circuiting operations—they stop processing once their condition is met.
3. **Lazy Evaluation**: These operations are intermediate, so they won’t execute until a terminal operation (like `forEach`) is called.
