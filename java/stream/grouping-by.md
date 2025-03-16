### **Grouping By with Java Streams**

The `Collectors.groupingBy()` method is a powerful tool in Java Streams for grouping elements of a collection based on a classification function. The result of `groupingBy()` is a `Map` where the keys are the group identifiers, and the values are lists of elements belonging to those groups.

---

### **1. Key Concepts**

- **Purpose**:  
  To group elements of a stream into categories (groups) based on a classifier function.

- **Result**:  
  The output is a `Map<Group, List<Value>>`, where:
  - **Group**: The classification key.
  - **Value**: The list of elements in each group.

- **Variants**:  
  - Basic grouping: Groups elements into a `List` by default.
  - Downstream collectors: Allows further processing (e.g., counting, mapping, reducing) of grouped elements.

---

### **2. Examples from the Images**

#### **Example 1: Grouping Numbers**
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<String, List<Integer>> map1 = numbers.stream()
    .collect(Collectors.groupingBy(
        integer -> integer > 5 ? "Besar" : "Kecil" // Classifier: "Besar" if > 5, "Kecil" otherwise
    ));

System.out.println(map1);
```

- **Input**: `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`
- **Output**:
  ```java
  {Kecil=[1, 2, 3, 4, 5], Besar=[6, 7, 8, 9, 10]}
  ```

---

#### **Example 2: Grouping Names by Length**
```java
List<String> names = List.of("Sevendi", "Eldrigen", "Rifki", "Budi", "Nugraha", "Joko");

Map<String, List<String>> map2 = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.length() > 4 ? "Besar" : "Kecil" // Classifier: "Besar" if length > 4, "Kecil" otherwise
    ));

System.out.println(map2);
```

- **Input**: `["Sevendi", "Eldrigen", "Rifki", "Budi", "Nugraha", "Joko"]`
- **Output**:
  ```java
  {Kecil=[Sevendi, Budi, Joko], Besar=[Eldrigen, Rifki, Nugraha]}
  ```

---

#### **Example 3: Grouping with Mapping**
```java
Map<String, List<String>> legacyIntervalDatasets = data.stream()
    .collect(Collectors.groupingBy(
        Data::getLegacyInterval, // Classifier: Group by legacy interval
        Collectors.mapping(
            Data::getDatasetName, // Map each element to its dataset name
            Collectors.toList()                       // Collect the mapped values into a list
        )
    ));
```

- **Explanation**:
  - The `groupingBy()` groups elements by their `legacyInterval`.
  - The `mapping()` collector transforms each grouped element into its `datasetName` and collects them into a list.

---

### **3. Advanced Examples**

#### **a. Grouping Strings by First Letter**
```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Anna", "Brian");

Map<Character, List<String>> groupedByFirstLetter = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0) // Classifier: First character of the name
    ));

System.out.println(groupedByFirstLetter);
```

- **Output**:
  ```java
  {A=[Alice, Anna], B=[Bob, Brian], C=[Charlie]}
  ```

---

#### **b. Grouping with Counting**
```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Anna", "Brian");

Map<Character, Long> countByFirstLetter = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0),      // Classifier: First character of the name
        Collectors.counting()        // Downstream collector: Count occurrences
    ));

System.out.println(countByFirstLetter);
```

- **Output**:
  ```java
  {A=2, B=2, C=1}
  ```

---

#### **c. Grouping with Summing**
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Map<String, Integer> sumByGroup = numbers.stream()
    .collect(Collectors.groupingBy(
        number -> number > 5 ? "Besar" : "Kecil", // Classifier
        Collectors.summingInt(Integer::intValue)  // Downstream collector: Sum the values
    ));

System.out.println(sumByGroup);
```

- **Output**:
  ```java
  {Kecil=15, Besar=40}
  ```

---

#### **d. Grouping with Custom Data Structures**
```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Anna", "Brian");

Map<Character, Set<String>> groupedByFirstLetter = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0),                // Classifier: First character
        Collectors.toCollection(TreeSet::new) // Downstream collector: Collect into a TreeSet
    ));

System.out.println(groupedByFirstLetter);
```

- **Output**:
  ```java
  {A=[Alice, Anna], B=[Bob, Brian], C=[Charlie]}
  ```

---

#### **e. Nested Grouping**
```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Anna", "Brian");

Map<Character, Map<Integer, List<String>>> nestedGrouping = names.stream()
    .collect(Collectors.groupingBy(
        name -> name.charAt(0),                 // Outer grouping: First character
        Collectors.groupingBy(
            String::length                      // Inner grouping: Length of the name
        )
    ));

System.out.println(nestedGrouping);
```

- **Output**:
  ```java
  {
    A={5=[Alice, Anna]},
    B={3=[Bob], 5=[Brian]},
    C={7=[Charlie]}
  }
  ```

---

### **4. Summary of Usage**

| **Use Case**                | **Code Example**                                                                                                  | **Output**                                                                                     |
|-----------------------------|------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------|
| Basic Grouping              | `Collectors.groupingBy(classifier)`                                                                              | Groups elements into a `List` by default.                                                    |
| Grouping with Mapping       | `Collectors.groupingBy(classifier, Collectors.mapping(mapper, downstream))`                                      | Groups elements and maps them to transformed values.                                          |
| Grouping with Counting      | `Collectors.groupingBy(classifier, Collectors.counting())`                                                       | Groups elements and counts the number of elements in each group.                             |
| Grouping with Summing       | `Collectors.groupingBy(classifier, Collectors.summingInt(mapper))`                                               | Groups elements and calculates the sum of values in each group.                              |
| Nested Grouping             | `Collectors.groupingBy(outerClassifier, Collectors.groupingBy(innerClassifier))`                                 | Groups elements into nested maps.                                                            |
| Grouping with Custom Set    | `Collectors.groupingBy(classifier, Collectors.toCollection(TreeSet::new))`                                       | Groups elements into a custom collection type.                                               |
