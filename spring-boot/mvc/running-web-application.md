 

### 1. **Running a Web Application in Spring Boot**

#### Key Points:
- **Embedded Apache Tomcat**:
  - Spring Boot includes an **embedded Apache Tomcat server** by default.
  - This eliminates the need to manually deploy the application to an external Tomcat server.
  - Applications no longer need to be packaged as `.war` files; instead, they can run as standalone `.jar` files.

- **Default Port**:
  - By default, Spring Boot runs the embedded Tomcat server on **port 8080**.

- **Changing the Port**:
  - To change the default port, modify the `server.port` property in the `application.properties` file:
    ```properties
    server.port=9090
    ```

#### Benefits:
- Simplifies the deployment process.
- Allows developers to run the application directly without requiring additional server setup.

---

### 2. **Servlet Request and Response**

#### Key Points:
- **Controller with RequestMapping**:
  - When creating a controller handler using `@RequestMapping`, you can inject `HttpServletRequest` and `HttpServletResponse` as method parameters.
  - These objects allow direct access to HTTP request and response data.

- **No Parameter Order Rules**:
  - Spring WebMVC automatically detects the type and position of parameters, so there is no strict rule about their order in the method signature.

---

### 3. **Code Example: Servlet Request and Response**

#### Code:
```java
@Controller
public class HelloController {

    @RequestMapping(path = "/hello")
    public void helloWorld(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String name = request.getParameter("name"); // Get "name" parameter from the request
        if (Objects.isNull(name)) { // Check if the parameter is null
            name = "Guest"; // Default value if no name is provided
        }
        response.getWriter().println("Hello " + name); // Write response back to the client
    }
}
```

#### Explanation:
1. **Annotations**:
   - `@Controller`: Marks the class as a Spring MVC controller.
   - `@RequestMapping(path = "/hello")`: Maps the `/hello` URL to the `helloWorld` method.

2. **Parameters**:
   - `HttpServletRequest request`: Represents the HTTP request object.
     - Used to retrieve request parameters (e.g., `name`).
   - `HttpServletResponse response`: Represents the HTTP response object.
     - Used to send data back to the client.

3. **Logic**:
   - The method retrieves the `name` parameter from the HTTP request.
   - If the parameter is missing or null, it defaults to `"Guest"`.
   - It then writes a greeting message (`"Hello Guest"`) to the HTTP response.

---

### 4. **How It Works**

- **Request Flow**:
  1. A client sends a request to `http://localhost:8080/hello?name=John`.
  2. The `helloWorld` method is triggered.
  3. The `name` parameter is extracted from the request.
  4. The response is sent back as `"Hello John"`.

- **Default Behavior**:
  - If no `name` parameter is provided (e.g., `http://localhost:8080/hello`), the response will be `"Hello Guest"`.

---

### 5. **Advantages of Using `HttpServletRequest` and `HttpServletResponse`**
- Provides **low-level access** to HTTP requests and responses.
- Useful for handling custom request/response logic (e.g., working with headers, cookies, or streams).
- Can be combined with Spring's higher-level abstractions for more flexibility.

---

### Summary of Concepts:
1. **Spring Boot Web Server**:
   - Embedded Tomcat simplifies deployment.
   - Default port is `8080`, but you can change it via `application.properties`.

2. **Servlet Request and Response**:
   - `HttpServletRequest` allows access to request data (e.g., parameters, headers).
   - `HttpServletResponse` is used to send responses back to the client.

3. **Code Example**:
   - Demonstrates how to handle a request parameter (`name`) and send a dynamic response.

