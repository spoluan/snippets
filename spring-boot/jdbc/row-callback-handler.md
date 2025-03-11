
### **Using `RowCallbackHandler`**
If you don’t need to return a value and only process rows, you can use `RowCallbackHandler`.

**Example:**
```java
RowCallbackHandler rowHandler = (rs) -> {
    while (rs.next()) {
        int courseId = rs.getInt("course_id");
        String title = rs.getString("title");
        String description = rs.getString("description");

        // Process the row
        System.out.println("Course ID: " + courseId + ", Title: " + title);
    }
};

// Use the handler with JdbcTemplate
String sql = "SELECT course_id, title, description FROM course";
jdbcTemplate.query(sql, rowHandler);
```

---

### **Using `ResultSetExtractor`**
If you need to return a result (e.g., a list of objects), you can use `ResultSetExtractor`.

**Example:**
```java
ResultSetExtractor<List<Course>> extractor = (rs) -> {
    List<Course> courses = new ArrayList<>();
    while (rs.next()) {
        Course course = new Course(
            rs.getInt("course_id"),
            rs.getString("title"),
            rs.getString("description")
        );
        courses.add(course);
    }
    return courses;
};

// Use the extractor with JdbcTemplate
String sql = "SELECT course_id, title, description FROM course";
List<Course> courses = jdbcTemplate.query(sql, extractor);

// Process the result
courses.forEach(System.out::println);
```

---

### **Functional Interface Explanation**
- **`RowCallbackHandler`**:
  - Processes rows one by one without returning a result.
  - Useful for streaming or immediate row-by-row processing.

- **`ResultSetExtractor`**:
  - Extracts and returns a result (e.g., a list or a map) from the entire `ResultSet`.
  - Useful when you need to collect and process all rows together.

---

### **Why Store Lambda in a Variable?**
- **Reusability**: You can reuse the same logic across multiple queries.
- **Readability**: Separating the processing logic makes the code cleaner and easier to understand.
- **Testability**: You can test the lambda independently of the database query.

By storing the lambda in a variable, you simplify your code and make it modular.
