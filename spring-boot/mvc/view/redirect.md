### **Redirect in Spring MVC**

---

### **1. What is a Redirect?**

A **redirect** in Spring MVC is a mechanism to instruct the browser to make a new HTTP request to a different URL. It is commonly used to:
- Redirect users to another page after processing a request.
- Avoid duplicate form submissions (Post/Redirect/Get pattern).
- Navigate to a different endpoint.

---

### **2. How Does Redirect Work in Spring MVC?**

In Spring MVC, a redirect can be achieved in the following ways:
1. **Using `HttpServletResponse`**: Manually set the redirect URL.
2. **Using `ModelAndView`**: Return a `ModelAndView` object with the view name prefixed with `redirect:`.
3. **Using `String` as the return type**: Return a string with the view name prefixed with `redirect:`.

---

### **3. Redirect with `ModelAndView`**

#### **3.1 Example: Redirect When a Parameter is Missing**

In this example, if the `name` parameter is missing, the user is redirected to a URL with the default value for `name`.

##### **Controller Code**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.servlet.ModelAndView;

import java.util.Objects;
import java.util.Map;

@Controller
public class HelloController {

    @GetMapping(path = "/web/hello")
    public ModelAndView hello(@RequestParam(name = "name", required = false) String name) {
        // Redirect if the name parameter is null
        if (Objects.isNull(name)) {
            return new ModelAndView("redirect:/web/hello?name=Guest");
        }

        // Return the view with the provided name
        return new ModelAndView("hello", Map.of(
                "title", "Belajar View",
                "name", name
        ));
    }
}
```

##### **Explanation**
- If the `name` parameter is missing, the controller redirects to `/web/hello?name=Guest`.
- Otherwise, it returns the `hello.mustache` view with the provided `name`.

---

### **4. Redirect with `String` Return Type**

You can also return a `String` with the `redirect:` prefix to trigger a redirect.

#### **Example: Redirect to Home Page**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class RedirectController {

    @GetMapping("/redirect")
    public String redirectToHome() {
        return "redirect:/home";
    }
}
```

In this example:
- Accessing `/redirect` will redirect the user to `/home`.

---

### **5. Redirect with `HttpServletResponse`**

You can manually set the redirect URL using the `HttpServletResponse` object.

#### **Example: Redirect Using `HttpServletResponse`**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Controller
public class ManualRedirectController {

    @GetMapping("/manual-redirect")
    public void manualRedirect(HttpServletResponse response) throws IOException {
        response.sendRedirect("/home");
    }
}
```

In this example:
- The `HttpServletResponse`'s `sendRedirect` method is used to redirect to `/home`.

---

### **6. Testing Redirects**

You can use `MockMvc` to test redirects in your application.

#### **Example: Testing Redirect with `MockMvc`**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
public class RedirectTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void helloViewRedirect() throws Exception {
        mockMvc.perform(get("/web/hello"))
                .andExpect(status().is3xxRedirection());
    }
}
```

##### **Explanation**
- The test ensures that accessing `/web/hello` without a `name` parameter results in a **3xx redirection**.

---

### **7. Redirect with Flash Attributes**

Flash attributes are used to pass temporary data between redirects. This ensures that the data is available only for the next request.

#### **Example: Redirect with Flash Attributes**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

@Controller
public class FlashRedirectController {

    @GetMapping("/redirect-with-flash")
    public String redirectWithFlash(RedirectAttributes redirectAttributes) {
        redirectAttributes.addFlashAttribute("message", "This is a flash message!");
        return "redirect:/show-message";
    }

    @GetMapping("/show-message")
    public String showMessage() {
        return "message";
    }
}
```

##### **Explanation**
- `addFlashAttribute` adds temporary data that is available in the `/show-message` request.
- Flash attributes are automatically removed after the redirect.

---

### **8. Redirect vs Forward**

- **Redirect**: The browser makes a new request to the target URL, and the URL in the browser changes.
- **Forward**: The request is forwarded internally on the server, and the URL in the browser does not change.

#### **Example: Forward**
```java
@GetMapping("/forward-example")
public String forwardExample() {
    return "forward:/home";
}
```

In this example:
- The request is forwarded to `/home` without changing the URL in the browser.

---

### **9. Summary**

| **Redirect Method**       | **Description**                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------|
| **ModelAndView**           | Return a `ModelAndView` object with `redirect:` prefix.                                         |
| **String Return Type**     | Return a `String` with `redirect:` prefix.                                                     |
| **HttpServletResponse**    | Use `HttpServletResponse.sendRedirect()` for manual redirects.                                 |
| **Flash Attributes**       | Pass temporary data between redirects using `RedirectAttributes`.                              |
| **Forward**                | Use `forward:` prefix to forward requests internally (URL in browser remains unchanged).       |

---

### **10. Practical Examples**

#### **10.1 Redirect to Login Page**
```java
@GetMapping("/protected")
public String redirectToLogin() {
    return "redirect:/login";
}
```
- If the user is not logged in, redirect to the login page.

---

#### **10.2 Redirect After Form Submission**
```java
@PostMapping("/submit-form")
public String handleFormSubmission() {
    return "redirect:/confirmation";
}
```
- Redirect to a confirmation page after form submission.

---

#### **10.3 Redirect with Query Parameters**
```java
@GetMapping("/redirect-with-params")
public String redirectWithParams() {
    return "redirect:/search?query=spring";
}
```
- Redirect to a search page with query parameters.

