To pass conditions dynamically to the query, you can use **parameterized queries** with placeholders (`?`) and provide the values for those placeholders as arguments to the `query` method. Here's how you can adapt your code to include conditions:

---

### **Parameterized Query with Conditions**
You can modify the SQL query to include conditions and pass the parameters dynamically.

**Example:**
```java
// Define the extractor
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

// Define the SQL query with conditions
String sql = "SELECT course_id, title, description FROM course WHERE title = ? AND description LIKE ?";

// Define the parameters for the query
String titleCondition = "Java Basics";
String descriptionCondition = "%beginner%";

// Execute the query with parameters
List<Course> courses = jdbcTemplate.query(sql, extractor, titleCondition, descriptionCondition);

// Process the result
courses.forEach(System.out::println);
```

---

### **Explanation**
1. **Parameterized SQL**: The `?` placeholders in the query are used to represent parameters.
2. **Passing Parameters**: The parameters (`titleCondition` and `descriptionCondition`) are passed as additional arguments to the `query` method.
3. **Dynamic Conditions**: You can dynamically set the values of the conditions (`titleCondition` and `descriptionCondition`) based on your application's requirements.

---

### **Alternative: Using `NamedParameterJdbcTemplate`**
If you prefer named parameters for better readability, you can use `NamedParameterJdbcTemplate`.

**Example:**
```java
// Define the extractor
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

// Define the SQL query with named parameters
String sql = "SELECT course_id, title, description FROM course WHERE title = :title AND description LIKE :description";

// Define the parameters for the query
Map<String, Object> params = new HashMap<>();
params.put("title", "Java Basics");
params.put("description", "%beginner%");

// Use NamedParameterJdbcTemplate
NamedParameterJdbcTemplate namedJdbcTemplate = new NamedParameterJdbcTemplate(jdbcTemplate.getDataSource());
List<Course> courses = namedJdbcTemplate.query(sql, params, extractor);

// Process the result
courses.forEach(System.out::println);
```

---

### **Why Use Parameterized Queries?**
- **Prevents SQL Injection**: Using placeholders (`?`) or named parameters ensures that the input is properly escaped.
- **Dynamic Queries**: Allows you to build queries with dynamic conditions without concatenating strings manually.
- **Reusability**: You can reuse the same `ResultSetExtractor` logic with different conditions.

