### **Understanding Form Requests in Spring**

A **Form Request** in Spring is a way to handle data submitted via HTML forms or POST requests with `application/x-www-form-urlencoded` content type. It allows you to extract form data or query parameters from the request using the `@RequestParam` annotation.

---

### **1. What is a Form Request?**

- **Definition**: A Form Request is a mechanism to send data from the client (via forms or query parameters) to the server.
- **Content Type**: The typical content type for form requests is `application/x-www-form-urlencoded`.
- **Use Case**: Commonly used in web applications to submit user data such as login forms, registration forms, etc.

---

### **2. Key Features of Form Requests**

1. **Annotation**: Use `@RequestParam` to bind form fields or query parameters to method arguments.
2. **Automatic Type Conversion**: Spring automatically converts form data to the required type (e.g., `String`, `int`, `Date`).
3. **Default Values**: You can provide default values for optional parameters.
4. **Validation**: You can validate form data using annotations such as `@Valid` or `@NotNull`.
5. **Multiple Parameters**: Handles multiple form fields or query parameters in a single request.

---

### **3. How to Handle Form Requests in Spring**

#### **Basic Syntax**
```java
@PostMapping("/submit")
public String handleForm(@RequestParam("fieldName") String fieldValue) {
    return "Received: " + fieldValue;
}
```

---

### **4. Examples of Form Requests**

#### **4.1. Handling a Single Form Field**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class SingleFormFieldController {

    @PostMapping("/submit")
    public String handleForm(@RequestParam("name") String name) {
        return "Received Name: " + name;
    }
}
```

**Request**:
```
POST /submit
Content-Type: application/x-www-form-urlencoded

name=Sevendi
```

**Response**:
```
Received Name: Sevendi
```

---

#### **4.2. Handling Multiple Form Fields**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class MultipleFormFieldsController {

    @PostMapping("/register")
    public String registerUser(
        @RequestParam("username") String username,
        @RequestParam("password") String password
    ) {
        return "Registered User: " + username;
    }
}
```

**Request**:
```
POST /register
Content-Type: application/x-www-form-urlencoded

username=Sevendi&password=12345
```

**Response**:
```
Registered User: Sevendi
```

---

#### **4.3. Using Default Values for Optional Parameters**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class DefaultValueController {

    @PostMapping("/greet")
    public String greetUser(
        @RequestParam(name = "name", defaultValue = "Guest") String name
    ) {
        return "Hello, " + name;
    }
}
```

**Request Without Parameter**:
```
POST /greet
Content-Type: application/x-www-form-urlencoded
```

**Response**:
```
Hello, Guest
```

**Request With Parameter**:
```
POST /greet
Content-Type: application/x-www-form-urlencoded

name=Alice
```

**Response**:
```
Hello, Alice
```

---

#### **4.4. Handling Date Conversion**

Spring can automatically convert form data to a `Date` object if the format matches.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;
import java.text.SimpleDateFormat;
import java.util.Date;

@RestController
public class DateConversionController {

    private final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");

    @PostMapping("/birthdate")
    public String handleBirthDate(@RequestParam("birthDate") Date birthDate) {
        return "Birth Date: " + dateFormat.format(birthDate);
    }
}
```

**Request**:
```
POST /birthdate
Content-Type: application/x-www-form-urlencoded

birthDate=1990-10-10
```

**Response**:
```
Birth Date: 1990-10-10
```

---

#### **4.5. Validating Form Data**

You can use validation annotations to ensure form data meets specific criteria.

**Controller Code**:
```java
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;
import javax.validation.constraints.NotNull;

@RestController
@Validated
public class ValidationController {

    @PostMapping("/validate")
    public String validateForm(
        @RequestParam("name") @NotNull String name
    ) {
        return "Validated Name: " + name;
    }
}
```

**Request Without Parameter**:
```
POST /validate
Content-Type: application/x-www-form-urlencoded
```

**Response**:
```
400 Bad Request
```

**Request With Parameter**:
```
POST /validate
Content-Type: application/x-www-form-urlencoded

name=Sevendi
```

**Response**:
```
Validated Name: Sevendi
```

---

#### **4.6. Handling Query Parameters**

Form requests can also handle query parameters sent in a GET request.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class QueryParameterController {

    @GetMapping("/search")
    public String search(@RequestParam("query") String query) {
        return "Search Query: " + query;
    }
}
```

**Request**:
```
GET /search?query=Spring
```

**Response**:
```
Search Query: Spring
```

---

#### **4.7. Testing Form Requests**

You can use `MockMvc` to test form requests in Spring.

**Unit Test Code**:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class FormRequestTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testFormRequest() throws Exception {
        mockMvc.perform(
                post("/submit")
                        .contentType("application/x-www-form-urlencoded")
                        .param("name", "Sevendi")
        ).andExpectAll(
                status().isOk(),
                content().string("Received Name: Sevendi")
        );
    }
}
```

