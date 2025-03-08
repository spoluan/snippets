### **Understanding Path Variables in Spring**

A **Path Variable** is a placeholder in a URL path that allows dynamic values to be passed as part of the request. This is a key feature in Spring WebMVC that enables building dynamic and flexible REST APIs. The `@PathVariable` annotation is used to extract these dynamic values from the URL and bind them to method parameters in a controller.

---

### **1. What is a Path Variable?**

- **Definition**: A **Path Variable** is a segment of the URL path that is dynamic and can be replaced with actual values at runtime.
- **Use Case**: It is commonly used in RESTful APIs to pass resource identifiers or other data in the URL.
- **Example**:
  - URL: `/orders/123/products/456`
  - Path Variables: `123` (Order ID) and `456` (Product ID)

---

### **2. Key Features of Path Variables**

1. **Dynamic URLs**: Enables dynamic routing based on URL patterns.
2. **Automatic Binding**: Spring automatically binds path variables to method parameters using `@PathVariable`.
3. **Type Conversion**: Spring supports automatic type conversion for path variables (e.g., converting `String` to `int` or `UUID`).
4. **Custom Naming**: You can specify custom variable names in the URL and map them to parameters.

---

### **3. How to Use Path Variables**

#### **Basic Syntax**
```java
@GetMapping("/path/{variable}")
public String example(@PathVariable("variable") String variable) {
    return "Value: " + variable;
}
```

---

### **4. Examples of Path Variables**

#### **4.1. Single Path Variable**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class SinglePathVariableController {

    @GetMapping("/user/{id}")
    public String getUser(@PathVariable("id") String id) {
        return "User ID: " + id;
    }
}
```

**Request**:
```
GET /user/123
```

**Response**:
```
User ID: 123
```

---

#### **4.2. Multiple Path Variables**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class MultiPathVariableController {

    @GetMapping("/orders/{orderId}/products/{productId}")
    public String getOrderProduct(
        @PathVariable("orderId") String orderId,
        @PathVariable("productId") String productId
    ) {
        return "Order: " + orderId + ", Product: " + productId;
    }
}
```

**Request**:
```
GET /orders/1/products/2
```

**Response**:
```
Order: 1, Product: 2
```

---

#### **4.3. Using Path Variable Without Explicit Name**

If the parameter name matches the variable in the URL, you can omit the name in the `@PathVariable` annotation.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class ImplicitPathVariableController {

    @GetMapping("/category/{categoryId}")
    public String getCategory(@PathVariable String categoryId) {
        return "Category ID: " + categoryId;
    }
}
```

**Request**:
```
GET /category/10
```

**Response**:
```
Category ID: 10
```

---

#### **4.4. Path Variables with Type Conversion**

Spring automatically converts the path variable to the required data type.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class TypeConversionController {

    @GetMapping("/product/{id}")
    public String getProduct(@PathVariable("id") int id) {
        return "Product ID: " + id;
    }
}
```

**Request**:
```
GET /product/123
```

**Response**:
```
Product ID: 123
```

---

#### **4.5. Default Values for Path Variables**

Although `@PathVariable` does not support default values directly, you can handle missing path variables by defining multiple endpoints or using `Optional`.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class DefaultValueController {

    @GetMapping({"/user/{id}", "/user"})
    public String getUser(@PathVariable(required = false) String id) {
        return id != null ? "User ID: " + id : "No User ID Provided";
    }
}
```

**Request Without Path Variable**:
```
GET /user
```

**Response**:
```
No User ID Provided
```

**Request With Path Variable**:
```
GET /user/123
```

**Response**:
```
User ID: 123
```

---

#### **4.6. Path Variables with Regular Expressions**

You can use regular expressions to validate path variables.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class RegexPathVariableController {

    @GetMapping("/user/{id:[a-zA-Z]+}")
    public String getUser(@PathVariable("id") String id) {
        return "User ID: " + id;
    }
}
```

**Request With Valid ID**:
```
GET /user/john
```

**Response**:
```
User ID: john
```

**Request With Invalid ID**:
```
GET /user/123
```

**Response**:
```
404 Not Found
```

---

#### **4.7. Path Variables in Nested Resources**

Path variables are especially useful in nested resources.

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class NestedResourceController {

    @GetMapping("/companies/{companyId}/employees/{employeeId}")
    public String getEmployee(
        @PathVariable("companyId") String companyId,
        @PathVariable("employeeId") String employeeId
    ) {
        return "Company: " + companyId + ", Employee: " + employeeId;
    }
}
```

**Request**:
```
GET /companies/101/employees/202
```

**Response**:
```
Company: 101, Employee: 202
```

---

#### **4.8. Unit Testing Path Variables**

You can use `MockMvc` to test endpoints with path variables.

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
public class PathVariableTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testPathVariable() throws Exception {
        mockMvc.perform(
                get("/orders/1/products/2")
        ).andExpectAll(
                status().isOk(),
                content().string("Order: 1, Product: 2")
        );
    }
}
```

