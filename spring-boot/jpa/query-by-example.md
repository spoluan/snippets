### **Query by Example (QBE) in JPA**

Query by Example (QBE) is a feature in **Spring Data JPA** that allows you to create queries dynamically by providing an example entity. This feature is useful when you want to search for entities based on the properties of an example object without writing explicit queries.

---

### **Key Features of Query by Example**
1. **Dynamic Queries**: Allows creating queries dynamically based on the fields of an example entity.
2. **Matcher Support**: Provides `ExampleMatcher` to customize how matching is performed (e.g., ignoring case, null values, etc.).
3. **No Boilerplate Code**: Eliminates the need to write custom queries.
4. **Flexible Matching**: Supports partial matching, case-insensitive matching, and ignoring null values.

---

### **How Query by Example Works**
1. Create an entity object with the fields you want to use as search criteria.
2. Use the `Example.of()` method to create an `Example` object from the entity.
3. Pass the `Example` object to repository methods like `findAll()` to execute the query.

---

### **Basic Implementation**

#### **1. Example Entity**
```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Category {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    // Getters and Setters
}
```

#### **2. Repository**
```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface CategoryRepository extends JpaRepository<Category, Long> {
}
```

#### **3. Test Case for Query by Example**
In this example, we create an example object with the `name` field set to "GADGET MURAH". Spring Data JPA will generate a query to find all categories with the same name.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class CategoryRepositoryTest {

    @Autowired
    private CategoryRepository categoryRepository;

    @Test
    void example() {
        Category category = new Category();
        category.setName("GADGET MURAH");

        Example<Category> example = Example.of(category);

        List<Category> categories = categoryRepository.findAll(example);
        assertEquals(1, categories.size()); // Verify the result
    }
}
```

---

### **Using ExampleMatcher**

`ExampleMatcher` allows you to customize how the query is performed. You can:
- Ignore case sensitivity.
- Ignore null or empty fields.
- Perform partial or exact matches.

#### **Customizing with ExampleMatcher**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.domain.Example;
import org.springframework.data.domain.ExampleMatcher;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class CategoryRepositoryTest {

    @Autowired
    private CategoryRepository categoryRepository;

    @Test
    void exampleMatcher() {
        Category category = new Category();
        category.setName("gadget murah");

        ExampleMatcher matcher = ExampleMatcher
                .matching()
                .withIgnoreNullValues() // Ignore null fields
                .withIgnoreCase();     // Ignore case sensitivity

        Example<Category> example = Example.of(category, matcher);

        List<Category> categories = categoryRepository.findAll(example);
        assertEquals(1, categories.size()); // Verify the result
    }
}
```

---

### **Common ExampleMatcher Methods**

| Method                      | Description                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| `withIgnoreNullValues()`    | Ignores all `null` fields in the example entity.                           |
| `withIgnoreCase()`          | Makes the query case-insensitive.                                          |
| `withStringMatcher()`       | Specifies how string fields are matched (e.g., `EXACT`, `CONTAINING`).     |
| `withMatcher()`             | Customizes matching for specific fields.                                   |
| `withIncludeNullValues()`   | Includes `null` fields in the query.                                       |

#### **String Matchers**
- `EXACT`: Matches the string exactly.
- `CONTAINING`: Matches if the string contains the value.
- `STARTING`: Matches if the string starts with the value.
- `ENDING`: Matches if the string ends with the value.

---

### **Advanced Use Cases**

#### **1. Partial Matching**
Search for entities where a string field contains a specific substring.

```java
ExampleMatcher matcher = ExampleMatcher
        .matching()
        .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING) // Partial match
        .withIgnoreCase();

Example<Category> example = Example.of(category, matcher);
```

---

#### **2. Exact Matching**
Search for entities where all fields match exactly.

```java
ExampleMatcher matcher = ExampleMatcher.matching(); // Default is exact matching
Example<Category> example = Example.of(category, matcher);
```

---

#### **3. Case-Insensitive Matching**
Ignore case sensitivity for string fields.

```java
ExampleMatcher matcher = ExampleMatcher
        .matching()
        .withIgnoreCase(); // Ignore case for all string fields
```

---

#### **4. Ignoring Specific Fields**
Exclude specific fields from the query.

```java
ExampleMatcher matcher = ExampleMatcher
        .matching()
        .withIgnorePaths("id"); // Ignore the "id" field
```

---

#### **5. Combining Matchers**
Combine multiple matchers for complex queries.

```java
ExampleMatcher matcher = ExampleMatcher
        .matching()
        .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING)
        .withIgnoreCase()
        .withIgnorePaths("id");
```

---

### **Real-World Use Cases**

1. **Search Filters**:
   - Allow users to search for entities using flexible filters (e.g., search by name, description, category).
   
2. **Dynamic Query Generation**:
   - Generate queries dynamically based on user input without writing explicit SQL or JPQL.

3. **Admin Dashboards**:
   - Use QBE to implement search functionality in admin dashboards for managing entities.

4. **APIs with Flexible Filters**:
   - Build APIs that accept dynamic filters and use QBE to query the database.

---

### **Advantages of Query by Example**
- **Simplicity**: No need to write custom queries.
- **Flexibility**: Easily customizable with `ExampleMatcher`.
- **Dynamic**: Generate queries at runtime based on user input.
- **Readable**: Code is easy to understand and maintain.

---

### **Limitations of Query by Example**
1. **Limited to Equality Matching**:
   - QBE is primarily designed for equality-based matching. For more complex queries involving joins, subqueries, or aggregations, you may need JPQL or Criteria API.
   
2. **No Support for OR Conditions**:
   - QBE only supports AND conditions. If you need OR conditions, you must use custom queries.

3. **Not Suitable for Large Datasets**:
   - Dynamic queries may not be optimized for performance when working with large datasets.

---

### **Summary**

- Query by Example (QBE) in JPA allows you to create dynamic queries based on an example entity.
- Use `Example.of()` to create an example and `ExampleMatcher` to customize the query behavior.
- QBE is ideal for simple, dynamic queries like filtering or searching entities.
- For more complex queries, consider using JPQL, Criteria API, or custom repository methods.

By combining QBE with `ExampleMatcher`, you can create flexible and powerful search functionalities with minimal effort.
