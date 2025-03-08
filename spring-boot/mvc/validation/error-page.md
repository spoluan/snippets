### **Error Page in Spring Boot**

Spring Boot provides a default mechanism for handling errors and exceptions that occur in your application. When an error happens, Spring Boot automatically redirects the user to a default error page or a custom error page if configured. This feature ensures that users receive a meaningful response instead of an unhandled exception or stack trace.

---

### **1. Default Error Page Behavior**

- When an unhandled exception occurs, Spring Boot redirects the request to the default error path (`/error`).
- If no custom error page or handler is defined, Spring Boot displays the **Whitelabel Error Page**, which contains basic information about the error (e.g., status code and type).
- The default behavior is controlled by the **`ErrorController`** interface, which Spring Boot implements internally.

---

### **2. Customizing the Error Page**

You can customize the error page by:

1. **Disabling the Whitelabel Error Page**:
   - Add the following property in `application.properties` to disable the default error page:
     ```properties
     server.error.whitelabel.enabled=false
     ```

2. **Creating a Custom Error Page**:
   - You can create an HTML page for specific HTTP status codes (e.g., `404.html` for "Not Found").
   - Place these files in the `src/main/resources/templates/error/` directory (if using Thymeleaf) or `src/main/resources/static/error/`.

3. **Implementing a Custom Error Controller**:
   - Define a controller that implements the `ErrorController` interface to override the default behavior.

---

### **3. Error Page Properties**

Spring Boot allows you to configure error handling using the `server.error` properties in `application.properties` or `application.yml`. Some key properties are:

| **Property**                         | **Description**                                                                 |
|--------------------------------------|---------------------------------------------------------------------------------|
| `server.error.path`                  | Path to the error controller (default: `/error`).                              |
| `server.error.whitelabel.enabled`    | Enables or disables the Whitelabel Error Page (default: `true`).               |
| `server.error.include-message`       | Whether to include the error message in the response (`always`, `never`).      |
| `server.error.include-stacktrace`    | Whether to include the stack trace in the response (`always`, `never`).        |
| `server.error.include-exception`     | Whether to include the exception message in the response (`true`, `false`).    |
| `server.error.include-binding-errors`| When to include binding errors (`always`, `never`).                            |

---

### **4. Creating a Custom Error Page**

#### **Example 1: Custom HTML Error Pages**
To create custom error pages for specific HTTP status codes:

1. **Add HTML Files to the `src/main/resources/templates/error` Directory**:
   - `404.html` (for "Not Found" errors):
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Error 404</title>
     </head>
     <body>
         <h1>Page Not Found</h1>
         <p>The page you are looking for does not exist.</p>
     </body>
     </html>
     ```

   - `500.html` (for "Internal Server Error"):
     ```html
     <!DOCTYPE html>
     <html>
     <head>
         <title>Error 500</title>
     </head>
     <body>
         <h1>Internal Server Error</h1>
         <p>Something went wrong on our end. Please try again later.</p>
     </body>
     </html>
     ```

2. **Access the Error Pages**:
   - Trigger a `404` error by navigating to a non-existent route.
   - Trigger a `500` error by causing an exception in the application.

---

#### **Example 2: Custom Error Controller**

If you need more control over the error response, you can create a custom error controller by implementing the `ErrorController` interface.

1. **Disable the Whitelabel Error Page**:
   ```properties
   server.error.whitelabel.enabled=false
   ```

2. **Create a Custom Error Controller**:
   ```java
   import org.springframework.boot.web.servlet.error.ErrorController;
   import org.springframework.http.HttpStatus;
   import org.springframework.http.ResponseEntity;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;

   import javax.servlet.RequestDispatcher;
   import javax.servlet.http.HttpServletRequest;

   @Controller
   public class CustomErrorController implements ErrorController {

       @RequestMapping("/error")
       public ResponseEntity<String> handleError(HttpServletRequest request) {
           Object status = request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE);
           Object message = request.getAttribute(RequestDispatcher.ERROR_MESSAGE);

           int statusCode = status != null ? Integer.parseInt(status.toString()) : 500;
           String errorMessage = message != null ? message.toString() : "Unknown error";

           String response = "<html><body>";
           response += "<h1>Error " + statusCode + "</h1>";
           response += "<p>" + errorMessage + "</p>";
           response += "</body></html>";

           return ResponseEntity.status(statusCode).body(response);
       }
   }
   ```

3. **Trigger the Error Page**:
   - Access an invalid route or cause an exception to see the custom error page.

---

#### **Example 3: JSON Error Responses**

If you want to return JSON responses for errors instead of HTML:

1. **Use `@RestController`**:
   ```java
   import org.springframework.boot.web.servlet.error.ErrorController;
   import org.springframework.http.HttpStatus;
   import org.springframework.http.ResponseEntity;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RestController;

   import javax.servlet.RequestDispatcher;
   import javax.servlet.http.HttpServletRequest;
   import java.util.HashMap;
   import java.util.Map;

   @RestController
   public class JsonErrorController implements ErrorController {

       @RequestMapping("/error")
       public ResponseEntity<Map<String, Object>> handleError(HttpServletRequest request) {
           Object status = request.getAttribute(RequestDispatcher.ERROR_STATUS_CODE);
           Object message = request.getAttribute(RequestDispatcher.ERROR_MESSAGE);

           int statusCode = status != null ? Integer.parseInt(status.toString()) : 500;
           String errorMessage = message != null ? message.toString() : "Unknown error";

           Map<String, Object> response = new HashMap<>();
           response.put("status", statusCode);
           response.put("message", errorMessage);

           return ResponseEntity.status(statusCode).body(response);
       }
   }
   ```

2. **Trigger the Error Page**:
   - Access an invalid route or cause an exception to see the JSON error response.

---

### **5. Advanced Configuration**

#### **Customizing Error Attributes**
You can customize the error attributes returned by Spring Boot by overriding the `DefaultErrorAttributes` class.

```java
import org.springframework.boot.web.error.DefaultErrorAttributes;
import org.springframework.stereotype.Component;
import org.springframework.web.context.request.WebRequest;

import java.util.Map;

@Component
public class CustomErrorAttributes extends DefaultErrorAttributes {

    @Override
    public Map<String, Object> getErrorAttributes(WebRequest webRequest, boolean includeStackTrace) {
        Map<String, Object> errorAttributes = super.getErrorAttributes(webRequest, includeStackTrace);
        errorAttributes.put("customMessage", "This is a custom error message");
        return errorAttributes;
    }
}
```

---

### **6. Summary of Error Handling Options**

| **Approach**                     | **Description**                                                                                   |
|----------------------------------|---------------------------------------------------------------------------------------------------|
| **Whitelabel Error Page**        | Default error page provided by Spring Boot.                                                      |
| **Custom HTML Error Pages**      | Create specific error pages for HTTP status codes (e.g., `404.html`, `500.html`).                |
| **Custom Error Controller**      | Implement `ErrorController` to define custom logic for handling errors.                         |
| **JSON Error Responses**         | Use `@RestController` to return JSON responses for errors.                                       |
| **Error Attributes Customization** | Override `DefaultErrorAttributes` to modify the attributes included in the error response.       |

---

### **7. Best Practices**

- Always disable the Whitelabel Error Page in production (`server.error.whitelabel.enabled=false`).
- Use custom error pages for user-friendly error handling.
- Avoid exposing stack traces or sensitive information in error responses.
- Use JSON responses for APIs to provide structured error information.
- Centralize error handling using `@ControllerAdvice` for consistent behavior across the application.
