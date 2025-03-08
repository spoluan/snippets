### **Understanding `@RequestParam` in Spring**

`@RequestParam` is an annotation in Spring that is used to extract **query parameters** or **form data** from an HTTP request and bind them to method parameters in a controller. It simplifies handling request parameters compared to the traditional `HttpServletRequest`.

---

### **1. What is `@RequestParam`?**

- **Definition**:
  - `@RequestParam` is used to indicate that a method parameter should be bound to a web request parameter (query parameter or form data).

- **Purpose**:
  - To extract parameters sent by the client (e.g., `/api?name=John`) and bind them directly to controller method parameters.

- **Key Features**:
  - Can specify if a parameter is **required** or **optional**.
  - Allows setting a **default value** if the parameter is not provided.
  - Automatically converts the parameter to the required type (e.g., `int`, `String`, etc.).

---

### **2. Key Attributes of `@RequestParam`**

| **Attribute** | **Description**                                                                                 | **Default Value** |
|---------------|-------------------------------------------------------------------------------------------------|-------------------|
| `name` or `value` | The name of the request parameter to bind to.                                                 | Required          |
| `required`    | Whether the parameter is mandatory. If `true`, an exception is thrown if the parameter is missing. | `true`            |
| `defaultValue`| The default value to use if the parameter is not provided.                                        | `""` (empty)      |

---

### **3. How `@RequestParam` Works**

When a client sends an HTTP request with query parameters (e.g., `/hello?name=John`), Spring automatically maps the `name` parameter to the method parameter annotated with `@RequestParam`.

---

### **4. Examples of Using `@RequestParam`**

#### **4.1. Basic Example: Required Parameter**

**Controller Code**:
```java
import org.springframework.web.bind.annotation.*;

@RestController
public class HelloController {

    @GetMapping("/hello")
    public String hello(@RequestParam(name = "name") String name) {
        return "Hello, " + name;
    }
}
```

**Request**:
```
GET /hello?name=John
```

**Response**:
```
Hello, John
```

If the `name` parameter is missing, Spring will throw a `400 Bad Request` error because the parameter is required by default.

---

#### **4.2. Optional Parameter**

You can make a parameter optional by setting `required = false`.

**Controller Code**:
```java
@GetMapping("/hello")
public String hello(@RequestParam(name = "name", required = false) String name) {
    return name != null ? "Hello, " + name : "Hello, Guest";
}
```

**Request**:
```
GET /hello
```

**Response**:
```
Hello, Guest
```

---

#### **4.3. Default Value for a Parameter**

Use the `defaultValue` attribute to provide a default value if the parameter is not sent.

**Controller Code**:
```java
@GetMapping("/hello")
public String hello(@RequestParam(name = "name", defaultValue = "Guest") String name) {
    return "Hello, " + name;
}
```

**Request**:
```
GET /hello
```

**Response**:
```
Hello, Guest
```

---

#### **4.4. Multiple Parameters**

You can use `@RequestParam` to handle multiple query parameters.

**Controller Code**:
```java
@GetMapping("/greet")
public String greet(
    @RequestParam(name = "firstName") String firstName,
    @RequestParam(name = "lastName") String lastName) {
    return "Hello, " + firstName + " " + lastName;
}
```

**Request**:
```
GET /greet?firstName=John&lastName=Doe
```

**Response**:
```
Hello, John Doe
```

---

#### **4.5. Parameter with Type Conversion**

Spring automatically converts query parameters to the required type (e.g., `int`, `boolean`).

**Controller Code**:
```java
@GetMapping("/calculate")
public String calculate(@RequestParam(name = "num1") int num1, @RequestParam(name = "num2") int num2) {
    return "Sum: " + (num1 + num2);
}
```

**Request**:
```
GET /calculate?num1=5&num2=10
```

**Response**:
```
Sum: 15
```

---

#### **4.6. Handling Missing Parameters Gracefully**

You can combine `required = false` with `defaultValue` to handle missing parameters gracefully.

**Controller Code**:
```java
@GetMapping("/welcome")
public String welcome(
    @RequestParam(name = "name", required = false, defaultValue = "Guest") String name) {
    return "Welcome, " + name;
}
```

**Request**:
```
GET /welcome
```

**Response**:
```
Welcome, Guest
```

---

#### **4.7. Using `Map` to Handle Dynamic Query Parameters**

You can use a `Map` to capture all query parameters dynamically.

**Controller Code**:
```java
@GetMapping("/params")
public String params(@RequestParam Map<String, String> params) {
    return "Parameters: " + params.toString();
}
```

**Request**:
```
GET /params?name=John&age=30
```

**Response**:
```
Parameters: {name=John, age=30}
```

---

#### **4.8. Using `@RequestParam` with Collections**

You can handle multiple values for a single parameter using a `List`.

**Controller Code**:
```java
@GetMapping("/numbers")
public String numbers(@RequestParam(name = "num") List<Integer> numbers) {
    return "Numbers: " + numbers.toString();
}
```

**Request**:
```
GET /numbers?num=1&num=2&num=3
```

**Response**:
```
Numbers: [1, 2, 3]
```

---

### **5. Common Use Cases**

1. **Form Data Handling**:
   - Extract form fields submitted via GET or POST requests.

2. **Filtering and Pagination**:
   - Use query parameters for filtering data (e.g., `/products?category=electronics&page=2`).

3. **Dynamic Queries**:
   - Capture multiple query parameters dynamically using `Map`.

4. **Default Values**:
   - Provide default values for optional parameters.

---

### **6. Comparison: `@RequestParam` vs `@PathVariable`**

| **Aspect**             | **`@RequestParam`**                                                         | **`@PathVariable`**                    |
|-------------------------|-----------------------------------------------------------------------------|----------------------------------------|
| **Purpose**            | Extract query/form parameters.                                              | Extract values from the URI path.      |
| **Example URL**         | `/api?name=John`                                                           | `/api/John`                            |
| **Annotation**          | `@RequestParam(name = "name")`                                             | `@PathVariable("name")`                |
| **Optional Parameters** | Can be optional with `required = false` or `defaultValue`.                 | Parameters are typically required.     |
| **Use Case**            | Filtering, pagination, or optional parameters.                             | Resource identification in REST APIs.  |

