### **Argument Resolver in Spring MVC**

---

### **1. What is an Argument Resolver?**
An **Argument Resolver** in Spring MVC is a mechanism to automatically resolve and populate method parameters in a controller. When a controller method is invoked, Spring looks for the required data (e.g., request body, query parameters, headers) and populates the method arguments.

By default, Spring provides several annotations like:
- `@RequestParam`
- `@RequestBody`
- `@ModelAttribute`
- `@PathVariable`

However, if you need custom logic to resolve arguments, you can create your own **Argument Resolver**.

---

### **2. How Does Argument Resolver Work?**
1. Spring scans the method parameters of a controller.
2. If a parameter matches a specific type or annotation, Spring uses the corresponding argument resolver to populate the parameter.
3. You can create a custom resolver by implementing the `HandlerMethodArgumentResolver` interface.

---

### **3. Creating a Custom Argument Resolver**

#### **3.1 Steps to Create an Argument Resolver**
1. Create a class that implements `HandlerMethodArgumentResolver`.
2. Override the following methods:
   - `supportsParameter`: Determines whether the resolver supports the parameter.
   - `resolveArgument`: Contains the logic to resolve the parameter.
3. Register the resolver in the `WebMvcConfigurer`.

---

#### **3.2 Example of Custom Argument Resolver**

##### **3.2.1 Model Class**
```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Partner {
    private String id;
    private String name;
}
```

##### **3.2.2 Custom Argument Resolver**
```java
import org.springframework.stereotype.Component;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.core.MethodParameter;

import javax.servlet.http.HttpServletRequest;

@Component
public class PartnerArgumentResolver implements HandlerMethodArgumentResolver {

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        // Check if the parameter type is Partner
        return parameter.getParameterType().equals(Partner.class);
    }

    @Override
    public Object resolveArgument(MethodParameter parameter,
                                  ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest,
                                  org.springframework.web.context.request.WebRequest request) throws Exception {
        // Extract the header "X-API-KEY"
        HttpServletRequest servletRequest = (HttpServletRequest) webRequest.getNativeRequest();
        String apiKey = servletRequest.getHeader("X-API-KEY");

        if (apiKey != null) {
            // Return a new Partner object populated with data
            return new Partner(apiKey, "Sample Partner");
        }

        throw new RuntimeException("Unauthorized Exception");
    }
}
```

##### **3.2.3 Registering the Resolver**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@Configuration
public class MyWebConfig implements WebMvcConfigurer {

    @Autowired
    private PartnerArgumentResolver partnerArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        // Register the custom argument resolver
        resolvers.add(partnerArgumentResolver);
    }
}
```

##### **3.2.4 Controller Using the Resolver**
```java
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;

@Controller
public class PartnerController {

    @GetMapping(path = "/partner/current")
    @ResponseBody
    @ResponseStatus(HttpStatus.OK)
    public String getPartner(Partner partner) {
        // The Partner object is resolved by the custom argument resolver
        return partner.getId() + ":" + partner.getName();
    }
}
```

##### **3.2.5 Testing the Controller**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class PartnerControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void getPartner() throws Exception {
        mockMvc.perform(
                get("/partner/current")
                        .header("X-API-KEY", "SAMPLE")
        ).andExpectAll(
                status().isOk(),
                content().string("SAMPLE:Sample Partner")
        );
    }
}
```

---

### **4. Key Methods in `HandlerMethodArgumentResolver`**

| **Method**                  | **Description**                                                                 |
|-----------------------------|---------------------------------------------------------------------------------|
| `supportsParameter`         | Determines whether the resolver supports a specific parameter.                 |
| `resolveArgument`           | Provides the logic to resolve the argument and return the object.              |

---

### **5. Benefits of Custom Argument Resolver**
- **Custom Logic**: Allows injecting custom objects into controller methods based on request data (e.g., headers, cookies).
- **Reusability**: The logic can be reused across multiple controllers.
- **Cleaner Code**: Reduces boilerplate code in controllers.

---

### **6. Limitations of Argument Resolvers**
- **Limited Scope**: Argument resolvers can only populate method parameters. They cannot modify HTTP responses (use interceptors for that).
- **Dependency on Parameter Type**: The resolver is triggered only for specific parameter types.

---

### **7. Combining Interceptors and Argument Resolvers**
To overcome the limitations of argument resolvers, you can combine them with interceptors. For example:
1. Use an interceptor to modify the request or add attributes.
2. Use an argument resolver to populate the controller method parameters from the modified request.

**Example:**
- Interceptor adds a request attribute:
  ```java
  request.setAttribute("user", new User("123", "John Doe"));
  ```
- Argument resolver retrieves the attribute:
  ```java
  User user = (User) request.getAttribute("user");
  ```

---

### **8. Other Use Cases for Argument Resolvers**

#### **8.1 Resolving User Information from JWT**
You can create an argument resolver to extract user details from a JWT token in the request header.

#### **8.2 Populating Custom Objects from Query Parameters**
Use an argument resolver to create a custom object from multiple query parameters.

#### **8.3 Resolving Locale Information**
Create an argument resolver to extract locale information from request headers or cookies.
