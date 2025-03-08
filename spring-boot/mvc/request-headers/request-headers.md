### **Understanding Request Headers in Spring**

A **Request Header** in HTTP is a key-value pair sent by the client to provide additional information about the request. In Spring, request headers can be accessed and processed using the `@RequestHeader` annotation, which simplifies the process compared to traditional methods like using `HttpServletRequest`.

---

### **1. What is a Request Header?**

- **Request Headers** provide metadata about the HTTP request, such as content type, authorization tokens, user-agent, etc.
- Common examples of request headers:
  - `Content-Type`: Specifies the media type of the request body.
  - `Authorization`: Contains credentials for authentication.
  - `User-Agent`: Provides information about the client making the request.
  - `Accept`: Specifies the media types the client can process.
  - `X-Custom-Header`: Custom headers defined by the client or server.

---

### **2. Accessing Request Headers in Spring**

Spring provides a convenient way to access request headers using the `@RequestHeader` annotation. It allows you to bind specific headers to method parameters in a controller.

#### **Syntax**
```java
@GetMapping("/example")
public String example(@RequestHeader("Header-Name") String headerValue) {
    return "Header value: " + headerValue;
}
```

---

### **3. Key Features of `@RequestHeader`**

- **Required or Optional**: You can specify whether the header is mandatory or optional using the `required` attribute.
- **Default Value**: You can provide a default value if the header is not present.
- **Type Conversion**: Spring automatically converts the header value to the specified type (e.g., `int`, `boolean`).

---

### **4. Examples of Using Request Headers**

#### **4.1. Basic Example: Accessing a Single Header**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class HeaderController {

    @GetMapping("/header/token")
    public String header(@RequestHeader(name = "X-TOKEN") String token) {
        if ("SEVENDI".equals(token)) {
            return "OK";
        }
        return "KO";
    }
}
```

**Request**:
```
GET /header/token
X-TOKEN: SEVENDI
```

**Response**:
```
OK
```

---

#### **4.2. Handling Optional Headers**

You can make a header optional by setting `required = false` and provide a default value.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class OptionalHeaderController {

    @GetMapping("/header/optional")
    public String optionalHeader(
        @RequestHeader(name = "X-TOKEN", required = false, defaultValue = "DEFAULT") String token
    ) {
        return "Token: " + token;
    }
}
```

**Request Without Header**:
```
GET /header/optional
```

**Response**:
```
Token: DEFAULT
```

**Request With Header**:
```
GET /header/optional
X-TOKEN: CUSTOM
```

**Response**:
```
Token: CUSTOM
```

---

#### **4.3. Accessing Multiple Headers**

You can access multiple headers by adding multiple `@RequestHeader` annotations.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class MultiHeaderController {

    @GetMapping("/header/multiple")
    public String multipleHeaders(
        @RequestHeader("X-TOKEN") String token,
        @RequestHeader("User-Agent") String userAgent
    ) {
        return "Token: " + token + ", User-Agent: " + userAgent;
    }
}
```

**Request**:
```
GET /header/multiple
X-TOKEN: SEVENDI
User-Agent: Mozilla/5.0
```

**Response**:
```
Token: SEVENDI, User-Agent: Mozilla/5.0
```

---

#### **4.4. Using `HttpHeaders` for Dynamic Headers**

If you want to access all headers dynamically, you can use `HttpHeaders`.

**Controller Code**:
```java
import org.springframework.http.HttpHeaders;
import org.springframework.web.bind.annotation.*;

@RestController
public class DynamicHeaderController {

    @GetMapping("/header/all")
    public String allHeaders(@RequestHeader HttpHeaders headers) {
        return "Headers: " + headers.toString();
    }
}
```

**Request**:
```
GET /header/all
X-TOKEN: SEVENDI
User-Agent: Mozilla/5.0
```

**Response**:
```
Headers: [X-TOKEN:"SEVENDI", User-Agent:"Mozilla/5.0"]
```

---

#### **4.5. Type Conversion for Headers**

Spring can automatically convert header values to other types like `int`, `boolean`, etc.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class TypeConversionController {

    @GetMapping("/header/convert")
    public String headerConversion(@RequestHeader(name = "X-COUNT") int count) {
        return "Count: " + count;
    }
}
```

**Request**:
```
GET /header/convert
X-COUNT: 5
```

**Response**:
```
Count: 5
```

---

#### **4.6. Using Default Values with Type Conversion**

You can combine default values and type conversion.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class DefaultValueController {

    @GetMapping("/header/default")
    public String defaultHeader(
        @RequestHeader(name = "X-COUNT", required = false, defaultValue = "10") int count
    ) {
        return "Count: " + count;
    }
}
```

**Request Without Header**:
```
GET /header/default
```

**Response**:
```
Count: 10
```

---

#### **4.7. Custom Headers**

You can use custom headers for specific purposes, such as API tokens, client-specific data, etc.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class CustomHeaderController {

    @GetMapping("/header/custom")
    public String customHeader(@RequestHeader(name = "X-CUSTOM-HEADER") String customHeader) {
        return "Custom Header: " + customHeader;
    }
}
```

**Request**:
```
GET /header/custom
X-CUSTOM-HEADER: MyHeaderValue
```

**Response**:
```
Custom Header: MyHeaderValue
```

---

#### **4.8. Header Validation**

You can validate headers manually to enforce specific rules.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class ValidationController {

    @GetMapping("/header/validate")
    public String validateHeader(@RequestHeader(name = "X-TOKEN") String token) {
        if (!"VALID_TOKEN".equals(token)) {
            return "Invalid Token";
        }
        return "Valid Token";
    }
}
```

**Request With Invalid Token**:
```
GET /header/validate
X-TOKEN: INVALID
```

**Response**:
```
Invalid Token
```

---

### **5. MockMVC Unit Test Example**

You can write unit tests for request headers using `MockMvc`.

**Unit Test Code**:
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
public class HeaderTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testHeader() throws Exception {
        mockMvc.perform(
                get("/header/token")
                        .header("X-TOKEN", "SEVENDI")
        ).andExpectAll(
                status().isOk(),
                content().string("OK")
        );
    }
}
```

