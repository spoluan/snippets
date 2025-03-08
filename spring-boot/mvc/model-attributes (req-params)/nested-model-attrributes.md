### **Understanding Nested Model Attributes in Spring**

A **nested model attribute** in Spring refers to the ability of `@ModelAttribute` to bind request parameters not only to a top-level object but also to its nested objects. This is a powerful feature that allows you to handle complex data structures in your request payloads.

---

### **1. What is a Nested Model Attribute?**

- A **nested model attribute** is an object inside another object (a nested Java Bean) that is automatically populated by Spring during request binding.
- This is useful when dealing with forms or requests that include hierarchical or complex data structures.
- **Example**: A `Person` object that contains an `Address` object as one of its fields.

---

### **2. How Does it Work?**

- Spring uses **dot notation** in request parameters to bind data to nested objects.
- For example:
  - `address.street` → Binds to the `street` field of the `Address` object inside the `Person` object.
  - `address.city` → Binds to the `city` field of the `Address` object.

---

### **3. Example: Nested Model Attribute**

#### **Step 1: Define the Nested Classes**

**Person Class:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    private String firstName;
    private String middleName;
    private String lastName;
    private String email;
    private String phone;
    private CreateAddressRequest address; // Nested Object
}
```

**Address Class:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateAddressRequest {
    private String street;
    private String city;
    private String country;
    private String postalCode;
}
```

---

#### **Step 2: Use in Controller**

**Controller Code:**
```java
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

@RestController
public class PersonController {

    @PostMapping(path = "/person", consumes = "application/x-www-form-urlencoded")
    @ResponseBody
    @ResponseStatus(HttpStatus.OK)
    public String createPerson(@ModelAttribute CreatePersonRequest request) {
        return new StringBuilder()
                .append("Success create person ")
                .append(request.getFirstName()).append(" ")
                .append(request.getMiddleName()).append(" ")
                .append(request.getLastName())
                .append(" with email ").append(request.getEmail())
                .append(" and phone ").append(request.getPhone())
                .append(" with address ")
                .append(request.getAddress().getStreet()).append(", ")
                .append(request.getAddress().getCity()).append(", ")
                .append(request.getAddress().getCountry()).append(", ")
                .append(request.getAddress().getPostalCode())
                .toString();
    }
}
```

---

#### **Step 3: Sending Request**

**HTML Form Example:**
```html
<form action="/person" method="post">
    <input type="text" name="firstName" placeholder="First Name">
    <input type="text" name="middleName" placeholder="Middle Name">
    <input type="text" name="lastName" placeholder="Last Name">
    <input type="email" name="email" placeholder="Email">
    <input type="text" name="phone" placeholder="Phone">
    <input type="text" name="address.street" placeholder="Street">
    <input type="text" name="address.city" placeholder="City">
    <input type="text" name="address.country" placeholder="Country">
    <input type="text" name="address.postalCode" placeholder="Postal Code">
    <button type="submit">Submit</button>
</form>
```

**Request Example:**
```
POST /person
Content-Type: application/x-www-form-urlencoded

firstName=John&middleName=Michael&lastName=Doe&email=john.doe@example.com&phone=1234567890&
address.street=Main+St&address.city=New+York&address.country=USA&address.postalCode=10001
```

**Response:**
```
Success create person John Michael Doe with email john.doe@example.com and phone 1234567890 
with address Main St, New York, USA, 10001
```

---

### **4. Advanced Features of Nested Model Attributes**

#### **4.1. Validation with Nested Objects**

You can apply validation annotations to both the parent and nested objects.

**Validation Example:**
```java
import jakarta.validation.constraints.NotEmpty;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateAddressRequest {
    @NotEmpty(message = "Street is required")
    private String street;

    @NotEmpty(message = "City is required")
    private String city;

    @NotEmpty(message = "Country is required")
    private String country;

    @NotEmpty(message = "Postal Code is required")
    private String postalCode;
}
```

**Controller Code with Validation:**
```java
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;
import org.springframework.validation.BindingResult;

@RestController
public class PersonController {

    @PostMapping(path = "/person", consumes = "application/x-www-form-urlencoded")
    public String createPerson(@Validated @ModelAttribute CreatePersonRequest request, BindingResult result) {
        if (result.hasErrors()) {
            return "Validation failed: " + result.getAllErrors();
        }
        return "Success create person with address: " + request.getAddress().getStreet();
    }
}
```

---

#### **4.2. Default Values for Nested Objects**

You can pre-populate default values for nested objects using a method annotated with `@ModelAttribute`.

**Example:**
```java
@ModelAttribute
public CreatePersonRequest defaultPerson() {
    CreatePersonRequest person = new CreatePersonRequest();
    person.setAddress(new CreateAddressRequest("Default Street", "Default City", "Default Country", "00000"));
    return person;
}
```

---

#### **4.3. Handling Missing Nested Values**

If a nested object is missing in the request, Spring will still create an empty instance of the nested object. However, you can handle this explicitly if needed.

**Example:**
```java
@PostMapping("/person")
public String createPerson(@ModelAttribute CreatePersonRequest request) {
    if (request.getAddress() == null || request.getAddress().getStreet() == null) {
        return "Address details are missing!";
    }
    return "Success create person with address: " + request.getAddress().getStreet();
}
```

---

### **5. Testing Nested Model Attributes**

You can test nested model attributes using `MockMvc`.

**Test Code:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@SpringBootTest
@AutoConfigureMockMvc
public class PersonControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void createPerson() throws Exception {
        mockMvc.perform(post("/person")
                .contentType(MediaType.APPLICATION_FORM_URLENCODED)
                .param("firstName", "John")
                .param("middleName", "Michael")
                .param("lastName", "Doe")
                .param("email", "john.doe@example.com")
                .param("phone", "1234567890")
                .param("address.street", "Main St")
                .param("address.city", "New York")
                .param("address.country", "USA")
                .param("address.postalCode", "10001"))
                .andExpect(status().isOk())
                .andExpect(content().string("Success create person John Michael Doe with email john.doe@example.com and phone 1234567890 with address Main St, New York, USA, 10001"));
    }
}
```

---

### **6. Common Use Cases for Nested Model Attributes**

1. **Complex Form Submissions**:
   - Forms with multiple sections (e.g., personal details, address details, etc.).
2. **REST APIs**:
   - Handling hierarchical data in API requests.
3. **Validation**:
   - Validate nested objects (e.g., validating both `Person` and `Address` data).
4. **Pre-populated Defaults**:
   - Provide default values for nested objects in forms or APIs.

