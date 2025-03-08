### **RestTemplate in Spring Framework**

---

### **1. What is RestTemplate?**

`RestTemplate` is a Spring class used to make HTTP requests to RESTful APIs. It acts as a **synchronous HTTP client** that allows applications to:
- Send HTTP requests (GET, POST, PUT, DELETE, etc.).
- Receive and process HTTP responses.
- Automatically serialize/deserialize Java objects to/from JSON or XML using libraries like Jackson.

---

### **2. Key Features of RestTemplate**

1. **HTTP Methods**:  
   - Supports all HTTP methods like `GET`, `POST`, `PUT`, `DELETE`, etc.

2. **Serialization & Deserialization**:  
   - Automatically converts JSON or XML responses into Java objects and vice versa.

3. **Custom Headers**:  
   - Allows adding custom headers (e.g., for authentication).

4. **Error Handling**:  
   - Throws exceptions for HTTP errors (e.g., `HttpClientErrorException`, `HttpServerErrorException`).

5. **Simplified API**:  
   - Provides methods like `getForObject`, `postForObject`, `exchange`, etc., for easy interaction.

---

### **3. Creating a RestTemplate Bean**

Spring Boot provides a `RestTemplateBuilder` that simplifies the creation and configuration of `RestTemplate`.

#### **Example: Creating a RestTemplate Bean**
```java
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

import java.time.Duration;

@Configuration
public class RestTemplateConfig {

    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder
                .setConnectTimeout(Duration.ofSeconds(3)) // Connection timeout
                .setReadTimeout(Duration.ofSeconds(3))    // Read timeout
                .build();
    }
}
```

---

### **4. Common Methods in RestTemplate**

| **Method**                       | **Description**                                                                 |
|----------------------------------|---------------------------------------------------------------------------------|
| `getForObject(url, responseType)`| Sends a GET request and converts the response body to the specified type.       |
| `postForObject(url, request, responseType)` | Sends a POST request with a body and converts the response to the specified type. |
| `exchange(request, responseType)`| Sends a request with custom headers and HTTP method, returning a `ResponseEntity`.|
| `delete(url)`                    | Sends a DELETE request to the specified URL.                                   |
| `put(url, request)`              | Sends a PUT request with a body.                                               |

---

### **5. Using RestTemplate with Examples**

#### **5.1 GET Request Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class GetExampleController {

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/get-example")
    public String getExample() {
        String url = "https://jsonplaceholder.typicode.com/posts/1";
        return restTemplate.getForObject(url, String.class);
    }
}
```

##### **Explanation**:
- The `getForObject` method sends a GET request to the URL and returns the response as a `String`.

---

#### **5.2 POST Request Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.HashMap;
import java.util.Map;

@RestController
public class PostExampleController {

    @Autowired
    private RestTemplate restTemplate;

    @PostMapping("/post-example")
    public String postExample() {
        String url = "https://jsonplaceholder.typicode.com/posts";
        Map<String, Object> requestBody = new HashMap<>();
        requestBody.put("title", "Spring RestTemplate");
        requestBody.put("body", "Learning RestTemplate");
        requestBody.put("userId", 1);

        return restTemplate.postForObject(url, requestBody, String.class);
    }
}
```

##### **Explanation**:
- The `postForObject` method sends a POST request with a JSON body and returns the response as a `String`.

---

#### **5.3 PUT Request Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import java.util.HashMap;
import java.util.Map;

@RestController
public class PutExampleController {

    @Autowired
    private RestTemplate restTemplate;

    @PutMapping("/put-example")
    public void putExample() {
        String url = "https://jsonplaceholder.typicode.com/posts/1";
        Map<String, Object> requestBody = new HashMap<>();
        requestBody.put("id", 1);
        requestBody.put("title", "Updated Title");
        requestBody.put("body", "Updated Body");
        requestBody.put("userId", 1);

        restTemplate.put(url, requestBody);
    }
}
```

##### **Explanation**:
- The `put` method sends a PUT request to update a resource.

---

#### **5.4 DELETE Request Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class DeleteExampleController {

    @Autowired
    private RestTemplate restTemplate;

    @DeleteMapping("/delete-example")
    public void deleteExample() {
        String url = "https://jsonplaceholder.typicode.com/posts/1";
        restTemplate.delete(url);
    }
}
```

##### **Explanation**:
- The `delete` method sends a DELETE request to the specified URL.

---

### **6. Advanced Example: Using `exchange` Method**

The `exchange` method allows full control over the HTTP request, including headers, HTTP method, and body.

#### **Code: Using exchange with RequestEntity**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.*;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class ExchangeExampleController {

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping("/exchange-example")
    public String exchangeExample() {
        String url = "https://jsonplaceholder.typicode.com/posts/1";

        HttpHeaders headers = new HttpHeaders();
        headers.setAccept(List.of(MediaType.APPLICATION_JSON));
        HttpEntity<String> entity = new HttpEntity<>(headers);

        ResponseEntity<String> response = restTemplate.exchange(url, HttpMethod.GET, entity, String.class);

        return response.getBody();
    }
}
```

##### **Explanation**:
- A custom header is added using `HttpHeaders`.
- The `exchange` method sends the request and returns a `ResponseEntity`.

---

### **7. Testing RestTemplate**

#### **Test Class with `@SpringBootTest` and `@LocalServerPort`**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.web.server.LocalServerPort;
import org.springframework.http.*;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.util.MultiValueMap;
import org.springframework.web.client.RestTemplate;

import static org.junit.jupiter.api.Assertions.*;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class RestTemplateTest {

    @LocalServerPort
    private Integer port;

    @Autowired
    private RestTemplate restTemplate;

    @Test
    void addTodo() {
        String url = "http://localhost:" + port + "/todos";

        HttpHeaders headers = new HttpHeaders();
        headers.setAccept(List.of(MediaType.APPLICATION_JSON));

        MultiValueMap<String, Object> form = new LinkedMultiValueMap<>();
        form.add("todo", "Learn Spring Boot");

        RequestEntity<MultiValueMap<String, Object>> request = new RequestEntity<>(form, headers, HttpMethod.POST, URI.create(url));

        ResponseEntity<String> response = restTemplate.exchange(request, String.class);

        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertTrue(response.getBody().contains("Learn Spring Boot"));
    }

    @Test
    void getTodos() {
        String url = "http://localhost:" + port + "/todos";

        HttpHeaders headers = new HttpHeaders();
        headers.setAccept(List.of(MediaType.APPLICATION_JSON));

        RequestEntity<Void> request = new RequestEntity<>(headers, HttpMethod.GET, URI.create(url));

        ResponseEntity<String> response = restTemplate.exchange(request, String.class);

        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertTrue(response.getBody().contains("Learn Spring Boot"));
    }
}
```

---

### **8. Summary**

| **Feature**             | **Description**                                                                 |
|--------------------------|---------------------------------------------------------------------------------|
| **GET Requests**         | `getForObject`, `getForEntity`                                                 |
| **POST Requests**        | `postForObject`, `postForEntity`                                               |
| **PUT Requests**         | `put`                                                                          |
| **DELETE Requests**      | `delete`                                                                       |
| **Custom Headers**       | Use `HttpHeaders` and `exchange`.                                              |
| **Testing**              | Use `@SpringBootTest` with `RestTemplate` for integration tests.               |

`RestTemplate` is a powerful tool for interacting with RESTful APIs, and it simplifies HTTP communication in Spring applications.
