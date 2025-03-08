### **Understanding List in Nested Attributes**

In Spring, you can use `List` as part of your model attributes to handle multiple values for a single field or to bind a collection of objects. This feature is especially useful when dealing with forms or requests that include repeated fields (e.g., hobbies, social media accounts, etc.).

---

### **1. What is a List in Nested Attributes?**

- A **List in nested attributes** allows you to bind multiple values or objects to a single field in your model.
- **Example Use Cases**:
  - Binding a list of primitive values like `List<String>` (e.g., hobbies, tags, etc.).
  - Binding a list of objects like `List<SocialMedia>` (e.g., a list of social media accounts).

---

### **2. How Does it Work?**

- Spring uses **index-based notation** to bind request parameters to `List` fields in the model.
- For primitive types:
  - `listName[0]=value1`
  - `listName[1]=value2`
  - ...
- For object types:
  - `listName[0].field1=value1`
  - `listName[0].field2=value2`
  - `listName[1].field1=value3`
  - ...

---

### **3. Example: List in Nested Attributes**

#### **Step 1: Define the Model Classes**

**Social Media Class:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateSocialMediaRequest {
    private String name;
    private String location;
}
```

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
    private CreateAddressRequest address; // Nested object
    private List<String> hobbies; // List of primitive values
    private List<CreateSocialMediaRequest> socialMedias; // List of objects
}
```

---

#### **Step 2: Controller Code**

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
        StringBuilder response = new StringBuilder()
                .append("Success create person ")
                .append(request.getFirstName()).append(" ")
                .append(request.getMiddleName()).append(" ")
                .append(request.getLastName())
                .append(" with email ").append(request.getEmail())
                .append(" and phone ").append(request.getPhone())
                .append(" with address ").append(request.getAddress().getStreet())
                .append(", ").append(request.getAddress().getCity())
                .append(", ").append(request.getAddress().getCountry())
                .append(", ").append(request.getAddress().getPostalCode());

        response.append(" and hobbies: ");
        for (String hobby : request.getHobbies()) {
            response.append(hobby).append(", ");
        }

        response.append(" and social media accounts: ");
        for (CreateSocialMediaRequest socialMedia : request.getSocialMedias()) {
            response.append(socialMedia.getName()).append(" (")
                    .append(socialMedia.getLocation()).append("), ");
        }

        return response.toString();
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
    <input type="text" name="hobbies[0]" placeholder="Hobby 1">
    <input type="text" name="hobbies[1]" placeholder="Hobby 2">
    <input type="text" name="socialMedias[0].name" placeholder="Social Media 1 Name">
    <input type="text" name="socialMedias[0].location" placeholder="Social Media 1 Location">
    <input type="text" name="socialMedias[1].name" placeholder="Social Media 2 Name">
    <input type="text" name="socialMedias[1].location" placeholder="Social Media 2 Location">
    <button type="submit">Submit</button>
</form>
```

**Request Example:**
```
POST /person
Content-Type: application/x-www-form-urlencoded

firstName=John&middleName=Michael&lastName=Doe&email=john.doe@example.com&phone=1234567890&
address.street=Main+St&address.city=New+York&address.country=USA&address.postalCode=10001&
hobbies[0]=Coding&hobbies[1]=Reading&
socialMedias[0].name=Facebook&socialMedias[0].location=facebook.com/johndoe&
socialMedias[1].name=Twitter&socialMedias[1].location=twitter.com/johndoe
```

**Response:**
```
Success create person John Michael Doe with email john.doe@example.com and phone 1234567890 
with address Main St, New York, USA, 10001 and hobbies: Coding, Reading, 
and social media accounts: Facebook (facebook.com/johndoe), Twitter (twitter.com/johndoe),
```

---

### **4. Advanced Features**

#### **4.1. Validation**

You can validate the `List` fields using annotations like `@NotEmpty` or custom validators.

**Example:**
```java
import jakarta.validation.constraints.NotEmpty;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreatePersonRequest {
    @NotEmpty(message = "First name is required")
    private String firstName;

    private List<@NotEmpty(message = "Hobbies cannot be empty") String> hobbies;

    private List<@Valid CreateSocialMediaRequest> socialMedias;
}
```

---

#### **4.2. Default Values for Lists**

You can set default values for `List` fields in a method annotated with `@ModelAttribute`.

**Example:**
```java
@ModelAttribute
public CreatePersonRequest defaultPerson() {
    CreatePersonRequest person = new CreatePersonRequest();
    person.setHobbies(Arrays.asList("Default Hobby 1", "Default Hobby 2"));
    person.setSocialMedias(Arrays.asList(new CreateSocialMediaRequest("Default SM", "default.com")));
    return person;
}
```

---

#### **4.3. Handling Missing List Values**

If a list is missing in the request, Spring initializes it as an empty list. You can handle this explicitly if needed.

**Example:**
```java
@PostMapping("/person")
public String createPerson(@ModelAttribute CreatePersonRequest request) {
    if (request.getHobbies() == null || request.getHobbies().isEmpty()) {
        return "Hobbies are missing!";
    }
    return "Success!";
}
```

---

### **5. Testing List in Nested Attributes**

You can test the functionality using `MockMvc`.

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
                .param("address.postalCode", "10001")
                .param("hobbies[0]", "Coding")
                .param("hobbies[1]", "Reading")
                .param("socialMedias[0].name", "Facebook")
                .param("socialMedias[0].location", "facebook.com/johndoe")
                .param("socialMedias[1].name", "Twitter")
                .param("socialMedias[1].location", "twitter.com/johndoe"))
                .andExpect(status().isOk())
                .andExpect(content().string("Success create person John Michael Doe with email john.doe@example.com and phone 1234567890 with address Main St, New York, USA, 10001 and hobbies: Coding, Reading, and social media accounts: Facebook (facebook.com/johndoe), Twitter (twitter.com/johndoe),"));
    }
}
```
