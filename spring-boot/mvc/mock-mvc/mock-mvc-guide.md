 
# MockMvc Guide

## Overview

`MockMvc` is a powerful utility in the **Spring Boot Test** framework that allows developers to test their web layer (controllers) without starting the entire application context. It simulates HTTP requests and provides a way to verify the behavior of your controllers in a lightweight and fast manner.

This guide covers the basics of using `MockMvc` for testing REST APIs, including setup, common methods, and an example test case.

---

## Prerequisites

To use `MockMvc`, ensure your project includes the following dependencies in your `pom.xml` or `build.gradle`:

### Maven
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### Gradle
```groovy
testImplementation 'org.springframework.boot:spring-boot-starter-test'
```

---

## Setting Up MockMvc

To configure `MockMvc` in your test class, you can use the `@AutoConfigureMockMvc` annotation along with `@SpringBootTest`. This ensures that the `MockMvc` instance is auto-configured and injected into your test class.

### Example Setup
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class BookControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetAllBooks() throws Exception {
        mockMvc.perform(
                get("/books")
                        .accept(MediaType.APPLICATION_JSON)
                        .queryParam("offset", "6")
                        .queryParam("limit", "3")
        ).andExpectAll(
                status().isOk()
        ).andDo(
                result -> {
                    System.out.println(result.getResponse().getContentAsString());
                }
        );
    }
}
```

---

## Key Components of MockMvc

### 1. **`perform()`**
The `perform()` method is used to perform HTTP requests. You can simulate various HTTP methods like `GET`, `POST`, `PUT`, and `DELETE`.

Example:
```java
mockMvc.perform(get("/books"));
```

### 2. **Request Builders**
MockMvc provides static methods to build requests:
- `get()`: For HTTP GET requests.
- `post()`: For HTTP POST requests.
- `put()`: For HTTP PUT requests.
- `delete()`: For HTTP DELETE requests.

Example:
```java
mockMvc.perform(post("/books").contentType(MediaType.APPLICATION_JSON).content("{\"title\":\"Book Title\"}"));
```

### 3. **Matchers**
Matchers are used to verify the response of a request. Common matchers include:
- `status()`: To check the HTTP status code.
- `content()`: To verify the response body.
- `jsonPath()`: To validate JSON responses.
- `header()`: To check response headers.

Example:
```java
.andExpect(status().isOk())
.andExpect(content().json("{\"id\":1,\"title\":\"Book Title\"}"));
```

### 4. **`andDo()`**
The `andDo()` method allows you to perform additional actions, such as logging the response or debugging.

Example:
```java
.andDo(result -> System.out.println(result.getResponse().getContentAsString()));
```

---

## Example Test Case

Here’s a complete example of testing a `GET` endpoint with query parameters:

### Test Code
```java
@Test
void testGetAllBooks() throws Exception {
    mockMvc.perform(
            get("/books")
                    .accept(MediaType.APPLICATION_JSON)
                    .queryParam("offset", "6")
                    .queryParam("limit", "3")
    ).andExpectAll(
            status().isOk()
    ).andDo(
            result -> {
                System.out.println(result.getResponse().getContentAsString());
            }
    );
}
```

### Explanation
1. **`get("/books")`**: Simulates an HTTP GET request to the `/books` endpoint.
2. **`.accept(MediaType.APPLICATION_JSON)`**: Specifies that the client expects a JSON response.
3. **`.queryParam("offset", "6")` and `.queryParam("limit", "3")`**: Adds query parameters to the request.
4. **`.andExpectAll(status().isOk())`**: Ensures the response status is `200 OK`.
5. **`.andDo()`**: Logs the response content for debugging purposes.

---

## Common Use Cases for MockMvc

1. **Testing GET Endpoints**
   ```java
   mockMvc.perform(get("/api/resource"))
          .andExpect(status().isOk())
          .andExpect(content().contentType(MediaType.APPLICATION_JSON));
   ```

2. **Testing POST Endpoints**
   ```java
   mockMvc.perform(post("/api/resource")
           .contentType(MediaType.APPLICATION_JSON)
           .content("{\"key\":\"value\"}"))
          .andExpect(status().isCreated());
   ```

3. **Testing Path Variables**
   ```java
   mockMvc.perform(get("/api/resource/{id}", 1))
          .andExpect(status().isOk());
   ```

4. **Testing Error Scenarios**
   ```java
   mockMvc.perform(get("/api/resource/invalid"))
          .andExpect(status().isNotFound());
   ```

---

## Advantages of Using MockMvc

- **Fast and Lightweight**: No need to start the full application context.
- **Comprehensive Testing**: Test request parameters, headers, and responses in detail.
- **Integration with Spring Boot**: Works seamlessly with Spring Boot’s test framework.

