### JDBC and DAO in Java

#### **What is JDBC?**
JDBC (Java Database Connectivity) is an API in Java that allows you to interact with relational databases. It provides methods to connect to a database, execute SQL queries, and retrieve/manipulate data.

- **Key Components of JDBC**:
  1. **DriverManager**: Manages database drivers and establishes connections.
  2. **Connection**: Represents a connection to the database.
  3. **Statement/PreparedStatement**: Used to execute SQL queries.
  4. **ResultSet**: Represents the results of a query.

#### **What is DAO?**
DAO (Data Access Object) is a design pattern used to abstract and encapsulate all access to a data source. It separates the persistence logic from the business logic, making the code modular and easier to maintain.

- **Responsibilities of a DAO**:
  - Handle database operations such as `CREATE`, `READ`, `UPDATE`, and `DELETE` (CRUD).
  - Use JDBC or an ORM (like Hibernate) to interact with the database.
  - Return data in the form of objects (e.g., POJOs).

---

### Example: Querying Data Using JDBC and DAO

#### **1. Database Schema**
The database table is defined as follows (from the provided image):

```sql
CREATE TABLE course (
    course_id INTEGER IDENTITY NOT NULL,
    title VARCHAR(80) NOT NULL,          -- Course Title
    description VARCHAR(500) NOT NULL,  -- Course Description
    link VARCHAR(255) NOT NULL,         -- Course Landing Page Link
    CONSTRAINT pk_course_course_id PRIMARY KEY (course_id)
);
```

#### **2. DAO Interface**
The DAO interface defines the methods for CRUD operations. 

```java
package dev.danvega.dao;

import java.util.List;
import java.util.Optional;

public interface DAO<T> {
    List<T> list();                // Retrieve all records
    void create(T t);              // Insert a new record
    Optional<T> get(int id);       // Retrieve a record by ID
    void update(T t, int id);      // Update a record by ID
    void delete(int id);           // Delete a record by ID
}
```

- **Purpose**:
  - `list()`: Fetch all records.
  - `create(T t)`: Add a new record.
  - `get(int id)`: Retrieve a specific record by its ID.
  - `update(T t, int id)`: Modify an existing record.
  - `delete(int id)`: Remove a record by its ID.

---

#### **3. POJO Class**
The `Course` class represents the `course` table in the database.

```java
public class Course {
    private int courseId;
    private String title;
    private String description;
    private String link;

    // Default constructor
    public Course() {}

    // Parameterized constructor
    public Course(String title, String description, String link) {
        this.title = title;
        this.description = description;
        this.link = link;
    }

    // Getters and setters
    public int getCourseId() {
        return courseId;
    }

    public void setCourseId(int courseId) {
        this.courseId = courseId;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getLink() {
        return link;
    }

    public void setLink(String link) {
        this.link = link;
    }
}
```

---

#### **4. DAO Implementation Using JDBC**

The `CourseJdbcDAO` class implements the `DAO` interface and uses JDBC to interact with the database.

```java
import org.springframework.stereotype.Component;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import java.util.List;
import java.util.Optional;

@Component
public class CourseJdbcDAO implements DAO<Course> {

    private JdbcTemplate jdbcTemplate;

    // RowMapper to map database rows to Course objects
    RowMapper<Course> rowMapper = (rs, rowNum) -> {
        Course course = new Course();
        course.setCourseId(rs.getInt("course_id"));
        course.setTitle(rs.getString("title"));
        course.setDescription(rs.getString("description"));
        course.setLink(rs.getString("link"));
        return course;
    };

    // Constructor injection for JdbcTemplate
    public CourseJdbcDAO(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public List<Course> list() {
        String sql = "SELECT course_id, title, description, link FROM course";
        return jdbcTemplate.query(sql, rowMapper);
    }

    @Override
    public void create(Course course) {
        String sql = "INSERT INTO course (title, description, link) VALUES (?, ?, ?)";
        jdbcTemplate.update(sql, course.getTitle(), course.getDescription(), course.getLink());
    }

    @Override
    public Optional<Course> get(int id) {
        String sql = "SELECT course_id, title, description, link FROM course WHERE course_id = ?";
        return jdbcTemplate.query(sql, rowMapper, id).stream().findFirst();
    }

    @Override
    public void update(Course course, int id) {
        String sql = "UPDATE course SET title = ?, description = ?, link = ? WHERE course_id = ?";
        jdbcTemplate.update(sql, course.getTitle(), course.getDescription(), course.getLink(), id);
    }

    @Override
    public void delete(int id) {
        String sql = "DELETE FROM course WHERE course_id = ?";
        jdbcTemplate.update(sql, id);
    }
}
```

- **Key Points**:
  - **`RowMapper`**: Converts each row of the result set into a `Course` object.
  - **JDBC Queries**:
    - `SELECT`: Used in `list()` and `get()`.
    - `INSERT`: Used in `create()`.
    - `UPDATE`: Used in `update()`.
    - `DELETE`: Used in `delete()`.

---

#### **5. Application Configuration**
The `application.properties` file configures the H2 database and Spring Boot:

```properties
spring.datasource.name=course_platform
spring.datasource.generate-unique-name=false
spring.h2.console.enabled=true
```

- **H2 Console**: Enabled for debugging and viewing the database.

---

#### **6. Main Application**
The `CoursePlatformApplication` class initializes the application and queries the database.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

import java.util.List;

@SpringBootApplication
public class CoursePlatformApplication {

    private static DAO<Course> dao;

    public CoursePlatformApplication(DAO<Course> dao) {
        this.dao = dao;
    }

    public static void main(String[] args) {
        SpringApplication.run(CoursePlatformApplication.class, args);

        System.out.println("\nAll Courses -----------------------------\n");
        List<Course> courses = dao.list();
        courses.forEach(System.out::println);
    }
}
```

- **Key Steps**:
  - The DAO is injected into the application.
  - The `list()` method is called to fetch all courses from the database.
  - Results are printed to the console.

---

#### **7. Sample Data**
The `INSERT` statements populate the `course` table with sample data:

```sql
INSERT INTO course (title, description, link) VALUES 
('Vue.js for Beginners', 'A beginner''s guide to learn Vue.js', 'https://vuejs.org'),
('The Complete Apache Groovy Developer Course', 'Everything you need to know about Groovy', 'https://groovy-lang.org'),
('Getting Started with Spring Boot 2', 'Learn how to build a real application using Spring Boot', 'https://spring.io'),
('Learn Spring Boot', 'Spring Boot gives you all the power of the Spring Framework', 'https://spring.io'),
('Angular 4 Java Developers', 'Learn how to build Spring Boot & Angular apps', 'https://angular.io');
```

---

### Summary of JDBC and DAO Workflow

1. **DAO Interface**:
   - Defines CRUD methods.
2. **DAO Implementation**:
   - Implements the methods using JDBC.
3. **RowMapper**:
   - Maps database rows to Java objects.
4. **Application**:
   - Injects the DAO and interacts with it to perform database operations.
5. **Database**:
   - The H2 database is used for this example, with a schema matching the `Course` class.
