

### 1. **`query`**
- **Purpose**: Execute a query that may return multiple rows and map each row to an object or a list of objects.
- **Return Type**: `List<T>` or other collections.
- **Common Use Case**: When you expect zero, one, or many rows.

Example:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
List<Course> courses = jdbcTemplate.query(sql, rowMapper, "%Java%");
```

---

### 2. **`queryForObject`**
- **Purpose**: Execute a query that is expected to return exactly one row and map it to an object.
- **Return Type**: A single object of type `T`.
- **Common Use Case**: When you expect **exactly one row** in the result.

Example:
```java
String sql = "SELECT title FROM course WHERE course_id = ?";
String title = jdbcTemplate.queryForObject(sql, String.class, 1);
```

---

### 3. **`queryForList`**
- **Purpose**: Execute a query that returns multiple rows and map each row to a `Map<String, Object>` or a specific type.
- **Return Type**: `List<Map<String, Object>>` or `List<T>`.
- **Common Use Case**: When you need a list of results but don't want to use a `RowMapper`.

Example:
```java
String sql = "SELECT title FROM course WHERE title LIKE ?";
List<String> titles = jdbcTemplate.queryForList(sql, String.class, "%Java%");
```

If you want to retrieve rows as maps:
```java
String sql = "SELECT * FROM course WHERE title LIKE ?";
List<Map<String, Object>> rows = jdbcTemplate.queryForList(sql, "%Java%");
```

---

### 4. **`queryForMap`**
- **Purpose**: Execute a query that returns exactly one row and map it to a `Map<String, Object>`.
- **Return Type**: `Map<String, Object>`.
- **Common Use Case**: When you need to retrieve a single row as a key-value map.

Example:
```java
String sql = "SELECT * FROM course WHERE course_id = ?";
Map<String, Object> course = jdbcTemplate.queryForMap(sql, 1);
```

- Throws `EmptyResultDataAccessException` if no rows are returned.
- Throws `IncorrectResultSizeDataAccessException` if more than one row is returned.

---

### 5. **`update`**
- **Purpose**: Execute an `INSERT`, `UPDATE`, or `DELETE` statement.
- **Return Type**: `int` (number of rows affected).
- **Common Use Case**: When performing write operations on the database.

Example:
```java
String sql = "UPDATE course SET title = ? WHERE course_id = ?";
int rowsUpdated = jdbcTemplate.update(sql, "Updated Title", 1);
System.out.println("Rows updated: " + rowsUpdated);
```

---

### 6. **`batchUpdate`**
- **Purpose**: Execute a batch of SQL statements in one go.
- **Return Type**: `int[]` (number of rows affected for each statement).
- **Common Use Case**: When you need to execute multiple insert/update/delete operations efficiently.

Example:
```java
String sql = "INSERT INTO course (title, description) VALUES (?, ?)";
List<Object[]> batchArgs = Arrays.asList(
    new Object[]{"Java Basics", "Intro to Java"},
    new Object[]{"Spring Boot", "Learn Spring Boot"}
);

int[] rowsAffected = jdbcTemplate.batchUpdate(sql, batchArgs);
System.out.println("Rows affected: " + Arrays.toString(rowsAffected));
```

---

### 7. **`execute`**
- **Purpose**: Execute a generic SQL statement (e.g., DDL like `CREATE TABLE` or custom SQL).
- **Return Type**: `void` or a custom result depending on the callback.
- **Common Use Case**: When you need to execute a custom SQL statement or procedure.

Example (DDL statement):
```java
String sql = "CREATE TABLE IF NOT EXISTS course (course_id SERIAL PRIMARY KEY, title VARCHAR(255))";
jdbcTemplate.execute(sql);
```

Example (using a callback):
```java
jdbcTemplate.execute("SELECT NOW()", (PreparedStatementCallback<Object>) ps -> {
    ResultSet rs = ps.executeQuery();
    if (rs.next()) {
        return rs.getTimestamp(1);
    }
    return null;
});
```

---

### 8. **`call`**
- **Purpose**: Execute a stored procedure or a function.
- **Return Type**: `Map<String, Object>` (output parameters and result sets).
- **Common Use Case**: When working with stored procedures in the database.

Example:
```java
SimpleJdbcCall jdbcCall = new SimpleJdbcCall(jdbcTemplate).withProcedureName("my_procedure");
Map<String, Object> result = jdbcCall.execute(Map.of("input_param", 123));
System.out.println(result);
```

---

### 9. **`queryForRowSet`**
- **Purpose**: Execute a query and return a `SqlRowSet`, which is similar to a `ResultSet` but disconnected from the database.
- **Return Type**: `SqlRowSet`.
- **Common Use Case**: When you need a lightweight, read-only result set.

Example:
```java
String sql = "SELECT * FROM course";
SqlRowSet rowSet = jdbcTemplate.queryForRowSet(sql);
while (rowSet.next()) {
    System.out.println("Course ID: " + rowSet.getInt("course_id"));
}
```

---

### 10. **`SimpleJdbcInsert` and `SimpleJdbcCall`**
These are utility classes for simplifying common operations like inserts and stored procedure calls.

#### SimpleJdbcInsert:
```java
SimpleJdbcInsert insert = new SimpleJdbcInsert(jdbcTemplate)
    .withTableName("course")
    .usingGeneratedKeyColumns("course_id");

Map<String, Object> parameters = Map.of(
    "title", "New Course",
    "description", "Course Description"
);

Number newId = insert.executeAndReturnKey(parameters);
System.out.println("Inserted course ID: " + newId);
```

#### SimpleJdbcCall (for stored procedures):
```java
SimpleJdbcCall call = new SimpleJdbcCall(jdbcTemplate)
    .withProcedureName("my_procedure");

Map<String, Object> result = call.execute(Map.of("param1", 123));
System.out.println(result);
```

---

### Summary of Key Methods

| **Method**              | **Use Case**                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| `query`                | Retrieve multiple rows and map them to a list of objects.                   |
| `queryForObject`       | Retrieve a single row and map it to an object.                              |
| `queryForList`         | Retrieve multiple rows as a list of a specific type or maps.               |
| `queryForMap`          | Retrieve a single row as a map.                                             |
| `update`               | Execute `INSERT`, `UPDATE`, or `DELETE` statements.                        |
| `batchUpdate`          | Execute multiple updates in a batch.                                       |
| `execute`              | Execute a generic SQL statement (DDL, custom SQL).                        |
| `queryForRowSet`       | Retrieve results as a `SqlRowSet` (disconnected, lightweight).             |
| `call`                 | Execute stored procedures or functions.                                    |
| `SimpleJdbcInsert`     | Simplify insert operations.                                                |
| `SimpleJdbcCall`       | Simplify calling stored procedures.                                        |

---

### Choosing the Right Method
- Use **`query`** when you expect multiple rows.
- Use **`queryForObject`** when you expect exactly one row.
- Use **`update`** for write operations (insert, update, delete).
- Use **`batchUpdate`** for batch operations.
- Use **`execute`** for custom SQL or DDL statements.
- Use **`queryForMap`** or **`queryForList`** for quick access to raw data without custom mapping.
- Use **`SimpleJdbcInsert`** and **`SimpleJdbcCall`** for simplified inserts or stored procedure calls.

