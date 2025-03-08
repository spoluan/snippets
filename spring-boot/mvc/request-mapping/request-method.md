### **Understanding Request Methods in Spring**

The **Request Method** in Spring determines which HTTP methods (e.g., GET, POST, PUT, DELETE) a controller method can handle. By default, if no HTTP method is specified, the controller method will accept **all HTTP methods**. However, you can explicitly define the allowed HTTP methods using the `method` attribute in `@RequestMapping` or by using specific shortcut annotations like `@GetMapping`, `@PostMapping`, etc.

---

### **1. What is a Request Method?**

- **Definition**:
  - A request method specifies the type of HTTP request (e.g., GET, POST) that a controller method can handle.

- **Purpose**:
  - To define how the server should respond to different HTTP requests like retrieving, creating, updating, or deleting resources.

- **Common HTTP Methods**:
  - **GET**: Retrieve data.
  - **POST**: Create new data.
  - **PUT**: Update existing data.
  - **PATCH**: Partially update data.
  - **DELETE**: Delete data.

---

### **2. Request Mapping with HTTP Methods**

In Spring, you can define the HTTP method for a controller method using `@RequestMapping` with the `method` attribute.

#### **Example: Using `@RequestMapping` with HTTP Methods**

```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class ExampleController {

    @RequestMapping(path = "/example", method = RequestMethod.GET)
    public String getExample() {
        return "This is a GET request";
    }

    @RequestMapping(path = "/example", method = RequestMethod.POST)
    public String postExample() {
        return "This is a POST request";
    }
}
```

---

### **3. Shortcut Annotations**

Spring provides shortcut annotations for each HTTP method to make the code simpler and more readable. These annotations are equivalent to using `@RequestMapping` with the `method` attribute.

| **HTTP Method** | **Shortcut Annotation** |
|------------------|--------------------------|
| GET              | `@GetMapping`           |
| POST             | `@PostMapping`          |
| PUT              | `@PutMapping`           |
| PATCH            | `@PatchMapping`         |
| DELETE           | `@DeleteMapping`        |

#### **Example: Using Shortcut Annotations**

```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class ExampleController {

    @GetMapping("/example")
    public String getExample() {
        return "This is a GET request";
    }

    @PostMapping("/example")
    public String postExample() {
        return "This is a POST request";
    }

    @PutMapping("/example")
    public String putExample() {
        return "This is a PUT request";
    }

    @DeleteMapping("/example")
    public String deleteExample() {
        return "This is a DELETE request";
    }
}
```

---

### **4. Default Behavior**

- If you don’t specify an HTTP method, the controller method will handle **all HTTP methods** by default.
  
#### **Example: No HTTP Method Specified**

```java
@RequestMapping("/example")
public String example() {
    return "This method handles all HTTP methods";
}
```

---

### **5. Handling Unsupported HTTP Methods**

If you send an unsupported HTTP method to a controller method, Spring will respond with **405 Method Not Allowed**.

#### **Example: Unit Test for Unsupported HTTP Method**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
public class ExampleControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testUnsupportedMethod() throws Exception {
        mockMvc.perform(post("/example")) // POST is not supported in this case
               .andExpect(status().isMethodNotAllowed());
    }
}
```

---

### **6. Combining Multiple HTTP Methods**

You can specify multiple HTTP methods for a single controller method using `@RequestMapping`.

#### **Example: Allowing Multiple HTTP Methods**

```java
@RequestMapping(path = "/example", method = {RequestMethod.GET, RequestMethod.POST})
public String handleMultipleMethods() {
    return "This method handles GET and POST requests";
}
```

---

### **7. Practical Examples**

#### **7.1. GET Request**

**Controller Code**:
```java
@GetMapping("/hello")
public String hello(@RequestParam String name) {
    return "Hello, " + name;
}
```

**Test Code**:
```java
mockMvc.perform(get("/hello").queryParam("name", "John"))
       .andExpect(status().isOk())
       .andExpect(content().string("Hello, John"));
```

---

#### **7.2. POST Request**

**Controller Code**:
```java
@PostMapping("/create")
public String create(@RequestBody String data) {
    return "Data created: " + data;
}
```

**Test Code**:
```java
mockMvc.perform(post("/create")
        .content("Sample Data")
        .contentType(MediaType.TEXT_PLAIN))
       .andExpect(status().isOk())
       .andExpect(content().string("Data created: Sample Data"));
```

---

#### **7.3. PUT Request**

**Controller Code**:
```java
@PutMapping("/update")
public String update(@RequestBody String data) {
    return "Data updated: " + data;
}
```

**Test Code**:
```java
mockMvc.perform(put("/update")
        .content("Updated Data")
        .contentType(MediaType.TEXT_PLAIN))
       .andExpect(status().isOk())
       .andExpect(content().string("Data updated: Updated Data"));
```

---

#### **7.4. DELETE Request**

**Controller Code**:
```java
@DeleteMapping("/delete/{id}")
public String delete(@PathVariable int id) {
    return "Deleted record with ID: " + id;
}
```

**Test Code**:
```java
mockMvc.perform(delete("/delete/1"))
       .andExpect(status().isOk())
       .andExpect(content().string("Deleted record with ID: 1"));
```

