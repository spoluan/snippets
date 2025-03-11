

### **How `RowMapper` Works **

When you use `jdbcTemplate.query` with a `RowMapper`:
1. The SQL query is executed, and the entire result set is fetched from the database.
2. Spring iterates over each row in the result set and applies the `RowMapper` to map each row into an object (e.g., `Course`).
3. All the mapped objects are stored in a `List<Course>` and returned to the caller.

#### Example:

```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
List<Course> courses = jdbcTemplate.query(sql, rowMapper, "%Java%");
```

- If the query returns **10 rows**, the `RowMapper` maps all 10 rows into `Course` objects, and they are stored in memory as a `List<Course>`.
- If the query returns **1 million rows**, the `RowMapper` will map all 1 million rows into `Course` objects, and they will all be stored in memory at once.

This approach is fine for **small or moderate datasets**, but for **large datasets**, it can lead to:
- **High memory usage**: Since all rows are stored in memory as objects.
- **Performance bottlenecks**: As the system may struggle to handle large lists.

---

### **Why `RowMapper` with `query` is Not Lightweight**

The problem lies in **how the `query` method works**:
- It **loads the entire result set into memory** and maps every row before returning the result as a list.
- This means the memory usage grows linearly with the size of the result set.

For example:
- If each row is 1 KB in size and the query returns 1 million rows, you'll need approximately **1 GB of memory** just to hold the data in memory.

---

### **Alternatives for Large Datasets**

If you're dealing with large datasets and want a more **lightweight approach**, you need to process rows incrementally (row-by-row) instead of loading everything into memory at once. Here are the best alternatives:

---

#### **1. Use `RowCallbackHandler` for Row-by-Row Processing**
- **How it Works**: Instead of returning a `List`, the `RowCallbackHandler` processes each row as it is retrieved from the database, without storing them in memory.
- **Memory Usage**: Minimal (only one row is processed at a time).
- **Use Case**: When you need to process rows individually (e.g., log them, write to a file) without keeping the entire dataset in memory.

**Example**:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
jdbcTemplate.query(sql, (rs) -> {
    while (rs.next()) {
        int courseId = rs.getInt("course_id");
        String title = rs.getString("title");
        String description = rs.getString("description");

        // Process each row (e.g., write to a file or perform calculations)
        System.out.println("Course ID: " + courseId + ", Title: " + title);
    }
}, "%Java%");
```

- **Advantages**:
  - Processes rows one at a time.
  - Very lightweight for large datasets.
- **Disadvantages**:
  - You need to handle row processing manually (less convenient than `RowMapper`).

---

#### **2. Use `ResultSetExtractor` for Custom Processing**
- **How it Works**: Similar to `RowCallbackHandler`, but it allows you to process rows and return a custom result (e.g., a transformed list or aggregated data).
- **Memory Usage**: Minimal, as rows are processed incrementally.
- **Use Case**: When you need to process rows and return a custom result.

**Example**:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
List<Course> courses = jdbcTemplate.query(sql, (ResultSet rs) -> {
    List<Course> result = new ArrayList<>();
    while (rs.next()) {
        Course course = new Course(
            rs.getInt("course_id"),
            rs.getString("title"),
            rs.getString("description")
        );
        result.add(course);
    }
    return result;
}, "%Java%");
```

- **Advantages**:
  - Allows custom processing of rows.
  - Can return a custom result (e.g., aggregated data).
- **Disadvantages**:
  - Slightly more complex than `RowMapper`.

---

#### **3. Use Streaming with `RowMapper`**
- **How it Works**: You can enable **streaming** to process rows incrementally with a `RowMapper`. This avoids loading the entire result set into memory.
- **Memory Usage**: Minimal, as rows are fetched in batches and processed one at a time.
- **Use Case**: When you want the convenience of `RowMapper` but need to handle large datasets efficiently.

**Configuration for Streaming**:
- For **MySQL**:
  Add the following properties to your datasource configuration:
  ```properties
  spring.datasource.hikari.data-source-properties.useServerPrepStmts=true
  spring.datasource.hikari.data-source-properties.cachePrepStmts=false
  spring.datasource.hikari.data-source-properties.useCursorFetch=true
  ```
- For **PostgreSQL**:
  Set the fetch size:
  ```java
  jdbcTemplate.setFetchSize(50); // Fetch rows in batches of 50
  ```

**Example**:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
jdbcTemplate.query(sql, (rs) -> {
    while (rs.next()) {
        Course course = rowMapper.mapRow(rs, rs.getRow());
        // Process each row
        System.out.println(course);
    }
}, "%Java%");
```

- **Advantages**:
  - Allows you to use `RowMapper` in a memory-efficient way.
  - Processes rows incrementally.
- **Disadvantages**:
  - Requires proper configuration of the database connection.

---

#### **4. Use `queryForRowSet` for Lightweight Row Iteration**
- **How it Works**: Retrieves results as a `SqlRowSet`, which is a lightweight, disconnected representation of a result set.
- **Memory Usage**: Minimal, as rows are processed incrementally.
- **Use Case**: When you need to iterate over rows without mapping them to objects.

**Example**:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
SqlRowSet rowSet = jdbcTemplate.queryForRowSet(sql, "%Java%");
while (rowSet.next()) {
    int courseId = rowSet.getInt("course_id");
    String title = rowSet.getString("title");
    String description = rowSet.getString("description");

    // Process each row
    System.out.println("Course ID: " + courseId + ", Title: " + title);
}
```

- **Advantages**:
  - Disconnected and lightweight.
  - Allows row-by-row processing.
- **Disadvantages**:
  - Limited functionality compared to `ResultSet`.

---

### **Which Option is Best for Large Datasets?**

| **Method**               | **Memory Usage**       | **Processing Style**      | **Use Case**                                   |
|--------------------------|------------------------|---------------------------|-----------------------------------------------|
| `RowMapper` with `query` | High (loads all rows)  | All rows at once          | Small or moderate datasets.                   |
| `RowCallbackHandler`     | Minimal               | Row-by-row                | Best for large datasets with manual processing. |
| `ResultSetExtractor`     | Minimal               | Custom processing         | When you need to process rows and return a custom result. |
| Streaming with `RowMapper` | Minimal (streaming)   | Row-by-row                | When working with extremely large datasets efficiently. |
| `queryForRowSet`         | Minimal               | Row-by-row (disconnected) | When you need a disconnected, lightweight result set. |
