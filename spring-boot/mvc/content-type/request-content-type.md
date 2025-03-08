### **Understanding Content-Type in Spring**

In HTTP, the `Content-Type` header is used to specify the **media type** of the request or response body. In Spring, you can control and handle the `Content-Type` of incoming requests using the `@RequestMapping` or its specialized annotations like `@PostMapping`, `@GetMapping`, etc. This allows you to **restrict or process specific content types**.

---

### **1. What is Content-Type?**

- The `Content-Type` header indicates the type of data being sent in the HTTP request or response.
- For example:
  - `application/json`: JSON data
  - `application/xml`: XML data
  - `application/x-www-form-urlencoded`: Form data (key-value pairs)
  - `multipart/form-data`: File uploads
  - `text/plain`: Plain text
- In Spring, you can **consume** specific content types in controller methods by using the `consumes` attribute in mapping annotations.

---

### **2. Restricting Content-Type in Spring**

To restrict the type of content a controller method can accept, you can use the `consumes` attribute in mapping annotations like `@RequestMapping`, `@PostMapping`, etc.

#### **Syntax**
```java
@PostMapping(path = "/example", consumes = MediaType.APPLICATION_JSON_VALUE)
```

The `consumes` attribute ensures that the method only processes requests with the specified `Content-Type`.

---

### **3. Examples of Handling Content-Type**

#### **3.1. Handling Form Data (`application/x-www-form-urlencoded`)**

This content type is commonly used when submitting HTML forms.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@Controller
public class FormController {

    @PostMapping(path = "/form/hello", consumes = MediaType.APPLICATION_FORM_URLENCODED_VALUE)
    @ResponseBody
    public String hello(@RequestParam(name = "name") String name) {
        return "Hello " + name;
    }
}
```

**Unit Test**:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class FormControllerTest {

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
                content().string("Hello Eko")
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
Hello Eko
```

---

#### **3.2. Handling JSON Requests (`application/json`)**

JSON is a common format for APIs.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class JsonController {

    @PostMapping(path = "/json/hello", consumes = MediaType.APPLICATION_JSON_VALUE)
    public String hello(@RequestBody User user) {
        return "Hello " + user.getName();
    }
}

class User {
    private String name;

    // Getter and Setter
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

**Request**:
```
POST /json/hello
Content-Type: application/json

{
  "name": "Eko"
}
```

**Response**:
```
Hello Eko
```

---

#### **3.3. Handling XML Requests (`application/xml`)**

XML is another common format for APIs, especially in legacy systems.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

import javax.xml.bind.annotation.XmlRootElement;

@RestController
public class XmlController {

    @PostMapping(path = "/xml/hello", consumes = MediaType.APPLICATION_XML_VALUE)
    public String hello(@RequestBody User user) {
        return "Hello " + user.getName();
    }
}

@XmlRootElement
class User {
    private String name;

    // Getter and Setter
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

**Request**:
```
POST /xml/hello
Content-Type: application/xml

<User>
    <name>Eko</name>
</User>
```

**Response**:
```
Hello Eko
```

---

#### **3.4. Handling Plain Text (`text/plain`)**

You can accept plain text data using the `text/plain` content type.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
public class TextController {

    @PostMapping(path = "/text/hello", consumes = MediaType.TEXT_PLAIN_VALUE)
    public String hello(@RequestBody String name) {
        return "Hello " + name;
    }
}
```

**Request**:
```
POST /text/hello
Content-Type: text/plain

Eko
```

**Response**:
```
Hello Eko
```

---

#### **3.5. Handling Multipart Data (`multipart/form-data`)**

This is used for file uploads.

**Controller Code**:
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

@RestController
public class FileUploadController {

    @PostMapping(path = "/upload", consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
    public String uploadFile(@RequestParam("file") MultipartFile file) {
        return "Uploaded file: " + file.getOriginalFilename();
    }
}
```

**Request**:
```
POST /upload
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary

------WebKitFormBoundary
Content-Disposition: form-data; name="file"; filename="example.txt"
Content-Type: text/plain

File content here...
------WebKitFormBoundary--
```

**Response**:
```
Uploaded file: example.txt
```

---

### **4. Multiple Content-Type Support**

You can allow multiple content types for a single method by specifying an array for the `consumes` attribute.

**Example**:
```java
@PostMapping(path = "/multi", consumes = {MediaType.APPLICATION_JSON_VALUE, MediaType.APPLICATION_XML_VALUE})
public String handleMultiple(@RequestBody User user) {
    return "Hello " + user.getName();
}
```

This method will accept both `application/json` and `application/xml` requests.

---

### **5. Error Handling for Unsupported Content-Type**

If the client sends an unsupported `Content-Type`, Spring automatically responds with a `415 Unsupported Media Type` error.

**Example**:
```json
{
  "timestamp": "2023-03-09T10:00:00",
  "status": 415,
  "error": "Unsupported Media Type",
  "message": "Content type 'text/plain' not supported",
  "path": "/json/hello"
}
```

---

### **6. Key Points**

| **Aspect**                  | **Details**                                                                 |
|-----------------------------|-----------------------------------------------------------------------------|
| **Purpose**                 | Restrict or process specific content types in requests.                    |
| **Common Content Types**    | `application/json`, `application/xml`, `text/plain`, `multipart/form-data`. |
| **Consumes Attribute**      | Used to specify the allowed `Content-Type` for a method.                   |
| **Default Behavior**        | If `consumes` is not specified, the method accepts all content types.       |
| **Error for Unsupported**   | Spring responds with `415 Unsupported Media Type` if the content type is invalid. |

