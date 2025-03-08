### **Understanding File Upload in Spring**

File upload is a common feature in web applications where users can send files (e.g., images, documents, etc.) to the server. In Spring MVC, file uploads are handled using the `MultipartFile` class and the `@RequestPart` or `@RequestParam` annotations.

---

### **1. What is File Upload in Spring?**

- **Definition**: File upload allows users to send files from the client (browser) to the server.
- **Multipart Requests**: File uploads are typically handled via `multipart/form-data` requests.
- **Spring Features**:
  - Spring provides built-in support for file uploads using `MultipartFile`.
  - You can configure file upload properties (e.g., size limits, storage location) in `application.properties`.

---

### **2. Key Components**

1. **`@RequestPart`**: Used to process parts of a multipart request, including files.
2. **`MultipartFile`**: Represents the uploaded file.
3. **Configuration**:
   - Spring Boot allows configuration of file upload settings using the `spring.servlet.multipart` prefix in `application.properties`.
4. **Testing**: Use `MockMultipartFile` in unit tests to simulate file uploads.

---

### **3. File Upload Configuration**

You can configure file upload properties in `application.properties`:

| **Property**                          | **Description**                                      | **Default Value** |
|---------------------------------------|------------------------------------------------------|-------------------|
| `spring.servlet.multipart.enabled`    | Enables multipart support.                          | `true`            |
| `spring.servlet.multipart.max-file-size` | Maximum size of a single file.                     | `1MB`             |
| `spring.servlet.multipart.max-request-size` | Maximum size of a request (including all files).   | `10MB`            |
| `spring.servlet.multipart.file-size-threshold` | Threshold after which files are written to disk.   | `0B`              |
| `spring.servlet.multipart.location`   | Directory where uploaded files are stored.          | Temporary dir     |

**Example Configuration**:
```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.max-file-size=5MB
spring.servlet.multipart.max-request-size=20MB
spring.servlet.multipart.location=/uploads
```

---

### **4. How to Handle File Upload in Spring**

#### **4.1. Basic File Upload Example**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
public class FileUploadController {

    @PostMapping("/upload")
    public String uploadFile(@RequestParam("file") MultipartFile file) throws IOException {
        // Save file to local directory
        Path path = Paths.get("uploads/" + file.getOriginalFilename());
        Files.write(path, file.getBytes());
        return "File uploaded successfully: " + file.getOriginalFilename();
    }
}
```

**Request**:
```
POST /upload
Content-Type: multipart/form-data

file=<file>
```

**Response**:
```
File uploaded successfully: example.txt
```

---

#### **4.2. File Upload with Additional Parameters**

You can upload a file along with additional form data.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
public class FileUploadWithParamsController {

    @PostMapping("/upload/profile")
    public String uploadProfile(
        @RequestParam("name") String name,
        @RequestParam("file") MultipartFile file
    ) throws IOException {
        // Save file to local directory
        Path path = Paths.get("uploads/" + file.getOriginalFilename());
        Files.write(path, file.getBytes());
        return "Profile uploaded for " + name + " with file " + file.getOriginalFilename();
    }
}
```

**Request**:
```
POST /upload/profile
Content-Type: multipart/form-data

name=John
file=<file>
```

**Response**:
```
Profile uploaded for John with file example.txt
```

---

#### **4.3. Using `@RequestPart` for File Upload**

`@RequestPart` can also be used to handle file uploads.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
public class RequestPartController {

    @PostMapping(path = "/upload/profile", consumes = "multipart/form-data")
    public String uploadWithRequestPart(
        @RequestPart("name") String name,
        @RequestPart("profile") MultipartFile profile
    ) throws IOException {
        Path path = Paths.get("uploads/" + profile.getOriginalFilename());
        Files.write(path, profile.getBytes());
        return "Success save profile " + name + " to " + path;
    }
}
```

---

#### **4.4. File Upload with Validation**

You can validate file uploads (e.g., size, type) manually or using libraries like Apache Commons FileUpload.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;

@RestController
public class FileValidationController {

    @PostMapping("/upload/validate")
    public String validateFile(@RequestParam("file") MultipartFile file) throws IOException {
        if (file.getSize() > 1048576) { // 1MB
            return "File size exceeds the limit!";
        }
        if (!file.getContentType().equals("image/png")) {
            return "Only PNG files are allowed!";
        }
        return "File uploaded successfully: " + file.getOriginalFilename();
    }
}
```

---

#### **4.5. File Upload Testing**

You can test file uploads using `MockMultipartFile`.

**Test Code**:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.mock.web.MockMultipartFile;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.multipart;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class FileUploadTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testFileUpload() throws Exception {
        MockMultipartFile file = new MockMultipartFile(
            "file", "example.txt", "text/plain", "Sample content".getBytes()
        );

        mockMvc.perform(multipart("/upload").file(file))
               .andExpect(status().isOk())
               .andExpect(content().string("File uploaded successfully: example.txt"));
    }
}
```

---

#### **4.6. File Upload with Custom Directory**

You can specify a custom directory for uploaded files.

**Controller Code**:
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.multipart.MultipartFile;

import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;

@RestController
public class CustomDirectoryController {

    @Value("${upload.directory}")
    private String uploadDirectory;

    @PostMapping("/upload/custom")
    public String uploadToCustomDir(@RequestParam("file") MultipartFile file) throws IOException {
        Path path = Paths.get(uploadDirectory + "/" + file.getOriginalFilename());
        Files.write(path, file.getBytes());
        return "File uploaded to custom directory: " + path;
    }
}
```

**Configuration**:
```properties
upload.directory=/custom/uploads
```

