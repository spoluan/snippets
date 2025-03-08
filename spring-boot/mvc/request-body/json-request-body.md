### **Understanding JSON Request Body in Spring Boot**

Spring Boot provides excellent support for handling JSON data through its integration with the **Jackson library**. JSON is commonly used for exchanging data between a client and a server in REST APIs. Spring simplifies the process of consuming and producing JSON data using `@RequestBody` and `@ResponseBody` annotations.

---

### **1. What is JSON Request Body?**

- A **JSON request body** is the payload of an HTTP request sent to the server in JSON format. It contains structured data that can be deserialized into Java objects on the server side.
- Spring handles JSON serialization and deserialization automatically using **Jackson**.
- For example, a client can send a JSON payload like this:
  ```json
  {
    "firstName": "Sevendi",
    "lastName": "Poluan",
    "email": "Sevendi.Poluan@example.com"
  }
  ```
  Spring will map this JSON into a Java object.

---

### **2. How Does Spring Handle JSON?**

- **Jackson Integration**: Spring Boot uses the **Jackson library** to convert JSON into Java objects (deserialization) and Java objects into JSON (serialization).
- You do not need to configure Jackson manually. Spring Boot auto-configures it.
- Use the `@RequestBody` annotation to bind a JSON request body to a Java object.
- Use the `@ResponseBody` annotation to return a Java object as a JSON response.

---

### **3. Example: Handling JSON Request Body**

#### **Step 1: Define the Model Class**

**Person Class:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    private String firstName;
    private String lastName;
    private String email;
    private String phone;
}
```

---

#### **Step 2: Controller Code**

**Person API Controller:**
```java
import org.springframework.http.MediaType;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/person")
public class PersonController {

    @PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public CreatePersonRequest createPerson(@RequestBody CreatePersonRequest request) {
        // Here you can process the request object
        return request; // Returning the request object as the response
    }
}
```

---

#### **Step 3: Sending a JSON Request**

**Example JSON Request:**
```json
{
  "firstName": "Sevendi",
  "lastName": "Poluan",
  "email": "Sevendi.Poluan@example.com",
  "phone": "1234567890"
}
```

**cURL Command:**
```bash
curl -X POST \
  http://localhost:8080/api/person \
  -H "Content-Type: application/json" \
  -d '{
        "firstName": "Sevendi",
        "lastName": "Poluan",
        "email": "Sevendi.Poluan@example.com",
        "phone": "1234567890"
      }'
```

**Response:**
```json
{
  "firstName": "Sevendi",
  "lastName": "Poluan",
  "email": "Sevendi.Poluan@example.com",
  "phone": "1234567890"
}
```

---

### **4. Nested JSON Request Body**

You can handle nested JSON objects by defining nested Java classes.

#### **Example: Nested JSON**

**JSON Request:**
```json
{
  "firstName": "Sevendi",
  "lastName": "Poluan",
  "email": "Sevendi.Poluan@example.com",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "country": "USA"
  }
}
```

**Model Classes:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    private String firstName;
    private String lastName;
    private String email;
    private CreateAddressRequest address; // Nested Object
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateAddressRequest {
    private String street;
    private String city;
    private String country;
}
```

**Controller Code:**
```java
@RestController
@RequestMapping("/api/person")
public class PersonController {

    @PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public CreatePersonRequest createPerson(@RequestBody CreatePersonRequest request) {
        return request; // Return the request object as the response
    }
}
```

---

### **5. Handling Lists in JSON**

You can handle JSON arrays by using `List` in your model.

#### **Example: JSON with Lists**

**JSON Request:**
```json
{
  "firstName": "Sevendi",
  "lastName": "Poluan",
  "email": "Sevendi.Poluan@example.com",
  "hobbies": ["Coding", "Reading"],
  "socialMedias": [
    {
      "name": "Facebook",
      "location": "facebook.com/SevendiPoluan"
    },
    {
      "name": "Twitter",
      "location": "twitter.com/SevendiPoluan"
    }
  ]
}
```

**Model Classes:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    private String firstName;
    private String lastName;
    private String email;
    private List<String> hobbies; // List of primitive values
    private List<CreateSocialMediaRequest> socialMedias; // List of objects
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateSocialMediaRequest {
    private String name;
    private String location;
}
```

**Controller Code:**
```java
@RestController
@RequestMapping("/api/person")
public class PersonController {

    @PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseBody
    public CreatePersonRequest createPerson(@RequestBody CreatePersonRequest request) {
        return request;
    }
}
```

---

### **6. Configuring Jackson**

Spring Boot allows you to configure Jackson using the `application.properties` file.

#### **Common Jackson Properties:**

| Property                               | Description                                                      |
|---------------------------------------|------------------------------------------------------------------|
| `spring.jackson.default-property-inclusion` | Controls the inclusion of properties during serialization.       |
| `spring.jackson.property-naming-strategy`   | Defines the naming strategy (e.g., snake_case or camelCase).     |
| `spring.jackson.time-zone`                 | Sets the time zone for date formatting.                         |
| `spring.jackson.locale`                    | Specifies the locale for formatting.                            |

**Example:**
```properties
spring.jackson.default-property-inclusion=non_null
spring.jackson.property-naming-strategy=SNAKE_CASE
spring.jackson.time-zone=America/New_York
spring.jackson.locale=en_US
```

---

### **7. Testing JSON Request Body**

You can test JSON request handling using `MockMvc`.

#### **Test Code:**
```java
@SpringBootTest
@AutoConfigureMockMvc
public class PersonControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void createPerson() throws Exception {
        CreatePersonRequest request = new CreatePersonRequest();
        request.setFirstName("Sevendi");
        request.setLastName("Poluan");
        request.setEmail("Sevendi.Poluan@example.com");
        request.setHobbies(List.of("Coding", "Reading"));

        List<CreateSocialMediaRequest> socialMedias = new ArrayList<>();
        socialMedias.add(new CreateSocialMediaRequest("Facebook", "facebook.com/SevendiPoluan"));
        socialMedias.add(new CreateSocialMediaRequest("Twitter", "twitter.com/SevendiPoluan"));
        request.setSocialMedias(socialMedias);

        mockMvc.perform(post("/api/person")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isOk())
                .andExpect(content().json(objectMapper.writeValueAsString(request)));
    }
}
```
