### **Static Resources in Spring MVC**

---

### **1. What are Static Resources?**
Static resources are files like HTML, CSS, JavaScript, images, videos, or other content that do not require server-side processing and are served directly to the client. These resources are essential for building websites and web applications.

In Spring MVC:
- Static resources can be placed in specific directories (e.g., `/resources/static/`).
- Spring automatically handles serving these files without requiring custom controllers or servlets.

---

### **2. How Does Spring Handle Static Resources?**
1. **Request Flow**:
   - When a request is made to a path, Spring first checks if there's a controller mapping for that path.
   - If no controller is found, Spring checks if the requested resource exists in the static resources directory.
   - If the resource exists, it is served directly. Otherwise, a `404 Not Found` error is returned.

2. **Default Static Resource Locations**:
   Spring Boot automatically serves static content from the following locations:
   - `src/main/resources/static/`
   - `src/main/resources/public/`
   - `src/main/resources/resources/`
   - `src/main/resources/META-INF/resources/`

---

### **3. Configuring Static Resources**

#### **3.1 Default Configuration**
By default, Spring Boot serves static resources from the `static` folder without requiring additional configuration.

#### **3.2 Custom Configuration**
If you want to customize the behavior of static resource handling, you can configure it in the `WebMvcConfigurer`.

Example:
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // Custom static resource mapping
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
    }
}
```

---

### **4. Example: Serving Static Resources**

#### **4.1 Creating a Static Resource**
Place your static files in the `src/main/resources/static/` directory.

Example:
File: `/src/main/resources/static/index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hello Static</title>
</head>
<body>
    <h1>Hello Static</h1>
</body>
</html>
```

#### **4.2 Accessing Static Resources**
- Start the Spring Boot application.
- Access the resource via the browser or API client:
  ```
  http://localhost:8080/index.html
  ```

---

### **5. Unit Testing Static Resources**

You can use `MockMvc` to test if static resources are being served correctly.

Example:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.Matchers.containsString;

@SpringBootTest
@AutoConfigureMockMvc
public class StaticResourceTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void getStaticResource() throws Exception {
        mockMvc.perform(get("/index.html"))
                .andExpectAll(
                        status().isOk(),
                        content().string(containsString("Hello Static"))
                );
    }
}
```

---

### **6. Advanced Use Cases**

#### **6.1 Serving Files from Custom Locations**
You can serve static resources from a custom directory outside the `resources` folder.

Example:
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CustomStaticResourceConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/files/**")
                .addResourceLocations("file:/var/www/static/");
    }
}
```
In this example:
- Static files are served from `/var/www/static/`.
- Accessible via: `http://localhost:8080/files/filename.ext`.

---

#### **6.2 Cache Control for Static Resources**
You can configure cache control for static resources to improve performance.

Example:
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;
import org.springframework.http.CacheControl;

import java.util.concurrent.TimeUnit;

@Configuration
public class CacheControlConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/")
                .setCacheControl(CacheControl.maxAge(30, TimeUnit.DAYS));
    }
}
```
This configuration sets a cache duration of 30 days for resources in the `/static/` directory.

---

#### **6.3 Serving Static Resources from JAR Files**
Static resources can also be served from `META-INF/resources/` inside a JAR file.

Example:
- Place static files in `src/main/resources/META-INF/resources/`.
- These files will be accessible in the same way as files in the `static/` directory.

---

#### **6.4 Combining Static Resources with Controllers**
If you have a controller that maps to the same path as a static resource, the controller takes precedence.

Example:
```java
@Controller
public class HomeController {

    @GetMapping("/index.html")
    public String home() {
        return "This is served by the controller.";
    }
}
```
In this case:
- Accessing `/index.html` serves the response from the controller, not the static file.

---

### **7. Common Issues and Solutions**

#### **7.1 Static Resource Not Found**
- **Cause**: The file is not in the correct directory.
- **Solution**: Ensure the file is placed in one of the default directories (`static`, `public`, etc.) or configure a custom resource handler.

#### **7.2 Overriding Default Behavior**
If you want to disable Spring's default static resource handling, you can exclude the `WebMvcAutoConfiguration` class:
```java
@SpringBootApplication(exclude = {WebMvcAutoConfiguration.class})
public class MyApplication {
}
```

---

### **8. Summary**

| **Feature**                  | **Description**                                                                 |
|------------------------------|---------------------------------------------------------------------------------|
| **Default Locations**         | `static`, `public`, `resources`, `META-INF/resources`.                         |
| **Custom Locations**          | Configure using `WebMvcConfigurer`.                                            |
| **Cache Control**             | Improve performance by setting cache headers for static resources.             |
| **Testing**                   | Use `MockMvc` to test static resource endpoints.                               |
| **Priority**                  | Controller mappings take precedence over static resources.                     |

---

### **9. Static Resource Examples**

#### **Example 1: Serving CSS and JavaScript**
Place `style.css` and `script.js` in `src/main/resources/static/`.

**style.css**:
```css
body {
    background-color: lightblue;
}
```

**script.js**:
```javascript
console.log("Hello from script.js");
```

Access:
- `http://localhost:8080/style.css`
- `http://localhost:8080/script.js`

---

#### **Example 2: Serving Images**
Place `logo.png` in `src/main/resources/static/images/`.

Access:
```
http://localhost:8080/images/logo.png
```

---

#### **Example 3: Custom Directory**
Serve files from `/custom-static/` directory on the file system:
```java
registry.addResourceHandler("/custom/**")
        .addResourceLocations("file:/path/to/custom-static/");
```

Access:
```
http://localhost:8080/custom/filename.ext
```
