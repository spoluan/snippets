# Spring Boot Controller

## Introduction
In Spring Boot, a **Controller** is a key component of the MVC (Model-View-Controller) architecture. It handles HTTP requests, processes them, and returns appropriate responses. Controllers are typically annotated with `@RestController` or `@Controller`, depending on the application's needs.

## Types of Controllers
1. **@Controller**: Used for traditional Spring MVC applications where responses are typically views (HTML, JSP, etc.).
2. **@RestController**: A specialized controller that simplifies the creation of RESTful web services by automatically adding `@ResponseBody` to all methods.

## Key Annotations
- `@Controller`: Defines a Spring MVC controller.
- `@RestController`: A convenience annotation that combines `@Controller` and `@ResponseBody`.
- `@RequestMapping`: Specifies request URL mappings at the class or method level.
- `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: Specialized annotations for handling specific HTTP request types.
- `@PathVariable`: Extracts values from the URI.
- `@RequestParam`: Extracts values from query parameters.
- `@RequestBody`: Maps HTTP request body to a method parameter.
- `@ResponseBody`: Serializes return values to JSON or XML.

## Example: Basic REST Controller
```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class HelloController {

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello, Spring Boot!";
    }
}
```

## Example: Handling Path Variables and Request Parameters
```java
@RestController
@RequestMapping("/users")
public class UserController {
    
    @GetMapping("/{id}")
    public String getUserById(@PathVariable("id") Long userId) {
        return "User ID: " + userId;
    }

    @GetMapping
    public String getUserByName(@RequestParam("name") String name) {
        return "User Name: " + name;
    }
}
```

## Example: Handling POST Requests with @RequestBody
```java
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {
    
    @PostMapping("/create")
    public String createUser(@RequestBody User user) {
        return "User Created: " + user.getName();
    }
}

class User {
    private String name;
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}
```

## How to Use the Controller
### Running the Application
1. Ensure you have **Spring Boot** set up with the necessary dependencies (`spring-boot-starter-web`).
2. Run the application using:
   ```sh
   mvn spring-boot:run
   ```
   or
   ```sh
   ./gradlew bootRun
   ```
3. The application will start, and you can access the endpoints.

### Accessing the Endpoints
Once the application is running, you can use a browser or tools like **Postman** or **cURL** to access the APIs.

#### Example Requests:
- **GET request:** Retrieve a simple message
  ```sh
  curl -X GET http://localhost:8080/api/hello
  ```
  **Response:**
  ```json
  "Hello, Spring Boot!"
  ```

- **GET request with path variable:** Retrieve user by ID
  ```sh
  curl -X GET http://localhost:8080/users/1
  ```
  **Response:**
  ```json
  "User ID: 1"
  ```

- **GET request with query parameter:** Retrieve user by name
  ```sh
  curl -X GET "http://localhost:8080/users?name=John"
  ```
  **Response:**
  ```json
  "User Name: John"
  ```

- **POST request:** Create a user
  ```sh
  curl -X POST http://localhost:8080/users/create \
       -H "Content-Type: application/json" \
       -d '{"name": "Alice"}'
  ```
  **Response:**
  ```json
  "User Created: Alice"
  ```
