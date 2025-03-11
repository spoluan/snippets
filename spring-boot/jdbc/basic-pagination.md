 
### 1. **Basic Pagination Query in PostgreSQL**

The general syntax for pagination in PostgreSQL is:

```sql
SELECT columns
FROM table
ORDER BY column
LIMIT page_size OFFSET offset;
```

- **`LIMIT`**: Specifies the maximum number of rows to return.
- **`OFFSET`**: Specifies the number of rows to skip before starting to return rows.
- **`ORDER BY`**: Always include an `ORDER BY` clause to ensure consistent ordering of results across pages.

#### Example:
```sql
SELECT course_id, title, description, link
FROM course
ORDER BY course_id
LIMIT 10 OFFSET 20;
```
This query retrieves 10 rows, starting from the 21st row (offset is 20).

---

### 2. **Java Implementation with `JdbcTemplate`**

Here’s how you can implement pagination in your Java code using `JdbcTemplate`:

#### Method to Fetch Paginated Results
```java
public List<Course> getCoursesWithPagination(int page, int size) {
    String sql = "SELECT course_id, title, description, link " +
                 "FROM course " +
                 "ORDER BY course_id " +
                 "LIMIT ? OFFSET ?";

    int offset = (page - 1) * size; // Calculate offset
    return jdbcTemplate.query(sql, rowMapper, size, offset);
}
```

- **Parameters**:
  - `page`: The current page number (starting from 1).
  - `size`: The number of rows per page.
- **`offset` Calculation**: \[(page - 1) * size\].

#### Example Usage:
```java
List<Course> courses = getCoursesWithPagination(2, 10); // Fetch page 2 with 10 courses per page
```

---

### 3. **Returning Paginated Results with Metadata**

If you want to return additional metadata (e.g., total records, total pages, etc.), you can extend the implementation as follows:

#### Method to Fetch Paginated Results with Metadata
```java
public PaginatedResponse<Course> getCoursesWithPagination(int page, int size) {
    // Query to fetch the paginated results
    String dataQuery = "SELECT course_id, title, description, link " +
                       "FROM course " +
                       "ORDER BY course_id " +
                       "LIMIT ? OFFSET ?";
    int offset = (page - 1) * size;
    List<Course> courses = jdbcTemplate.query(dataQuery, rowMapper, size, offset);

    // Query to fetch the total count of records
    String countQuery = "SELECT COUNT(*) FROM course";
    int totalRecords = jdbcTemplate.queryForObject(countQuery, Integer.class);

    // Calculate total pages
    int totalPages = (int) Math.ceil((double) totalRecords / size);

    // Return paginated response
    return new PaginatedResponse<>(courses, page, size, totalRecords, totalPages);
}
```

#### PaginatedResponse Class
```java
public class PaginatedResponse<T> {
    private List<T> data;
    private int currentPage;
    private int pageSize;
    private int totalRecords;
    private int totalPages;

    public PaginatedResponse(List<T> data, int currentPage, int pageSize, int totalRecords, int totalPages) {
        this.data = data;
        this.currentPage = currentPage;
        this.pageSize = pageSize;
        this.totalRecords = totalRecords;
        this.totalPages = totalPages;
    }

    // Getters and setters
}
```

#### Example Usage:
```java
PaginatedResponse<Course> response = getCoursesWithPagination(2, 10);
System.out.println("Page: " + response.getCurrentPage());
System.out.println("Total Pages: " + response.getTotalPages());
response.getData().forEach(System.out::println);
```

---

### 4. **Performance Considerations in PostgreSQL**

While `LIMIT` and `OFFSET` are straightforward, they can become inefficient for large datasets because PostgreSQL must scan and skip rows for each page. Here are some strategies to improve performance:

#### a. **Use Keyset Pagination (Seek Method)**
Instead of using `OFFSET`, you can use **keyset pagination** (also known as the seek method). This approach uses a "cursor" based on a unique column (e.g., `course_id`) to fetch the next set of rows. It avoids skipping rows and is more efficient for large datasets.

Example Query:
```sql
SELECT course_id, title, description, link
FROM course
WHERE course_id > ?
ORDER BY course_id
LIMIT ?;
```

Java Implementation:
```java
public List<Course> getCoursesWithKeysetPagination(int lastCourseId, int size) {
    String sql = "SELECT course_id, title, description, link " +
                 "FROM course " +
                 "WHERE course_id > ? " +
                 "ORDER BY course_id " +
                 "LIMIT ?";
    return jdbcTemplate.query(sql, rowMapper, lastCourseId, size);
}
```

- **`lastCourseId`**: The ID of the last course from the previous page.
- This approach is faster for large datasets but requires maintaining the last ID in the application or client.

---

#### b. **Indexing**
Ensure that the column(s) used in the `ORDER BY` or `WHERE` clause are indexed. For example:
```sql
CREATE INDEX idx_course_id ON course(course_id);
```

---

### 5. **Example API Response for Pagination**

Here’s an example JSON response for paginated data:

```json
{
    "data": [
        {
            "course_id": 11,
            "title": "Advanced Java",
            "description": "Learn advanced Java concepts",
            "link": "http://example.com/advanced-java"
        },
        {
            "course_id": 12,
            "title": "Spring Boot",
            "description": "Build REST APIs with Spring Boot",
            "link": "http://example.com/spring-boot"
        }
    ],
    "currentPage": 2,
    "pageSize": 10,
    "totalRecords": 50,
    "totalPages": 5
}
```
