### **Partitioning By with Java Streams**

The `Collectors.partitioningBy()` method is a specialized form of grouping where the classification function always results in two groups: `true` and `false`. This makes it ideal for splitting a collection into two parts based on a predicate.

---

### **1. Key Concepts**

- **Purpose**:  
  To partition elements of a stream into two groups based on a boolean condition (predicate).

- **Result**:  
  The output is a `Map<Boolean, List<Value>>`, where:
  - The key `true` contains elements that satisfy the predicate.
  - The key `false` contains elements that do not satisfy the predicate.

- **Difference from `groupingBy()`**:  
  - `groupingBy()` can produce multiple groups based on a classifier.
  - `partitioningBy()` always produces exactly two groups (`true` and `false`).

---

### **2. Examples from the Images**

#### **Example 1: Partitioning Numbers**
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<Boolean, List<Integer>> map1 = numbers.stream()
    .collect(Collectors.partitioningBy(
        integer -> integer > 5 // Predicate: Numbers greater than 5
    ));

System.out.println(map1);
```

- **Input**: `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`
- **Output**:
  ```java
  {false=[1, 2, 3, 4, 5], true=[6, 7, 8, 9, 10]}
  ```

---

#### **Example 2: Partitioning Names by Length**
```java
List<String> names = List.of("Sevendi", "Rifki", "Eldrige", "Budi", "Nugraha", "Joko");

Map<Boolean, List<String>> map2 = names.stream()
    .collect(Collectors.partitioningBy(
        name -> name.length() > 4 // Predicate: Names longer than 4 characters
    ));

System.out.println(map2);
```

- **Input**: `["Sevendi", "Rifki", "Eldrige", "Budi", "Nugraha", "Joko"]`
- **Output**:
  ```java
  {false=[Sevendi, Budi, Joko], true=[Rifki, Eldrige, Nugraha]}
  ```

---

### **3. Advanced Examples**

#### **a. Partitioning with Downstream Collectors**
You can use downstream collectors to further process the partitioned groups.

##### **Example: Counting Elements in Each Partition**
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<Boolean, Long> partitionCount = numbers.stream()
    .collect(Collectors.partitioningBy(
        number -> number > 5, // Predicate
        Collectors.counting() // Downstream collector: Count elements
    ));

System.out.println(partitionCount);
```

- **Output**:
  ```java
  {false=5, true=5}
  ```

---

#### **b. Partitioning into Sets**
You can collect the partitioned results into a `Set` instead of a `List`.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<Boolean, Set<Integer>> partitionSet = numbers.stream()
    .collect(Collectors.partitioningBy(
        number -> number > 5, // Predicate
        Collectors.toSet()    // Downstream collector: Collect into a Set
    ));

System.out.println(partitionSet);
```

- **Output**:
  ```java
  {false=[1, 2, 3, 4, 5], true=[6, 7, 8, 9, 10]}
  ```

---

#### **c. Nested Partitioning**
You can combine `partitioningBy()` with other collectors like `groupingBy()` for more complex operations.

```java
List<String> names = List.of("Sevendi", "Rifki", "Eldrige", "Budi", "Nugraha", "Joko");

Map<Boolean, Map<Character, List<String>>> nestedPartition = names.stream()
    .collect(Collectors.partitioningBy(
        name -> name.length() > 4, // Outer partition: Length > 4
        Collectors.groupingBy(
            name -> name.charAt(0) // Inner grouping: First character
        )
    ));

System.out.println(nestedPartition);
```

- **Output**:
  ```java
  {
    false={E=[Sevendi], B=[Budi], J=[Joko]},
    true={K=[Rifki, Eldrige], N=[Nugraha]}
  }
  ```

---

### **4. Summary of Usage**

| **Use Case**                | **Code Example**                                                                                                  | **Output**                                                                                     |
|-----------------------------|------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Basic Partitioning          | `Collectors.partitioningBy(predicate)`                                                                           | Splits elements into `true` and `false` groups.                                              |
| Partitioning with Counting  | `Collectors.partitioningBy(predicate, Collectors.counting())`                                                    | Counts elements in each partition.                                                           |
| Partitioning with Sets      | `Collectors.partitioningBy(predicate, Collectors.toSet())`                                                       | Collects elements into a `Set` instead of a `List`.                                          |
| Nested Partitioning         | `Collectors.partitioningBy(predicate, Collectors.groupingBy(classifier))`                                        | Combines partitioning with grouping.                                                         |

---

### **5. Key Differences Between `partitioningBy()` and `groupingBy()`**

| **Feature**                | **`partitioningBy()`**                         | **`groupingBy()`**                                |
|----------------------------|-----------------------------------------------|--------------------------------------------------|
| **Number of Groups**       | Always 2 (`true` and `false`).                | Can have multiple groups based on the classifier.|
| **Key Type**               | `Boolean`.                                   | Any type (based on the classifier).              |
| **Use Case**               | Binary classification (e.g., pass/fail).     | General grouping into multiple categories.       |

