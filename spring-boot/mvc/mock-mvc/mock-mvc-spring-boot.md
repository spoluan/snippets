### MockMVC in Spring Boot

`MockMvc` is a powerful feature provided by Spring for testing Spring WebMVC controllers. It allows you to perform **unit tests** on your controllers without the need to start the full web server. This makes testing faster and eliminates the need for manual testing via browsers or HTTP clients.

---

### **1. What is MockMVC?**

- `MockMvc` is a part of the **Spring Test** module.
- It allows testing controllers by simulating HTTP requests and responses in a lightweight way.
- You can test:
  - Controller logic.
  - HTTP response codes.
  - Content of the response.
  - View rendering (if applicable).
- You don't need to start the application or deploy it to a server for testing.

---

### **2. Benefits of MockMVC**

1. **Fast Testing**:
   - No need to start the application or deploy it.
   - Tests run quickly as only the controller layer is tested.

2. **No Browser or HTTP Client Required**:
   - Tests are performed programmatically without manual interaction.

3. **Comprehensive Testing**:
   - Simulates HTTP requests with custom headers, parameters, and payloads.
   - Verifies HTTP status, response content, and headers.

4. **Integration with JUnit**:
   - Works seamlessly with JUnit and other testing frameworks.

---

### **3. Setting Up MockMVC**

#### **Dependencies**
Ensure you have the following dependencies in your `pom.xml` (for Maven projects):
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

#### **Annotations**
- `@SpringBootTest`: Loads the full Spring application context for testing.
- `@AutoConfigureMockMvc`: Automatically configures and injects `MockMvc` into your test class.

#### **Example Test Class Setup**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

@SpringBootTest
@AutoConfigureMockMvc
public class MyControllerTest {

    @Autowired
    private MockMvc mockMvc;

    // Test methods go here
}
```

---

### **4. Writing MockMVC Tests**

#### **Basic Example: Testing HTTP GET Request**
```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;

@Test
public void testHelloEndpoint() throws Exception {
    mockMvc.perform(get("/hello"))
           .andExpect(status().isOk()) // Check if status is 200 OK
           .andExpect(content().string("Hello World")); // Check response content
}
```

#### **Testing Query Parameters**
```java
@Test
public void testHelloWithQueryParam() throws Exception {
    mockMvc.perform(get("/hello").queryParam("name", "John"))
           .andExpect(status().isOk())
           .andExpect(content().string("Hello John"));
}
```

#### **Testing HTTP POST Request**
```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@Test
public void testPostEndpoint() throws Exception {
    mockMvc.perform(post("/submit")
           .contentType("application/json")
           .content("{\"name\":\"John\"}")) // JSON request body
           .andExpect(status().isCreated()); // Expect HTTP 201 Created
}
```

#### **Testing Headers**
```java
@Test
public void testWithCustomHeader() throws Exception {
    mockMvc.perform(get("/api")
           .header("X-Custom-Header", "12345"))
           .andExpect(status().isOk());
}
```

#### **Testing Response Content Type**
```java
@Test
public void testResponseContentType() throws Exception {
    mockMvc.perform(get("/hello"))
           .andExpect(status().isOk())
           .andExpect(content().contentType("text/plain"));
}
```

---

### **5. Using Static Imports**

To simplify the code, you can use static imports for utility methods. Here are the most common classes:

1. **`MockMvcBuilders`**: For building `MockMvc` instances.
2. **`MockMvcRequestBuilders`**: For creating HTTP requests (e.g., `get`, `post`).
3. **`MockMvcResultMatchers`**: For verifying response properties (e.g., status, content).
4. **`MockMvcResultHandlers`**: For handling results (e.g., logging).

#### Example of Static Imports:
```java
import static org.springframework.test.web.servlet.MockMvcBuilders.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.*;
```

---

### **6. Advanced MockMVC Examples**

#### **Testing Path Variables**
```java
@Test
public void testWithPathVariable() throws Exception {
    mockMvc.perform(get("/user/{id}", 1))
           .andExpect(status().isOk())
           .andExpect(content().string("User ID: 1"));
}
```

#### **Testing Forwarded URL**
```java
@Test
public void testForwardedUrl() throws Exception {
    mockMvc.perform(get("/form"))
           .andExpect(status().isOk())
           .andExpect(forwardedUrl("/WEB-INF/layouts/main.jsp"));
}
```

#### **Testing Error Response**
```java
@Test
public void testErrorResponse() throws Exception {
    mockMvc.perform(get("/nonexistent"))
           .andExpect(status().isNotFound()); // Expect HTTP 404 Not Found
}
```

#### **Testing JSON Response**
```java
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;

@Test
public void testJsonResponse() throws Exception {
    mockMvc.perform(get("/user"))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$.name").value("John")) // Check JSON field "name"
           .andExpect(jsonPath("$.age").value(30));     // Check JSON field "age"
}
```

---

### **7. Building MockMVC Manually**

If you don't use `@AutoConfigureMockMvc`, you can manually create a `MockMvc` instance.

#### Example:
```java
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

@Autowired
private WebApplicationContext context;

private MockMvc mockMvc;

@BeforeEach
public void setup() {
    mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
}
```

---

### **8. Unit Test Example for a Controller**

#### Controller Code:
```java
@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(@RequestParam(required = false) String name) {
        return "Hello " + (name != null ? name : "Guest");
    }
}
```

#### Test Code:
```java
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    public void testHelloGuest() throws Exception {
        mockMvc.perform(get("/hello"))
               .andExpect(status().isOk())
               .andExpect(content().string("Hello Guest"));
    }

    @Test
    public void testHelloName() throws Exception {
        mockMvc.perform(get("/hello").queryParam("name", "Eko"))
               .andExpect(status().isOk())
               .andExpect(content().string("Hello Eko"));
    }
}
```

---

### **9. MockMVC vs Full Integration Testing**

| **MockMVC**                                      | **Full Integration Testing**                     |
|--------------------------------------------------|-------------------------------------------------|
| Tests only the controller layer.                | Tests the entire application stack.             |
| Does not start the server.                      | Starts the server (using embedded Tomcat, etc.).|
| Faster and lightweight.                         | Slower due to full application context startup. |
| Ideal for unit testing controllers.             | Ideal for end-to-end testing.                   |

---

### **10. Key Notes**
- MockMVC is ideal for **unit testing controllers**.
- It is not suitable for testing database interactions or external services.
- Use tools like **Postman** or **RestAssured** for full API testing if needed.
