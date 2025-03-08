### **Views in Spring MVC**

---

### **1. What is a View in Spring MVC?**

A **View** in Spring MVC is a mechanism for rendering the data provided by the controller into a user interface. Views are part of the **Model-View-Controller (MVC)** architecture, where:
- **Model** contains the data.
- **Controller** handles user requests and prepares the data.
- **View** is responsible for displaying the data to the user.

Spring MVC does not generate views manually; instead, it integrates with various templating engines to render views.

---

### **2. Supported View Technologies**

Spring MVC supports the following templating technologies for rendering views:
1. **JSP (Java Server Pages)**  
   - A traditional Java-based templating engine.
   - Requires a servlet container like Tomcat.

2. **Apache Velocity**  
   - A lightweight and fast templating engine.  
   - Website: [Velocity](https://velocity.apache.org/)

3. **Apache FreeMarker**  
   - A powerful and flexible templating engine.  
   - Website: [FreeMarker](https://freemarker.apache.org/)

4. **Mustache**  
   - A logic-less templating engine integrated with Spring Boot.  
   - Website: [Mustache](https://mustache.github.io/)

5. **Thymeleaf**  
   - A modern, feature-rich templating engine designed for Spring applications.  
   - Website: [Thymeleaf](https://www.thymeleaf.org/)

---

### **3. Using Mustache as a View Engine**

#### **3.1 Adding Mustache Dependency**
To use Mustache in a Spring Boot application, add the dependency in `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mustache</artifactId>
</dependency>
```

#### **3.2 Mustache Configuration**
- Templates are stored in the `/resources/templates/` directory.
- File extension: `.mustache`.
- No manual configuration is required; Spring Boot automatically sets up Mustache.

#### **3.3 Example: Mustache Template**
File: `/resources/templates/hello.mustache`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{title}}</title>
</head>
<body>
    <h1>Hello {{name}}</h1>
</body>
</html>
```

#### **3.4 Example: Mustache Controller**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.servlet.ModelAndView;

import java.util.Map;

@Controller
public class HelloController {

    @GetMapping("/web/hello")
    public ModelAndView hello(@RequestParam(name = "name", required = false) String name) {
        return new ModelAndView("hello", Map.of(
                "title", "Learning Mustache",
                "name", name
        ));
    }
}
```

#### **3.5 Testing Mustache View**
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
public class HelloControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void helloView() throws Exception {
        mockMvc.perform(get("/web/hello").queryParam("name", "John"))
                .andExpectAll(
                        status().isOk(),
                        content().string(containsString("Learning Mustache")),
                        content().string(containsString("Hello John"))
                );
    }
}
```

---

### **4. Using ModelAndView**

The `ModelAndView` object is used to pass both the model (data) and the view (template) to the client.

#### **4.1 Example: Using ModelAndView**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ExampleController {

    @GetMapping("/example")
    public ModelAndView example() {
        ModelAndView modelAndView = new ModelAndView("example");
        modelAndView.addObject("message", "Welcome to Spring MVC!");
        return modelAndView;
    }
}
```

#### **4.2 Example: Template**
File: `/resources/templates/example.mustache`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Example</title>
</head>
<body>
    <h1>{{message}}</h1>
</body>
</html>
```

---

### **5. Thymeleaf Integration**

Thymeleaf is one of the most popular templating engines for Spring Boot applications.

#### **5.1 Adding Thymeleaf Dependency**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

#### **5.2 Thymeleaf Template Example**
File: `/resources/templates/greeting.html`
```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Thymeleaf Example</title>
</head>
<body>
    <h1 th:text="'Hello, ' + ${name} + '!'"></h1>
</body>
</html>
```

#### **5.3 Thymeleaf Controller Example**
```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class GreetingController {

    @GetMapping("/greeting")
    public String greeting(@RequestParam(name = "name", required = false, defaultValue = "World") String name, Model model) {
        model.addAttribute("name", name);
        return "greeting";
    }
}
```

#### **5.4 Accessing Thymeleaf View**
URL: `http://localhost:8080/greeting?name=John`  
Output: `Hello, John!`

---

### **6. JSP Integration**

JSP is a traditional templating technology that requires a servlet container like Tomcat.

#### **6.1 Adding JSP Dependency**
```xml
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <scope>provided</scope>
</dependency>
```

#### **6.2 JSP Example**
File: `/src/main/webapp/WEB-INF/jsp/welcome.jsp`
```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>Welcome</title>
</head>
<body>
    <h1>Welcome, ${name}!</h1>
</body>
</html>
```

#### **6.3 JSP Controller Example**
```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.ui.Model;

@Controller
public class WelcomeController {

    @GetMapping("/welcome")
    public String welcome(@RequestParam(name = "name", required = false, defaultValue = "Guest") String name, Model model) {
        model.addAttribute("name", name);
        return "welcome";
    }
}
```

---

### **7. Customizing View Resolvers**

You can customize how Spring resolves views by defining a custom `ViewResolver`.

#### **Example: Custom View Resolver**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration
public class ViewResolverConfig {

    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/jsp/");
        resolver.setSuffix(".jsp");
        return resolver;
    }
}
```

