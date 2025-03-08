### **Spring MVC Configuration and Examples**

Spring MVC provides a flexible and extensible way to configure web applications by overriding the default behavior using the `WebMvcConfigurer` interface. Below is a detailed explanation of MVC configuration, interceptors, and examples for different use cases.

---

### **1. MVC Configuration Overview**

#### **1.1 What is Spring MVC Configuration?**
Spring MVC configuration allows developers to customize the behavior of the Spring Web MVC framework. It includes features like:
- Adding interceptors.
- Configuring view resolvers.
- Handling static resources.
- Configuring CORS (Cross-Origin Resource Sharing).
- Adding custom argument resolvers, message converters, etc.

#### **1.2 How to Configure Spring MVC**
1. Create a class annotated with `@Configuration`.
2. Implement the `WebMvcConfigurer` interface.
3. Override methods provided by `WebMvcConfigurer` to customize behavior.

---

### **2. Basic MVC Configuration Example**

#### **2.1 Example of `WebMvcConfigurer` Implementation**

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {
    // Override methods here to customize Spring MVC
}
```

This class can now be used to customize Spring MVC by overriding specific methods.

---

### **3. Common Customizations with `WebMvcConfigurer`**

#### **3.1 Adding Interceptors**
Interceptors allow you to intercept HTTP requests and responses for pre-processing or post-processing.

**Interceptor Implementation:**
```java
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class SessionInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession(true);
        Object user = session.getAttribute("user");
        if (user == null) {
            response.sendRedirect("/login");
            return false;
        }
        return true;
    }
}
```

**Registering the Interceptor:**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Autowired
    private SessionInterceptor sessionInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(sessionInterceptor).addPathPatterns("/user/*");
    }
}
```

---

#### **3.2 Configuring Static Resources**
Static resources like CSS, JavaScript, and images can be served using the following configuration:

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**")
                .addResourceLocations("classpath:/static/");
    }
}
```

---

#### **3.3 Configuring View Resolvers**
View resolvers map view names to actual templates (e.g., JSP, Thymeleaf).

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ViewResolverRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }
}
```

---

#### **3.4 Enabling CORS**
CORS (Cross-Origin Resource Sharing) configuration allows you to define which domains can access your APIs.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("http://example.com")
                .allowedMethods("GET", "POST");
    }
}
```

---

#### **3.5 Adding Formatters and Converters**
Formatters and converters are used to convert data types (e.g., String to Date).

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.format.FormatterRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new DateFormatter("yyyy-MM-dd"));
    }
}
```

---

#### **3.6 Adding Custom Argument Resolvers**
Custom argument resolvers allow you to define how method parameters in controllers are resolved.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new MyCustomArgumentResolver());
    }
}
```

---

#### **3.7 Adding Custom Exception Handlers**
You can configure global exception handling using `@ControllerAdvice` or by overriding methods in `WebMvcConfigurer`.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    // Add custom exception resolvers here
}
```

---

#### **3.8 Configuring Message Converters**
Message converters handle serialization and deserialization of request/response bodies (e.g., JSON, XML).

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(new MyCustomMessageConverter());
    }
}
```

---

### **4. Ant Path Matcher**
Spring uses **Ant Path Matcher** to define URL patterns. It supports:
- `?` matches one character.
- `*` matches zero or more characters.
- `**` matches zero or more directories.

**Examples:**
- `com/t?st.jsp` matches `com/test.jsp`, `com/tast.jsp`.
- `com/*.jsp` matches all `.jsp` files in the `com` directory.
- `com/**/test.jsp` matches all `test.jsp` files in nested directories under `com`.

---

### **5. Full MVC Configuration Example**

Here’s a full example combining multiple configurations:

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.format.FormatterRegistry;
import org.springframework.http.converter.HttpMessageConverter;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.*;

import java.util.List;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new SessionInterceptor()).addPathPatterns("/user/**");
    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
    }

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        registry.jsp("/WEB-INF/views/", ".jsp");
    }

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**").allowedOrigins("http://example.com");
    }

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new DateFormatter("yyyy-MM-dd"));
    }

    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        converters.add(new MyCustomMessageConverter());
    }

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new MyCustomArgumentResolver());
    }
}
```

---

### **6. Testing MVC Configurations**

**Example Test for Interceptors:**
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
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void getUserInvalid() throws Exception {
        mockMvc.perform(get("/user/current"))
                .andExpect(status().is3xxRedirection());
    }
}
```
