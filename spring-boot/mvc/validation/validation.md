### **Validation in Spring Boot**

Spring Boot integrates seamlessly with **Bean Validation** (JSR 380) to validate incoming data for both request bodies (`@RequestBody`) and form data (`@ModelAttribute`). By leveraging annotations such as `@Valid` or `@Validated`, you can ensure that the data coming into your application meets specific requirements.

---

### **1. How Validation Works in Spring Boot**

- **Bean Validation**: Spring Boot uses **Hibernate Validator** as the default implementation of the Bean Validation API.
- **Automatic Validation**: When you annotate a parameter with `@Valid` or `@Validated`, Spring automatically validates the incoming data.
- **Response on Validation Failure**:
  - If validation fails, Spring returns a **400 Bad Request** response.
  - The exception thrown is `MethodArgumentNotValidException` for `@RequestBody` or `BindException` for `@ModelAttribute`.

---

### **2. Common Validation Annotations**

| **Annotation**         | **Description**                                                                                     |
|-------------------------|-----------------------------------------------------------------------------------------------------|
| `@NotNull`             | Ensures the value is not null.                                                                      |
| `@NotBlank`            | Ensures the string is not null, not empty, and contains at least one non-whitespace character.      |
| `@NotEmpty`            | Ensures the collection, map, or string is not empty.                                               |
| `@Size`                | Ensures the size of a string, collection, or array is within the specified range.                   |
| `@Pattern`             | Ensures the string matches a regular expression.                                                   |
| `@Email`               | Ensures the string is a valid email address.                                                       |
| `@Min` and `@Max`      | Ensures the number is within a specified range.                                                    |
| `@Positive` and `@Negative` | Ensures the number is positive or negative, respectively.                                       |
| `@Past` and `@Future`  | Ensures the date is in the past or future, respectively.                                            |

---

### **3. Validation with `@RequestBody`**

#### **Example: Validating a JSON Request Body**

##### **Step 1: Define the Model with Validation Annotations**

```java
import jakarta.validation.constraints.*;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    @NotBlank(message = "First name must not be blank")
    private String firstName;

    @NotBlank(message = "Last name must not be blank")
    private String lastName;

    @Email(message = "Email must be valid")
    private String email;

    @NotBlank(message = "Phone number must not be blank")
    private String phone;

    private List<@NotBlank(message = "Hobbies must not be blank") String> hobbies;
}
```

##### **Step 2: Controller Method**

```java
@RestController
@RequestMapping("/api/person")
public class PersonController {

    @PostMapping(consumes = MediaType.APPLICATION_JSON_VALUE, produces = MediaType.APPLICATION_JSON_VALUE)
    public ResponseEntity<String> createPerson(@Valid @RequestBody CreatePersonRequest request) {
        return ResponseEntity.ok("Person created successfully");
    }
}
```

##### **Step 3: Sending an Invalid Request**

**Request:**
```json
{
  "firstName": "",
  "lastName": "Doe",
  "email": "invalid-email",
  "phone": "",
  "hobbies": [""]
}
```

**Response:**
```json
{
  "timestamp": "2025-03-09T12:00:00.000+00:00",
  "status": 400,
  "errors": [
    "First name must not be blank",
    "Email must be valid",
    "Phone number must not be blank",
    "Hobbies must not be blank"
  ]
}
```

---

### **4. Validation with `@ModelAttribute`**

#### **Example: Validating Form Data**

##### **Step 1: Define the Model**

```java
import jakarta.validation.constraints.*;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    @NotBlank(message = "First name must not be blank")
    private String firstName;

    @NotBlank(message = "Last name must not be blank")
    private String lastName;

    @Email(message = "Email must be valid")
    private String email;

    @NotBlank(message = "Phone number must not be blank")
    private String phone;
}
```

##### **Step 2: Controller Method**

```java
@Controller
@RequestMapping("/person")
public class PersonController {

    @PostMapping(consumes = MediaType.APPLICATION_FORM_URLENCODED_VALUE)
    @ResponseBody
    public String createPerson(@ModelAttribute @Valid CreatePersonRequest request) {
        return "Person created successfully";
    }
}
```

##### **Step 3: Sending an Invalid Request**

**Request:**
```http
POST /person
Content-Type: application/x-www-form-urlencoded

firstName=&lastName=Doe&email=invalid-email&phone=
```

**Response:**
```json
{
  "timestamp": "2025-03-09T12:00:00.000+00:00",
  "status": 400,
  "errors": [
    "First name must not be blank",
    "Email must be valid",
    "Phone number must not be blank"
  ]
}
```

---

### **5. Custom Validation**

#### **Example: Custom Validator**

##### **Step 1: Create a Custom Annotation**

```java
@Documented
@Constraint(validatedBy = PhoneNumberValidator.class)
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface ValidPhoneNumber {
    String message() default "Invalid phone number";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

##### **Step 2: Implement the Validator**

```java
public class PhoneNumberValidator implements ConstraintValidator<ValidPhoneNumber, String> {

    private static final String PHONE_PATTERN = "\\d{10}";

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value != null && value.matches(PHONE_PATTERN);
    }
}
```

##### **Step 3: Use the Custom Validator**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    @NotBlank(message = "First name must not be blank")
    private String firstName;

    @ValidPhoneNumber
    private String phone;
}
```

---

### **6. Testing Validation**

#### **Test Validation with MockMvc**

```java
@SpringBootTest
@AutoConfigureMockMvc
public class PersonControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void createPersonValidationError() throws Exception {
        CreatePersonRequest request = new CreatePersonRequest();
        request.setFirstName("");
        request.setLastName("Doe");
        request.setEmail("invalid-email");
        request.setPhone("");

        mockMvc.perform(post("/api/person")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request)))
                .andExpect(status().isBadRequest());
    }
}
```

---

### **7. Handling Validation Errors**

#### **Using a Global Exception Handler**

You can customize the validation error response by creating a global exception handler.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<Map<String, Object>> handleValidationException(MethodArgumentNotValidException ex) {
        Map<String, Object> errors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error -> {
            errors.put(error.getField(), error.getDefaultMessage());
        });
        return ResponseEntity.badRequest().body(errors);
    }
}
```

**Response for Invalid Request:**
```json
{
  "firstName": "First name must not be blank",
  "email": "Email must be valid",
  "phone": "Phone number must not be blank"
}
```
