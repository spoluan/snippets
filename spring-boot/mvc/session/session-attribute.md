### **Sessions in Spring Framework**

A **session** is a mechanism to store user-specific information on the server side during a user's interaction with a web application. Sessions are commonly used to maintain state between multiple requests from a client, such as user authentication or storing temporary data.

Spring simplifies session management by providing annotations and tools like `@SessionAttribute`, `HttpSession`, and `@SessionAttributes`.

---

### **1. Session Management in Spring**

#### **1.1. Using `HttpSession`**
The `HttpSession` object is provided by the Servlet API and can be used to store and retrieve session attributes.

- **Set Session Attributes:**
  ```java
  HttpSession session = request.getSession();
  session.setAttribute("key", value);
  ```

- **Get Session Attributes:**
  ```java
  Object value = session.getAttribute("key");
  ```

#### **1.2. Using `@SessionAttribute`**
The `@SessionAttribute` annotation is used to retrieve session attributes directly in a controller method. This is a convenient way to access session data without explicitly using `HttpSession`.

#### **1.3. Using `@SessionAttributes`**
The `@SessionAttributes` annotation is used to store model attributes in the session. It is typically used with model-based data that needs to persist across multiple requests.

---

### **2. Example Scenarios**

#### **2.1. Using `HttpSession`**

**AuthController Example:**

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@RestController
public class AuthController {

    @PostMapping(path = "/auth/login")
    public ResponseEntity<String> login(@RequestParam String username,
                                        @RequestParam String password,
                                        HttpServletRequest request,
                                        HttpServletResponse response) {
        if ("eko".equals(username) && "rahasia".equals(password)) {
            // Create session and set attributes
            HttpSession session = request.getSession(true);
            session.setAttribute("user", new User(username));

            // Optional: Add a cookie
            response.addCookie(new javax.servlet.http.Cookie("username", username));

            return new ResponseEntity<>("Login successful", HttpStatus.OK);
        } else {
            return new ResponseEntity<>("Invalid credentials", HttpStatus.UNAUTHORIZED);
        }
    }
}
```

---

#### **2.2. Using `@SessionAttribute`**

**UserController Example:**

```java
import org.springframework.web.bind.annotation.*;

@RestController
public class UserController {

    @GetMapping("/user/current")
    public String getUser(@SessionAttribute(name = "user") User user) {
        return "Hello " + user.getUsername();
    }
}
```

In this example:
- The `@SessionAttribute` annotation retrieves the `User` object stored in the session.
- If the session attribute `user` is not found, Spring throws an exception.

---

#### **2.3. Using `@SessionAttributes`**

**Controller with `@SessionAttributes`:**

```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
@SessionAttributes("user")
public class SessionDemoController {

    @GetMapping("/start")
    public String startSession(Model model) {
        User user = new User("eko");
        model.addAttribute("user", user); // Add to session
        return "start";
    }

    @GetMapping("/continue")
    public String continueSession(@ModelAttribute("user") User user) {
        // Access session attribute
        System.out.println("User in session: " + user.getUsername());
        return "continue";
    }
}
```

In this example:
- The `@SessionAttributes("user")` annotation ensures that the `user` object is stored in the session.
- The `@ModelAttribute` annotation is used to retrieve the session attribute in subsequent requests.

---

#### **2.4. Testing Session Attributes**

**Test UserController Example:**

```java
import org.hamcrest.Matchers;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void getUser() throws Exception {
        mockMvc.perform(get("/user/current")
                .sessionAttr("user", new User("eko")))
                .andExpectAll(
                        status().isOk(),
                        content().string(Matchers.containsString("Hello eko"))
                );
    }
}
```

In this test:
- The `sessionAttr` method is used to simulate a session attribute for testing.
- The test verifies the response content and status.

---

### **3. Common Use Cases**

#### **3.1. Storing Authentication Data**
Store user details (e.g., username, roles) in the session after login to maintain the user's state across requests.

#### **3.2. Shopping Cart**
Store a user's shopping cart items in the session so that the cart persists across multiple requests.

#### **3.3. Multi-Step Form**
Store form data in the session during a multi-step form submission process.

---

### **4. Session Timeout**

By default, sessions have a timeout period configured in the application server. You can customize the session timeout in Spring Boot by modifying the `application.properties` file:

```properties
server.servlet.session.timeout=30m
```

This sets the session timeout to 30 minutes.

---

### **5. Clearing Session Data**

You can clear session attributes programmatically or by invalidating the session.

- **Clear Specific Attribute:**
  ```java
  session.removeAttribute("user");
  ```

- **Invalidate Session:**
  ```java
  session.invalidate();
  ```

---

### **6. Best Practices for Session Management**

1. **Minimize Session Data:**
   - Store only essential data in the session to reduce memory usage.

2. **Secure Sensitive Data:**
   - Avoid storing sensitive information (e.g., passwords) in the session.
   - Use HTTPS to secure session cookies.

3. **Session Timeout:**
   - Set an appropriate session timeout to improve security and resource utilization.

4. **Stateless Applications:**
   - For REST APIs, prefer stateless authentication mechanisms like JWT instead of sessions.

5. **Testing:**
   - Use tools like `MockMvc` to test session-based logic.

