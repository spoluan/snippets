### **Servlet in Java**

---

### **1. What is a Servlet?**

A **Servlet** is a Java class used to handle HTTP requests and responses in a web application. It is part of the **Java Servlet API** and runs within a servlet container (e.g., Apache Tomcat). Servlets are commonly used to build dynamic web applications.

**Key Features of Servlets:**
- Handles HTTP requests (e.g., GET, POST).
- Generates dynamic responses (e.g., HTML, JSON).
- Runs on a server-side Java web container.
- Provides lifecycle methods (`init`, `service`, `destroy`).

---

### **2. Servlet Lifecycle**

A servlet's lifecycle is managed by the servlet container and includes the following steps:

1. **Instantiation**:  
   The servlet container creates an instance of the servlet class.

2. **Initialization (`init` method)**:  
   The container calls the `init` method to initialize the servlet.

3. **Request Handling (`service` method)**:  
   The container calls the `service` method for every incoming HTTP request.  
   - Based on the HTTP method (e.g., GET, POST), the `doGet`, `doPost`, `doPut`, or other methods are called.

4. **Destruction (`destroy` method)**:  
   The container calls the `destroy` method before removing the servlet instance.

---

### **3. Creating a Basic Servlet**

#### **Code: Basic Hello Servlet**
```java
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet(urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("Hello from Servlet!");
    }
}
```

##### **Explanation**:
- `@WebServlet`: Maps the servlet to the `/hello` URL.
- `doGet`: Handles HTTP GET requests and writes "Hello from Servlet!" to the response.

---

### **4. Servlet Integration in Spring Boot**

By default, Spring Boot does not register custom servlets automatically. To enable servlet integration:
- Use the `@ServletComponentScan` annotation in the main application class.

#### **Code: Spring Boot Servlet Integration**
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
@ServletComponentScan
public class SpringBootServletApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringBootServletApplication.class, args);
    }
}
```

---

### **5. Advanced Servlet Examples**

#### **5.1 Handling POST Request**
```java
@WebServlet(urlPatterns = "/post-example")
public class PostServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String name = req.getParameter("name");
        resp.getWriter().write("Hello, " + name + "!");
    }
}
```

##### **Explanation**:
- This servlet handles POST requests and retrieves a parameter (`name`) from the request body.

---

#### **5.2 Servlet with Initialization Parameters**
```java
@WebServlet(urlPatterns = "/config-example", initParams = {
        @WebInitParam(name = "defaultName", value = "Guest")
})
public class ConfigServlet extends HttpServlet {

    private String defaultName;

    @Override
    public void init() throws ServletException {
        defaultName = getServletConfig().getInitParameter("defaultName");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("Hello, " + defaultName + "!");
    }
}
```

##### **Explanation**:
- `@WebInitParam`: Defines an initialization parameter (`defaultName`).
- `init`: Reads the parameter during servlet initialization.

---

#### **5.3 Using `HttpSession` in a Servlet**
```java
@WebServlet(urlPatterns = "/session-example")
public class SessionServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        Integer count = (Integer) session.getAttribute("count");
        if (count == null) {
            count = 0;
        }
        count++;
        session.setAttribute("count", count);

        resp.getWriter().write("Session count: " + count);
    }
}
```

##### **Explanation**:
- This servlet uses `HttpSession` to store a counter that increments with every request.

---

#### **5.4 Servlet Filter**
A **Filter** is used to intercept requests and responses, often for logging, authentication, or modifying headers.

```java
import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.FilterConfig;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebFilter(urlPatterns = "/*")
public class LoggingFilter implements Filter {

    @Override
    public void doFilter(jakarta.servlet.ServletRequest request, jakarta.servlet.ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        HttpServletRequest httpRequest = (HttpServletRequest) request;
        System.out.println("Request URL: " + httpRequest.getRequestURL());
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {}

    @Override
    public void destroy() {}
}
```

##### **Explanation**:
- `@WebFilter`: Registers a filter for all URLs (`/*`).
- `doFilter`: Logs the request URL before passing it to the next filter or servlet.

---

#### **5.5 Servlet Listener**
A **Listener** listens for events in the servlet lifecycle.

```java
import jakarta.servlet.ServletContextEvent;
import jakarta.servlet.ServletContextListener;
import jakarta.servlet.annotation.WebListener;

@WebListener
public class AppListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("Application started!");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("Application stopped!");
    }
}
```

##### **Explanation**:
- `@WebListener`: Registers a listener.
- `contextInitialized`: Called when the application starts.
- `contextDestroyed`: Called when the application stops.

---

### **6. Testing Servlets with Spring Boot**

Spring Boot provides `TestRestTemplate` for testing servlets.

#### **Code: Testing Hello Servlet**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.ResponseEntity;
import org.springframework.web.client.RestTemplate;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloServletTest {

    @LocalServerPort
    private int port;

    @Autowired
    private RestTemplate restTemplate;

    @Test
    void testHelloServlet() {
        String url = "http://localhost:" + port + "/hello";
        ResponseEntity<String> response = restTemplate.getForEntity(url, String.class);
        assertEquals("Hello from Servlet!", response.getBody().trim());
    }
}
```

---

### **7. Summary**

| **Feature**                  | **Description**                                                                 |
|-------------------------------|---------------------------------------------------------------------------------|
| **Basic Servlet**             | Handles HTTP requests using `doGet`, `doPost`, etc.                            |
| **Spring Boot Integration**   | Use `@ServletComponentScan` to register servlets automatically.                |
| **Filters**                   | Intercepts requests and responses for logging, authentication, etc.            |
| **Listeners**                 | Listens for application lifecycle events.                                      |
| **Testing**                   | Use `TestRestTemplate` and `@SpringBootTest` to test servlets.                 |

Servlets are a fundamental part of Java web applications, and they integrate seamlessly with Spring Boot for modern web development.
