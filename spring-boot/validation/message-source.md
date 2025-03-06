### **Introduction to MessageSource in Spring Boot**

In Spring Boot, `MessageSource` is used for internationalization (i18n) and localization. It allows you to manage messages (e.g., error messages, validation messages, or general application messages) in different languages using externalized properties files. This is particularly useful for applications that need to support multiple languages or custom error messages for Java Bean validation.

---

### **Key Features of `MessageSource`**

1. **Internationalization (i18n)**:
   - Supports multiple languages by loading messages from language-specific property files.
   - Automatically resolves messages based on the user's locale.

2. **Centralized Message Management**:
   - All messages (e.g., validation errors, application messages) can be managed in external `.properties` files.

3. **Integration with Java Validation**:
   - Provides custom validation messages for `javax.validation` annotations like `@NotNull`, `@Size`, etc.

4. **Dynamic Locale Switching**:
   - Enables dynamic switching of locales at runtime.

---

### **How `MessageSource` Works**

1. **Message Files**: Messages are stored in `.properties` files, typically named as `messages_<locale>.properties` (e.g., `messages_en.properties`, `messages_fr.properties`).
2. **Locale**: The locale determines which message file is loaded.
3. **MessageSource Bean**: Spring Boot automatically provides a `MessageSource` bean if the `messages.properties` file is present in the classpath.

---

### **Steps to Configure and Use `MessageSource`**

#### **1. Add Message Files**

Create message files for different locales in the `src/main/resources` directory.

##### **Example: `messages.properties` (default)**
```properties
welcome.message=Welcome to the application!
error.notfound=Resource not found.
```

##### **Example: `messages_en.properties` (English)**
```properties
welcome.message=Welcome to the application!
error.notfound=Resource not found.
```

##### **Example: `messages_fr.properties` (French)**
```properties
welcome.message=Bienvenue dans l'application!
error.notfound=Ressource non trouvée.
```

---

#### **2. Configure a Custom `MessageSource` Bean (Optional)**

By default, Spring Boot will look for a `messages.properties` file. If you want to customize the location or behavior of `MessageSource`, you can define a custom bean.

##### **Example: Custom `MessageSource` Bean**
```java
import org.springframework.context.MessageSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ResourceBundleMessageSource;

@Configuration
public class MessageSourceConfig {

    @Bean
    public MessageSource messageSource() {
        ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
        messageSource.setBasename("messages"); // Base name of the messages file
        messageSource.setDefaultEncoding("UTF-8");
        messageSource.setUseCodeAsDefaultMessage(true); // Fallback to message code if not found
        return messageSource;
    }
}
```

---

#### **3. Access Messages Programmatically**

You can access messages using the `MessageSource` bean and specify the desired locale.

##### **Example: Accessing Messages**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.stereotype.Service;

import java.util.Locale;

@Service
public class MessageService {

    @Autowired
    private MessageSource messageSource;

    public String getMessage(String code, Object[] args, Locale locale) {
        return messageSource.getMessage(code, args, locale);
    }
}
```

##### **Usage**
```java
Locale locale = Locale.FRENCH;
String message = messageService.getMessage("welcome.message", null, locale);
System.out.println(message); // Output: Bienvenue dans l'application!
```

---

#### **4. Set Locale Dynamically**

To dynamically switch locales, you can use `LocaleResolver`.

##### **Example: Configure `LocaleResolver`**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.SessionLocaleResolver;

import java.util.Locale;

@Configuration
public class LocaleConfig {

    @Bean
    public LocaleResolver localeResolver() {
        SessionLocaleResolver localeResolver = new SessionLocaleResolver();
        localeResolver.setDefaultLocale(Locale.ENGLISH); // Default locale
        return localeResolver;
    }
}
```

##### **Controller Example**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.Locale;

@RestController
public class MessageController {

    @Autowired
    private MessageSource messageSource;

    @GetMapping("/message")
    public String getMessage(@RequestParam String code, @RequestParam String lang) {
        Locale locale = new Locale(lang);
        return messageSource.getMessage(code, null, locale);
    }
}
```

##### **Request Example**
```bash
GET /message?code=welcome.message&lang=fr
Response: Bienvenue dans l'application!
```

---

#### **5. Integrating with Java Validation**

Spring Boot’s validation framework (`javax.validation`) can use `MessageSource` for custom validation messages.

##### **Example: Validation Messages**

Create a `ValidationMessages.properties` file for validation messages.

##### **File: `ValidationMessages.properties`**
```properties
NotNull.user.name=Name cannot be null!
Size.user.name=Name must be between {min} and {max} characters!
```

##### **Entity Example**
```java
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;

public class User {

    @NotNull(message = "{NotNull.user.name}")
    @Size(min = 2, max = 30, message = "{Size.user.name}")
    private String name;

    // Getters and Setters
}
```

##### **Controller Example**
```java
import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public ResponseEntity<String> createUser(@Valid @RequestBody User user) {
        return ResponseEntity.ok("User is valid!");
    }
}
```

##### **Error Response Example**
If the `name` field is null:
```json
{
  "errors": [
    "Name cannot be null!"
  ]
}
```

---

#### **6. Using Placeholders in Messages**

You can include placeholders in your messages and pass dynamic values.

##### **Example: `messages.properties`**
```properties
greeting.message=Hello, {0}! Welcome to {1}.
```

##### **Accessing Messages with Placeholders**
```java
String message = messageSource.getMessage("greeting.message", new Object[]{"John", "Spring Boot"}, Locale.ENGLISH);
System.out.println(message); // Output: Hello, John! Welcome to Spring Boot.
```

---

### **Complete Example: MessageSource with Validation**

#### **1. Message Files**

**`messages.properties`**
```properties
welcome.message=Welcome to the application!
```

**`ValidationMessages.properties`**
```properties
NotNull.user.name=Name cannot be null!
Size.user.name=Name must be between {min} and {max} characters!
```

---

#### **2. Entity**
```java
import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Size;

public class User {

    @NotNull(message = "{NotNull.user.name}")
    @Size(min = 2, max = 30, message = "{Size.user.name}")
    private String name;

    // Getters and Setters
}
```

---

#### **3. Controller**
```java
import jakarta.validation.Valid;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/users")
public class UserController {

    @PostMapping
    public ResponseEntity<String> createUser(@Valid @RequestBody User user) {
        return ResponseEntity.ok("User is valid!");
    }
}
```

---

#### **4. Locale Configuration**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.SessionLocaleResolver;

import java.util.Locale;

@Configuration
public class LocaleConfig {

    @Bean
    public LocaleResolver localeResolver() {
        SessionLocaleResolver localeResolver = new SessionLocaleResolver();
        localeResolver.setDefaultLocale(Locale.ENGLISH);
        return localeResolver;
    }
}
```

---

#### **5. Testing**

- **Request**: POST `/users`
```json
{
  "name": ""
}
```

- **Response**:
```json
{
  "errors": [
    "Name must be between 2 and 30 characters!"
  ]
}
```

