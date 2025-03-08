### All About Request Mapping in Spring Boot

The `@RequestMapping` annotation in Spring Boot is used to map HTTP requests to handler methods in controller classes. It is one of the most fundamental annotations in Spring Web MVC for defining routes and managing incoming requests.

---

### 1. **What is `@RequestMapping`?**
- `@RequestMapping` is an annotation provided by Spring Framework to map web requests (URLs) to specific methods in a controller.
- It allows developers to specify:
  - The **URL path**.
  - The **HTTP method** (GET, POST, etc.).
  - Additional configurations like headers, parameters, and content types.

---

### 2. **Key Features of `@RequestMapping`**
- **Path Mapping**:
  - Maps specific URL patterns to methods.
- **HTTP Method Mapping**:
  - Allows specifying HTTP methods like GET, POST, PUT, DELETE, etc.
- **Flexible Configuration**:
  - Supports headers, parameters, and content type conditions.

---

### 3. **Basic Usage**

#### Example Code:
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class HelloController {

    @RequestMapping(path = "/hello", method = RequestMethod.GET)
    public String sayHello() {
        return "hello"; // Returns the name of the view (e.g., hello.html)
    }
}
```

#### Explanation:
1. **Annotation**:
   - `@RequestMapping(path = "/hello", method = RequestMethod.GET)` maps the `/hello` URL to the `sayHello` method for HTTP GET requests.
2. **Return Value**:
   - The method returns the name of the view to be rendered.

---

### 4. **Advanced Features of `@RequestMapping`**

#### a. **Mapping by HTTP Methods**:
You can specify the HTTP method using the `method` attribute.
```java
@RequestMapping(path = "/submit", method = RequestMethod.POST)
public String handleFormSubmission() {
    return "success";
}
```

#### b. **Mapping by Parameters**:
You can restrict the mapping based on request parameters.
```java
@RequestMapping(path = "/filter", params = "type=admin")
public String filterAdmin() {
    return "adminPage";
}
```

#### c. **Mapping by Headers**:
You can map requests based on specific headers.
```java
@RequestMapping(path = "/api", headers = "X-API-KEY=12345")
public String apiHandler() {
    return "apiResponse";
}
```

#### d. **Mapping by Content Type**:
You can restrict the mapping based on content types.
```java
@RequestMapping(path = "/json", consumes = "application/json", produces = "application/json")
public String handleJson() {
    return "{\"message\":\"JSON Response\"}";
}
```

---

### 5. **Simplified Annotations for HTTP Methods**
In Spring Boot, `@RequestMapping` can be replaced with more specific annotations for common HTTP methods:
- `@GetMapping` → For HTTP GET requests.
- `@PostMapping` → For HTTP POST requests.
- `@PutMapping` → For HTTP PUT requests.
- `@DeleteMapping` → For HTTP DELETE requests.
- `@PatchMapping` → For HTTP PATCH requests.

#### Example:
```java
@GetMapping("/hello")
public String sayHello() {
    return "hello";
}
```

---

### 6. **Combining `@RequestMapping` with Path Variables**
You can use path variables to capture dynamic values from the URL.
```java
@RequestMapping(path = "/user/{id}")
public String getUser(@PathVariable("id") String userId) {
    return "User ID: " + userId;
}
```

---

### 7. **Benefits of Using `@RequestMapping`**
- **Flexible Routing**:
  - Supports advanced routing options like headers, parameters, and content types.
- **Customizable**:
  - Allows fine-grained control over request handling.
- **Extensible**:
  - Works seamlessly with path variables and query parameters.

---

### 8. **Key Notes**
- `@RequestMapping` is versatile but can be verbose for simple use cases. Use specific annotations like `@GetMapping` for simplicity.
- Supports multiple configurations in a single annotation, making it powerful for complex routing requirements.

For more details, refer to the official documentation:  
[RequestMapping Documentation](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html)
