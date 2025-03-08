### **Understanding `ResponseEntity` in Spring**

`ResponseEntity` is a flexible and powerful class provided by Spring to handle HTTP responses. It allows you to control not only the response body but also the HTTP status code, headers, and other response details. This makes it an essential tool for building RESTful APIs.

---

### **1. What is `ResponseEntity`?**

- **Definition**: `ResponseEntity` is a generic class (`ResponseEntity<T>`) that represents the entire HTTP response, including the status code, headers, and body.
- **Purpose**:
  - To provide full control over the HTTP response.
  - To allow dynamic setting of status codes, headers, and response bodies.
- **Key Features**:
  - **Status Code**: Set any HTTP status code (e.g., `200 OK`, `404 NOT FOUND`, etc.).
  - **Headers**: Add custom headers to the response.
  - **Body**: Return any type of response body (e.g., JSON, plain text, etc.).

---

### **2. Basic Usage of `ResponseEntity`**

#### **2.1. Returning a Simple Response**

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class ExampleController {

    @GetMapping("/hello")
    public ResponseEntity<String> sayHello() {
        return new ResponseEntity<>("Hello, World!", HttpStatus.OK);
    }
}
```

**Request**:
```
GET /hello
```

**Response**:
- **Status Code**: `200 OK`
- **Body**: `Hello, World!`

---

#### **2.2. Returning a Response with Headers**

**Controller Code**:
```java
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class ExampleController {

    @GetMapping("/custom-headers")
    public ResponseEntity<String> customHeaders() {
        HttpHeaders headers = new HttpHeaders();
        headers.add("Custom-Header", "CustomHeaderValue");

        return new ResponseEntity<>("Hello with custom headers!", headers, HttpStatus.OK);
    }
}
```

**Request**:
```
GET /custom-headers
```

**Response**:
- **Status Code**: `200 OK`
- **Headers**:
  - `Custom-Header: CustomHeaderValue`
- **Body**: `Hello with custom headers!`

---

### **3. Using `ResponseEntity` for Dynamic Responses**

#### **3.1. Dynamic Status Codes**

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class AuthController {

    @PostMapping("/auth/login")
    public ResponseEntity<String> login(@RequestParam String username, @RequestParam String password) {
        if ("user".equals(username) && "password".equals(password)) {
            return new ResponseEntity<>("Login Successful", HttpStatus.OK);
        } else {
            return new ResponseEntity<>("Invalid Credentials", HttpStatus.UNAUTHORIZED);
        }
    }
}
```

**Request (Success)**:
```
POST /auth/login
Content-Type: application/x-www-form-urlencoded

username=user&password=password
```

**Response (Success)**:
- **Status Code**: `200 OK`
- **Body**: `Login Successful`

**Request (Failure)**:
```
POST /auth/login
Content-Type: application/x-www-form-urlencoded

username=user&password=wrongpassword
```

**Response (Failure)**:
- **Status Code**: `401 Unauthorized`
- **Body**: `Invalid Credentials`

---

#### **3.2. Response with No Body**

You can return a response without a body by setting it to `null` or using `ResponseEntity<Void>`.

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class ExampleController {

    @DeleteMapping("/delete/{id}")
    public ResponseEntity<Void> deleteItem(@PathVariable int id) {
        // Simulate deletion logic
        return new ResponseEntity<>(HttpStatus.NO_CONTENT);
    }
}
```

**Request**:
```
DELETE /delete/1
```

**Response**:
- **Status Code**: `204 No Content`
- **Body**: None

---

### **4. Advanced Usage of `ResponseEntity`**

#### **4.1. Returning JSON Data**

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class ProductController {

    @GetMapping("/product/{id}")
    public ResponseEntity<Product> getProduct(@PathVariable int id) {
        Product product = new Product(id, "Laptop", 1200.00);
        return new ResponseEntity<>(product, HttpStatus.OK);
    }
}

class Product {
    private int id;
    private String name;
    private double price;

    // Constructor, Getters, and Setters
    public Product(int id, String name, double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }

    // Getters and Setters omitted for brevity
}
```

**Request**:
```
GET /product/1
```

**Response**:
- **Status Code**: `200 OK`
- **Body**:
```json
{
  "id": 1,
  "name": "Laptop",
  "price": 1200.0
}
```

---

#### **4.2. Returning a List of Objects**

**Controller Code**:
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Arrays;
import java.util.List;

@RestController
public class ProductController {

    @GetMapping("/products")
    public ResponseEntity<List<Product>> getProducts() {
        List<Product> products = Arrays.asList(
            new Product(1, "Laptop", 1200.00),
            new Product(2, "Phone", 800.00)
        );
        return new ResponseEntity<>(products, HttpStatus.OK);
    }
}
```

**Request**:
```
GET /products
```

**Response**:
- **Status Code**: `200 OK`
- **Body**:
```json
[
  {
    "id": 1,
    "name": "Laptop",
    "price": 1200.0
  },
  {
    "id": 2,
    "name": "Phone",
    "price": 800.0
  }
]
```

---

### **5. Unit Testing with `ResponseEntity`**

#### **5.1. Testing Successful Login**

**Test Code**:
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
public class AuthControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void loginSuccess() throws Exception {
        mockMvc.perform(post("/auth/login")
                .contentType(MediaType.APPLICATION_FORM_URLENCODED)
                .param("username", "user")
                .param("password", "password"))
                .andExpect(status().isOk())
                .andExpect(content().string("Login Successful"));
    }
}
```

---

#### **5.2. Testing Failed Login**

**Test Code**:
```java
@Test
void loginFailed() throws Exception {
    mockMvc.perform(post("/auth/login")
            .contentType(MediaType.APPLICATION_FORM_URLENCODED)
            .param("username", "user")
            .param("password", "wrongpassword"))
            .andExpect(status().isUnauthorized())
            .andExpect(content().string("Invalid Credentials"));
}
```
