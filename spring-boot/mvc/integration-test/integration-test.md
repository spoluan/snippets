### Integration Testing in Spring Boot

Integration testing is a crucial part of testing in Spring Boot applications, where the **entire application context** is loaded, and the application is tested as a whole, including the web server, controllers, and other components. Unlike unit testing with `MockMvc`, integration testing runs the actual application and interacts with it through real HTTP requests.

---

### **1. What is an Integration Test?**

- **Definition**:
  - An integration test involves running the entire application, including the web server (e.g., Apache Tomcat), and testing the interaction between different components of the application.

- **Key Differences from MockMVC**:
  - MockMVC only simulates requests and responses without running the actual application.
  - Integration tests run the full application stack, including the embedded server, and send real HTTP requests.

- **Purpose**:
  - To test the behavior of the application in a real-world scenario.
  - Ensures that all layers (controller, service, repository) work together correctly.

---

### **2. Benefits of Integration Tests**

1. **Real-World Testing**:
   - Simulates real HTTP requests and tests the application as it would behave in production.

2. **End-to-End Validation**:
   - Tests the interaction between different layers of the application (e.g., controller, service, repository).

3. **Full Application Context**:
   - Loads the entire Spring application context, ensuring all beans are configured correctly.

4. **Catches Configuration Issues**:
   - Detects issues related to bean wiring, application properties, or dependency injection.

---

### **3. Setting Up Integration Tests**

#### **Dependencies**
Ensure you have the Spring Boot testing dependency in your `pom.xml`:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

#### **Annotations**
- `@SpringBootTest`: Loads the entire Spring application context for testing.
- `@Test`: Marks a method as a test case.
- `@LocalServerPort`: Injects the port number of the running application when using a random port.

---

### **4. Running Integration Tests**

Integration tests in Spring Boot can be run with the `@SpringBootTest` annotation. By default, the application will start on the port specified in the `application.properties` file. However, you can configure it to use a **random port** to avoid conflicts.

#### **Example: Basic Integration Test**
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void testHelloGuest() {
        String response = restTemplate.getForObject("/hello", String.class);
        Assertions.assertEquals("Hello Guest", response.trim());
    }

    @Test
    public void testHelloName() {
        String response = restTemplate.getForObject("/hello?name=John", String.class);
        Assertions.assertEquals("Hello John", response.trim());
    }
}
```

---

### **5. Key Components of Integration Testing**

#### **TestRestTemplate**
- A special HTTP client provided by Spring for integration testing.
- Used to send real HTTP requests to the running application.
- Supports GET, POST, PUT, DELETE, and other HTTP methods.

##### Example Usage:
```java
@Autowired
private TestRestTemplate restTemplate;

@Test
public void testGetRequest() {
    String response = restTemplate.getForObject("/api/resource", String.class);
    Assertions.assertNotNull(response);
}
```

---

#### **Random Port**
- Using a random port ensures that your tests do not conflict with other applications running on the same machine.
- Spring will automatically detect an available port and use it for the test.

##### Example:
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class MyIntegrationTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void testWithRandomPort() {
        String url = "http://localhost:" + port + "/hello";
        String response = restTemplate.getForObject(url, String.class);
        Assertions.assertEquals("Hello Guest", response.trim());
    }
}
```

---

### **6. Integration Test Example**

#### **Controller Code**
```java
@RestController
@RequestMapping("/hello")
public class HelloController {

    @GetMapping
    public String hello(@RequestParam(required = false) String name) {
        return "Hello " + (name != null ? name : "Guest");
    }
}
```

#### **Integration Test Code**
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class HelloControllerIntegrationTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void testHelloGuest() {
        String response = restTemplate.getForObject("http://localhost:" + port + "/hello", String.class);
        Assertions.assertEquals("Hello Guest", response.trim());
    }

    @Test
    public void testHelloName() {
        String response = restTemplate.getForObject("http://localhost:" + port + "/hello?name=Eko", String.class);
        Assertions.assertEquals("Hello Eko", response.trim());
    }
}
```

---

### **7. Assertions in Integration Tests**

Spring Boot integration tests often use JUnit assertions to validate the results. Here are some common assertions:

#### **Check Response Body**
```java
Assertions.assertEquals("Expected Response", actualResponse);
```

#### **Check Response Not Null**
```java
Assertions.assertNotNull(response);
```

#### **Check HTTP Status Code**
If you need to validate the HTTP status code, you can use `ResponseEntity`:
```java
ResponseEntity<String> response = restTemplate.getForEntity("/hello", String.class);
Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
```

---

### **8. Integration Test with POST Request**

#### **Controller Code**
```java
@RestController
@RequestMapping("/api")
public class ApiController {

    @PostMapping("/submit")
    public ResponseEntity<String> submit(@RequestBody Map<String, String> payload) {
        return ResponseEntity.ok("Received: " + payload.get("name"));
    }
}
```

#### **Integration Test Code**
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ApiControllerIntegrationTest {

    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    public void testPostRequest() {
        Map<String, String> request = new HashMap<>();
        request.put("name", "John");

        ResponseEntity<String> response = restTemplate.postForEntity(
            "http://localhost:" + port + "/api/submit", request, String.class);

        Assertions.assertEquals(HttpStatus.OK, response.getStatusCode());
        Assertions.assertEquals("Received: John", response.getBody());
    }
}
```

---

### **9. Advantages of Integration Testing**

1. **Real Application Behavior**:
   - Tests how the application behaves when running as a whole.

2. **Detects End-to-End Issues**:
   - Identifies problems that may not be visible in unit tests (e.g., configuration issues, dependency mismatches).

3. **Validates Application Context**:
   - Ensures that all beans are correctly wired and the application context is loaded properly.

