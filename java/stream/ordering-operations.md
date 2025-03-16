### **Ordering Operations in Java Streams**

Ordering operations in Java Streams are used to arrange elements in a specific order, either natural or custom-defined. These operations are intermediate and do not produce results until a terminal operation is invoked.

---

### **Key Ordering Methods**

| **Method**          | **Description**                                                                 |
|----------------------|---------------------------------------------------------------------------------|
| **`sorted()`**       | Sorts elements in their natural order (ascending order for numbers, alphabetic for strings). |
| **`sorted(Comparator)`** | Sorts elements using a custom comparator defined by the user.                          |

---

### **Code Example**

Here’s an example of how to use ordering operations in Java Streams:

```java
@Test
void testOrderingOperations() {
    List<String> names = List.of("Sevendi", "Eldrige", "Rifki", "Budi", "Nugraha", "Joko");

    // Natural order sorting (alphabetical)
    names.stream()
         .sorted()
         .forEach(System.out::println);
    // Output: Budi, Sevendi, Joko, Rifki, Eldrige, Nugraha

    // Custom order sorting (by string length)
    names.stream()
         .sorted(Comparator.comparingInt(String::length))
         .forEach(System.out::println);
    // Output: Sevendi, Budi, Joko, Nugraha, Rifki, Eldrige

    // Reverse order sorting (alphabetical)
    names.stream()
         .sorted(Comparator.reverseOrder())
         .forEach(System.out::println);
    // Output: Nugraha, Eldrige, Rifki, Joko, Sevendi, Budi
}
```

---

### **Explanation of Methods**

1. **`sorted()`**
   - Sorts the elements of the stream in their **natural order**.
   - Natural order for numbers is ascending, and for strings, it’s alphabetical.
   - Example:
     ```java
     List<Integer> numbers = List.of(5, 2, 8, 1, 3);
     numbers.stream()
            .sorted()
            .forEach(System.out::println); // Output: 1, 2, 3, 5, 8
     ```

2. **`sorted(Comparator)`**
   - Sorts the elements using a **custom comparator** provided by the user.
   - You can define your own sorting logic using `Comparator`.
   - Example:
     ```java
     List<String> names = List.of("Sevendi", "Eldrige", "Rifki", "Budi", "Nugraha", "Joko");

     // Sort by string length
     names.stream()
          .sorted(Comparator.comparingInt(String::length))
          .forEach(System.out::println);
     // Output: Sevendi, Budi, Joko, Nugraha, Rifki, Eldrige
     ```

---

### **Practical Use Cases**

1. **Sorting Numbers**
   ```java
   List<Integer> numbers = List.of(10, 5, 20, 1, 15);
   numbers.stream()
          .sorted()
          .forEach(System.out::println); // Output: 1, 5, 10, 15, 20
   ```

2. **Sorting Strings Alphabetically**
   ```java
   List<String> names = List.of("Zara", "Anna", "Mike", "John");
   names.stream()
        .sorted()
        .forEach(System.out::println); // Output: Anna, John, Mike, Zara
   ```

3. **Sorting Strings by Length**
   ```java
   List<String> names = List.of("Sevendi", "Eldrige", "Rifki", "Budi");
   names.stream()
        .sorted(Comparator.comparingInt(String::length))
        .forEach(System.out::println);
   // Output: Sevendi, Budi, Rifki, Eldrige
   ```

4. **Sorting in Reverse Order**
   ```java
   List<Integer> numbers = List.of(10, 5, 20, 1, 15);
   numbers.stream()
          .sorted(Comparator.reverseOrder())
          .forEach(System.out::println); // Output: 20, 15, 10, 5, 1
   ```

5. **Sorting Complex Objects**
   ```java
   class Person {
       String name;
       int age;

       Person(String name, int age) {
           this.name = name;
           this.age = age;
       }

       @Override
       public String toString() {
           return name + " (" + age + ")";
       }
   }

   List<Person> people = List.of(
       new Person("Sevendi", 30),
       new Person("Budi", 25),
       new Person("Joko", 35)
   );

   // Sort by age
   people.stream()
         .sorted(Comparator.comparingInt(person -> person.age))
         .forEach(System.out::println);
   // Output: Budi (25), Sevendi (30), Joko (35)
   ```

---

### **Key Considerations**

1. **Stability of Sorting**:
   - The `sorted()` method is **stable**, meaning that equal elements retain their original order.

2. **Performance**:
   - Sorting operations can be computationally expensive for large datasets. Use them judiciously.

3. **Custom Comparators**:
   - Always ensure your custom comparators are consistent with `equals()` to avoid unexpected behavior.

4. **Stream is Immutable**:
   - Sorting does not modify the original collection. It creates a new stream with the sorted order.

