To implement **login functionality** and use the **UserArgumentResolver** for user authentication and authorization, here's a structured explanation and implementation based on the code you provided.

---

### **1. Login Process Overview**
The login feature will:
1. Authenticate the user by verifying their username and password.
2. Generate a token for the user upon successful login.
3. Save the token and its expiration time in the database.
4. Use the `UserArgumentResolver` to resolve the currently authenticated user based on the token sent in the request header.

---

### **2. LoginUserRequest**
This DTO represents the login request, containing the username and password.

```java
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Size;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class LoginUserRequest {

    @NotBlank
    @Size(max = 100)
    private String username;

    @NotBlank
    @Size(max = 100)
    private String password;
}
```

---

### **3. TokenResponse**
This DTO represents the response sent back to the client after a successful login.

```java
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class TokenResponse {

    private String token;
    private Long expiredAt;
}
```

---

### **4. AuthService**
The `AuthService` handles the login logic, including password verification, token generation, and updating the user record.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.security.crypto.bcrypt.BCrypt;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.web.server.ResponseStatusException;

import java.util.UUID;

@Service
public class AuthService {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private ValidationService validationService;

    @Transactional
    public TokenResponse login(LoginUserRequest request) {
        // Validate the request
        validationService.validate(request);

        // Find the user by username
        User user = userRepository.findById(request.getUsername())
                .orElseThrow(() -> new ResponseStatusException(HttpStatus.UNAUTHORIZED, "Username or password wrong"));

        // Verify the password
        if (BCrypt.checkpw(request.getPassword(), user.getPassword())) {
            // Generate a new token and set expiration date
            user.setToken(UUID.randomUUID().toString());
            user.setTokenExpiredAt(next30Days()); // Method to calculate expiration time (30 days from now)

            // Save the updated user
            userRepository.save(user);

            // Return the token response
            return TokenResponse.builder()
                    .token(user.getToken())
                    .expiredAt(user.getTokenExpiredAt())
                    .build();
        } else {
            throw new ResponseStatusException(HttpStatus.UNAUTHORIZED, "Username or password wrong");
        }
    }

    private Long next30Days() {
        return System.currentTimeMillis() + (30L * 24 * 60 * 60 * 1000); // 30 days in milliseconds
    }
}
```

---

### **5. AuthController**
The `AuthController` exposes the login endpoint to clients.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/auth")
public class AuthController {

    @Autowired
    private AuthService authService;

    @PostMapping(
            path = "/login",
            consumes = MediaType.APPLICATION_JSON_VALUE,
            produces = MediaType.APPLICATION_JSON_VALUE
    )
    public WebResponse<TokenResponse> login(@RequestBody LoginUserRequest request) {
        TokenResponse tokenResponse = authService.login(request);
        return WebResponse.<TokenResponse>builder()
                .data(tokenResponse)
                .build();
    }
}
```

---

### **6. UserArgumentResolver**
The `UserArgumentResolver` resolves the current authenticated user based on the token sent in the request header (`X-API-TOKEN`).

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.support.WebDataBinderFactory;
import org.springframework.web.context.request.NativeWebRequest;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.method.support.ModelAndViewContainer;
import org.springframework.web.server.ResponseStatusException;

import jakarta.servlet.http.HttpServletRequest;

@Component
public class UserArgumentResolver implements HandlerMethodArgumentResolver {

    @Autowired
    private UserRepository userRepository;

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        return User.class.equals(parameter.getParameterType());
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest, WebDataBinderFactory binderFactory) {
        HttpServletRequest servletRequest = (HttpServletRequest) webRequest.getNativeRequest();
        String token = servletRequest.getHeader("X-API-TOKEN");

        if (token == null) {
            throw new ResponseStatusException(HttpStatus.UNAUTHORIZED, "Unauthorized");
        }

        return userRepository.findFirstByToken(token)
                .orElseThrow(() -> new ResponseStatusException(HttpStatus.UNAUTHORIZED, "Unauthorized"));
    }
}
```

---

### **7. WebConfiguration**
The `WebConfiguration` class registers the `UserArgumentResolver` with Spring.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.method.support.HandlerMethodArgumentResolver;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import java.util.List;

@Configuration
public class WebConfiguration implements WebMvcConfigurer {

    @Autowired
    private UserArgumentResolver userArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(userArgumentResolver);
    }
}
```

---

### **8. Example of Secured Endpoint**
This endpoint demonstrates how to use the `UserArgumentResolver` to retrieve the authenticated user.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping(
            path = "/current",
            produces = MediaType.APPLICATION_JSON_VALUE
    )
    public WebResponse<UserResponse> getCurrentUser(User user) {
        UserResponse userResponse = userService.get(user);
        return WebResponse.<UserResponse>builder()
                .data(userResponse)
                .build();
    }
}
```

---

### **9. Sample Request and Response**

#### **Login Request**
**POST** `/api/auth/login`  
**Header:**
```http
Content-Type: application/json
```

**Body:**
```json
{
  "username": "john_doe",
  "password": "password123"
}
```

**Response:**
```json
{
  "data": {
    "token": "f4c2d4b8-5f4b-4f2b-8bfb-6e8b8e8e8e8e",
    "expiredAt": 1735689600000
  }
}
```

#### **Authenticated Request**
**GET** `/api/users/current`  
**Header:**
```http
X-API-TOKEN: f4c2d4b8-5f4b-4f2b-8bfb-6e8b8e8e8e8e
```

**Response:**
```json
{
  "data": {
    "username": "john_doe",
    "name": "John Doe"
  }
}
```

