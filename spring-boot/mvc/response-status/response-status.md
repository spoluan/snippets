### **Understanding `@ResponseStatus` in Spring**

In Spring, the `@ResponseStatus` annotation is used to set the HTTP status code for a specific method or exception. By default, Spring returns a status code of `200 OK` for successful responses, but sometimes you may need to modify the response status code to indicate different outcomes (e.g., `201 CREATED`, `404 NOT FOUND`, `500 INTERNAL SERVER ERROR`, etc.).

---

### **1. What is `@ResponseStatus`?**

- **Definition**: `@ResponseStatus` is an annotation used to specify the HTTP response status code for a particular controller method or exception.
- **Purpose**:
  - To hardcode a specific HTTP status code for a method.
  - To simplify response handling without manually using `HttpServletResponse`.
- **Default Behavior**: If no status is specified, a successful response will return `200 OK`.

---

### **2. How to Use `@ResponseStatus`**

#### **2.1. Basic Example**

You can use `@ResponseStatus` to define the HTTP status code for a controller method.

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
public class ProductController {

    @DeleteMapping("/products/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT) // Sets status to 204 No Content
    public void deleteProduct(@PathVariable("id") Integer id) {
        // Logic to delete the product
    }
}
```

**Request**:
```
DELETE /products/12345
```

**Response**:
- Status Code: `204 No Content`
- No response body.

---

#### **2.2. Using `@ResponseStatus` with a Custom Message**

You can also add a custom reason to the HTTP status.

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
public class ProductController {

    @PostMapping("/products")
    @ResponseStatus(value = HttpStatus.CREATED, reason = "Product successfully created")
    public void createProduct() {
        // Logic to create a product
    }
}
```

**Request**:
```
POST /products
```

**Response**:
- Status Code: `201 Created`
- Reason Phrase: `Product successfully created`.

---

### **3. HTTP Status Codes in Spring**

Here are some commonly used HTTP status codes and their usage in Spring:

| **HTTP Status Code** | **Enum**               | **Description**                                                  |
|-----------------------|-----------------------|------------------------------------------------------------------|
| `200 OK`             | `HttpStatus.OK`       | Standard response for successful HTTP requests.                 |
| `201 Created`        | `HttpStatus.CREATED`  | Indicates that a resource has been successfully created.        |
| `204 No Content`     | `HttpStatus.NO_CONTENT` | Indicates success but no content is returned.                   |
| `400 Bad Request`    | `HttpStatus.BAD_REQUEST` | Indicates that the request is invalid.                          |
| `401 Unauthorized`   | `HttpStatus.UNAUTHORIZED` | Indicates that authentication is required.                      |
| `403 Forbidden`      | `HttpStatus.FORBIDDEN` | Indicates that the server understands the request but refuses it. |
| `404 Not Found`      | `HttpStatus.NOT_FOUND` | Indicates that the resource could not be found.                 |
| `500 Internal Server Error` | `HttpStatus.INTERNAL_SERVER_ERROR` | Indicates a server-side error.                                   |

---

### **4. Dynamic Response Status Handling**

If you need to set the response status dynamically, you can use the `HttpServletResponse` object.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

import javax.servlet.http.HttpServletResponse;

@RestController
public class DynamicStatusController {

    @GetMapping("/dynamic-status")
    public void dynamicStatus(HttpServletResponse response) {
        response.setStatus(HttpServletResponse.SC_ACCEPTED); // Sets status to 202 Accepted
    }
}
```

**Request**:
```
GET /dynamic-status
```

**Response**:
- Status Code: `202 Accepted`.

---

### **5. Exception Handling with `@ResponseStatus`**

You can use `@ResponseStatus` on custom exceptions to automatically set the response status when the exception is thrown.

#### **5.1. Custom Exception Example**

**Custom Exception**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.ResponseStatus;

@ResponseStatus(HttpStatus.NOT_FOUND) // Sets status to 404 Not Found
public class ProductNotFoundException extends RuntimeException {
    public ProductNotFoundException(String message) {
        super(message);
    }
}
```

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class ProductController {

    @GetMapping("/products/{id}")
    public String getProduct(@PathVariable("id") Integer id) {
        if (id != 1) { // Simulate product not found
            throw new ProductNotFoundException("Product not found with ID: " + id);
        }
        return "Product details";
    }
}
```

**Request**:
```
GET /products/12345
```

**Response**:
- Status Code: `404 Not Found`
- Body: `Product not found with ID: 12345`.

---

### **6. Unit Testing `@ResponseStatus`**

You can test the response status using `MockMvc`.

#### **6.1. Testing a DELETE Request**

**Test Code**:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@SpringBootTest
@AutoConfigureMockMvc
public class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void deleteProduct() throws Exception {
        mockMvc.perform(delete("/products/12345"))
               .andExpect(status().isNoContent()); // Asserts 204 No Content
    }
}
```

---

#### **6.2. Testing a Custom Exception**

**Test Code**:
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
public class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void productNotFound() throws Exception {
        mockMvc.perform(get("/products/12345"))
               .andExpect(status().isNotFound()); // Asserts 404 Not Found
    }
}
```

