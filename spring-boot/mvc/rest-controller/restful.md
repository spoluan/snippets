### **RestController in Spring MVC**

---

### **1. What is a RestController?**

A **RestController** in Spring MVC is a specialized controller used for building RESTful web services. It is a combination of the `@Controller` and `@ResponseBody` annotations. This means that:
- All methods in a class annotated with `@RestController` will return data directly as the HTTP response body, instead of rendering a view.
- The returned data is automatically serialized to JSON or XML (depending on the configuration and client request).

---

### **2. Key Features of RestController**

1. **Simplified REST API Development**:  
   - No need to annotate each method with `@ResponseBody`.  
   - Suitable for returning JSON or XML responses.

2. **Serialization Support**:  
   - Spring automatically converts Java objects to JSON or XML using libraries like Jackson or JAXB.

3. **HTTP Methods**:  
   - Supports all HTTP methods (`GET`, `POST`, `PUT`, `DELETE`, etc.) through annotations like `@GetMapping`, `@PostMapping`, etc.

4. **Produces and Consumes**:  
   - You can specify the content type of the response using `produces` and `consumes` attributes in mapping annotations.

---

### **3. How to Create a RestController**

#### **Example: Basic RestController**
```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloRestController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, World!";
    }
}
```

##### **Explanation**:
- The `@RestController` annotation ensures that the `sayHello` method's return value is sent directly as an HTTP response body.
- Accessing `/hello` will return the string `"Hello, World!"` in the response.

---

### **4. Example: Todo Controller**

The provided `TodoController` example demonstrates how to use `@RestController` for managing a list of tasks (todos).

#### **Code: TodoController**
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

@RestController
public class TodoController {

    private List<String> todos = new ArrayList<>();

    @PostMapping(path = "/todos", produces = MediaType.APPLICATION_JSON_VALUE)
    public List<String> addTodo(@RequestParam("todo") String todo) {
        todos.add(todo);
        return todos;
    }

    @GetMapping(path = "/todos", produces = MediaType.APPLICATION_JSON_VALUE)
    public List<String> getTodos() {
        return todos;
    }
}
```

##### **Explanation**:
1. **POST `/todos`**:
   - Adds a new todo to the list.
   - Returns the updated list of todos in JSON format.
2. **GET `/todos`**:
   - Returns the current list of todos in JSON format.

---

### **5. Testing RestController**

Spring Boot provides the `MockMvc` framework to test controllers.

#### **Code: Testing TodoController**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.Matchers.containsString;

@SpringBootTest
@AutoConfigureMockMvc
public class TodoControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void addTodo() throws Exception {
        mockMvc.perform(post("/todos")
                .accept(MediaType.APPLICATION_JSON)
                .param("todo", "Eko"))
                .andExpectAll(
                        status().isOk(),
                        content().string(containsString("Eko"))
                );
    }

    @Test
    void getTodos() throws Exception {
        mockMvc.perform(get("/todos")
                .accept(MediaType.APPLICATION_JSON))
                .andExpectAll(
                        status().isOk(),
                        content().string(containsString("Eko"))
                );
    }
}
```

##### **Explanation**:
- **`addTodo` Test**:
  - Simulates a POST request to `/todos` with a parameter `todo=Eko`.
  - Verifies that the response contains the string `"Eko"`.
- **`getTodos` Test**:
  - Simulates a GET request to `/todos`.
  - Verifies that the response contains the string `"Eko"`.

---

### **6. Advanced Examples**

#### **6.1 Returning JSON Objects**
```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.Map;

@RestController
public class JsonController {

    @GetMapping("/json")
    public Map<String, String> getJson() {
        return Map.of("message", "Hello, JSON!", "status", "success");
    }
}
```

##### **Output**:
```json
{
  "message": "Hello, JSON!",
  "status": "success"
}
```

---

#### **6.2 Handling Path Variables**
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class PathVariableController {

    @GetMapping("/user/{id}")
    public String getUserById(@PathVariable("id") String userId) {
        return "User ID: " + userId;
    }
}
```

##### **Explanation**:
- Accessing `/user/123` will return `"User ID: 123"`.

---

#### **6.3 Handling Query Parameters**
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class QueryParamController {

    @GetMapping("/search")
    public String search(@RequestParam("query") String query) {
        return "Search results for: " + query;
    }
}
```

##### **Explanation**:
- Accessing `/search?query=spring` will return `"Search results for: spring"`.

---

#### **6.4 Using `@RequestBody` for JSON Input**
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class RequestBodyController {

    @PostMapping("/create")
    public String createUser(@RequestBody User user) {
        return "User created: " + user.getName();
    }

    static class User {
        private String name;
        private int age;

        // Getters and Setters
        public String getName() { return name; }
        public void setName(String name) { this.name = name; }
        public int getAge() { return age; }
        public void setAge(int age) { this.age = age; }
    }
}
```

##### **Input**:
```json
{
  "name": "John",
  "age": 25
}
```

##### **Output**:
```
User created: John
```

---

#### **6.5 Using `@ResponseStatus` for Custom HTTP Status**
```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
public class StatusController {

    @GetMapping("/notfound")
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public String notFound() {
        return "Resource not found";
    }
}
```

##### **Explanation**:
- Accessing `/notfound` will return a **404 Not Found** status with the message `"Resource not found"`.

---

### **7. Summary**

| **Feature**                       | **Description**                                                                 |
|-----------------------------------|---------------------------------------------------------------------------------|
| **Annotation**                    | `@RestController` combines `@Controller` and `@ResponseBody`.                  |
| **Default Response Format**       | JSON (requires Jackson dependency).                                            |
| **HTTP Methods**                  | Supports `GET`, `POST`, `PUT`, `DELETE`, etc.                                  |
| **Serialization**                 | Automatically converts Java objects to JSON or XML.                            |
| **Path Variables**                | Use `@PathVariable` to capture dynamic segments in URLs.                       |
| **Query Parameters**              | Use `@RequestParam` to capture query parameters.                               |
| **Request Body**                  | Use `@RequestBody` to accept JSON input.                                       |
| **Custom HTTP Status**            | Use `@ResponseStatus` to set custom HTTP statuses.                             |

---

### **8. Practical Use Cases**

1. **Building REST APIs**:  
   - Example: User management, product catalog, order processing.
2. **Microservices Communication**:  
   - Example: Exposing APIs for other services to consume.
3. **Single Page Applications (SPAs)**:  
   - Example: Providing backend APIs for frontend frameworks like React or Angular.

The `@RestController` annotation simplifies the creation of RESTful web services, making it a powerful and essential tool for modern web development.
