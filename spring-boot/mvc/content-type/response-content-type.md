### **Understanding Response Content-Type in Spring**

The `Response Content-Type` determines the format of the data returned by the server in the HTTP response. In Spring, you can control this using the `produces` attribute in mapping annotations (e.g., `@RequestMapping`, `@PostMapping`, etc.). This ensures that the server sends the response in a specific format, such as `JSON`, `XML`, `HTML`, or `plain text`.

---

### **1. What is Response Content-Type?**

- The `Content-Type` header in the HTTP response specifies the **media type** of the response body sent by the server.
- Common response content-types:
  - `text/html`: HTML content
  - `application/json`: JSON content
  - `application/xml`: XML content
  - `text/plain`: Plain text
- In Spring, the `produces` attribute is used to define the expected response content type.

---

### **2. How to Set Response Content-Type in Spring**

To specify the response content type, use the `produces` attribute in mapping annotations like `@RequestMapping`, `@PostMapping`, etc.

#### **Syntax**
```java
@PostMapping(path = "/example", produces = MediaType.APPLICATION_JSON_VALUE)
```

---

### **3. Examples of Response Content-Type**

Below are examples of how to control the `Content-Type` of responses in Spring.

---

#### **3.1. Returning HTML (`text/html`)**

HTML responses are often used when building web applications.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class HtmlController {

    @PostMapping(
        path = "/form/hello",
        consumes = MediaType.APPLICATION_FORM_URLENCODED_VALUE,
        produces = MediaType.TEXT_HTML_VALUE
    )
    public String hello(@RequestParam(name = "name") String name) {
        return """
            <html>
                <body>
                    <h1>Hello %s</h1>
                </body>
            </html>
        """.formatted(name);
    }
}
```

**Unit Test**:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class HtmlControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void formHello() throws Exception {
        mockMvc.perform(
                post("/form/hello")
                        .contentType(MediaType.APPLICATION_FORM_URLENCODED)
                        .param("name", "Eko")
        ).andExpectAll(
                status().isOk(),
                header().string(HttpHeaders.CONTENT_TYPE, MediaType.TEXT_HTML_VALUE),
                content().string(org.hamcrest.Matchers.containsString("Hello Eko"))
        );
    }
}
```

**Request**:
```
POST /form/hello
Content-Type: application/x-www-form-urlencoded

name=Eko
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: text/html

<html>
    <body>
        <h1>Hello Eko</h1>
    </body>
</html>
```

---

#### **3.2. Returning JSON (`application/json`)**

JSON is the most commonly used format for APIs.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class JsonController {

    @GetMapping(path = "/json/hello", produces = MediaType.APPLICATION_JSON_VALUE)
    public User hello() {
        return new User("Eko", 30);
    }
}

class User {
    private String name;
    private int age;

    // Constructor, Getters, and Setters
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

**Request**:
```
GET /json/hello
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "Eko",
  "age": 30
}
```

---

#### **3.3. Returning XML (`application/xml`)**

XML is less common but still used in legacy systems.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

import javax.xml.bind.annotation.XmlRootElement;

@RestController
public class XmlController {

    @GetMapping(path = "/xml/hello", produces = MediaType.APPLICATION_XML_VALUE)
    public User hello() {
        return new User("Eko", 30);
    }
}

@XmlRootElement
class User {
    private String name;
    private int age;

    // Constructor, Getters, and Setters
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

**Request**:
```
GET /xml/hello
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: application/xml

<User>
    <name>Eko</name>
    <age>30</age>
</User>
```

---

#### **3.4. Returning Plain Text (`text/plain`)**

Plain text is used for simple responses.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class TextController {

    @GetMapping(path = "/text/hello", produces = MediaType.TEXT_PLAIN_VALUE)
    public String hello() {
        return "Hello Eko";
    }
}
```

**Request**:
```
GET /text/hello
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: text/plain

Hello Eko
```

---

#### **3.5. Returning Multiple Content-Types**

You can support multiple response content types by specifying an array in the `produces` attribute.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class MultiContentTypeController {

    @GetMapping(path = "/multi/hello", produces = {MediaType.APPLICATION_JSON_VALUE, MediaType.APPLICATION_XML_VALUE})
    public User hello() {
        return new User("Eko", 30);
    }
}
```

**Request with `Accept: application/json`**:
```
GET /multi/hello
Accept: application/json
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "name": "Eko",
  "age": 30
}
```

**Request with `Accept: application/xml`**:
```
GET /multi/hello
Accept: application/xml
```

**Response**:
```
HTTP/1.1 200 OK
Content-Type: application/xml

<User>
    <name>Eko</name>
    <age>30</age>
</User>
```

---

#### **3.6. Dynamic Content-Type Based on Request**

You can dynamically set the response `Content-Type` based on the `Accept` header in the request.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class DynamicController {

    @GetMapping("/dynamic/hello")
    public String hello(@RequestHeader("Accept") String accept) {
        if (accept.contains(MediaType.TEXT_HTML_VALUE)) {
            return "<html><body><h1>Hello Eko</h1></body></html>";
        } else if (accept.contains(MediaType.APPLICATION_JSON_VALUE)) {
            return "{\"message\":\"Hello Eko\"}";
        }
        return "Hello Eko";
    }
}
```

---

### **4. Key Points**

| **Aspect**                  | **Details**                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Purpose**                 | To specify the format of the HTTP response body.                           |
| **Common Response Types**   | `text/html`, `application/json`, `application/xml`, `text/plain`.           |
| **Produces Attribute**      | Used to define the `Content-Type` of the response.                         |
| **Dynamic Content-Type**    | Can be determined based on the `Accept` header in the request.             |
| **Default Behavior**        | If `produces` is not specified, Spring tries to determine the best match.  |

