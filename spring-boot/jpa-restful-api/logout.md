 
### **1. Logout Method in AuthService**
The `logout` method clears the token and its expiration time for the authenticated user. It then saves the updated user object in the database.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class AuthService {

    @Autowired
    private UserRepository userRepository;

    @Transactional
    public void logout(User user) {
        // Clear the token and expiration time
        user.setToken(null);
        user.setTokenExpiredAt(null);

        // Save the updated user in the database
        userRepository.save(user);
    }
}
```

---

### **2. Logout Endpoint in AuthController**
The `logout` endpoint uses the `UserArgumentResolver` to resolve the currently authenticated user and calls the `logout` method in the `AuthService`.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/auth")
public class AuthController {

    @Autowired
    private AuthService authService;

    @PostMapping("/logout")
    public ResponseEntity<String> logout(User user) {
        // Call the logout method
        authService.logout(user);

        // Return a success response
        return ResponseEntity.ok("Logout successful");
    }
}
```

---

### **3. Example Workflow**

#### **Request**
**POST** `/api/auth/logout`  
**Headers:**
```http
X-API-TOKEN: <user_token>
```

#### **Response (Success)**
**Status:** `200 OK`  
**Body:**
```json
"Logout successful"
```

#### **Response (Unauthorized - Missing or Invalid Token)**
If the token is missing or invalid, the `UserArgumentResolver` will throw an exception:
**Status:** `401 Unauthorized`  
**Body:**
```json
"Unauthorized"
```

---

### **4. Explanation**
1. **Token Clearing:** The `logout` method sets both the token and the expiration time to `null`, effectively invalidating the token.
2. **Database Update:** The updated user object is saved to the database.
3. **Authentication Middleware:** The `UserArgumentResolver` ensures that only authenticated users can access the logout endpoint.
