
### **Introduction to Properties Files in Spring Boot**

Spring Boot uses **properties files** to configure application settings, making it easy to externalize and manage environment-specific configurations. These files are typically located in the `resources` directory and follow the naming convention `application-{profile}.properties`.

---

### **Types of Properties Files**

1. **`application.properties`**:
   - The default configuration file loaded by Spring Boot.
   - Used when no specific profile is activated.
   - Contains general settings that apply to all environments.

2. **`application-production.properties`**:
   - A profile-specific properties file for the `production` environment.
   - Loaded when the `production` profile is active.

3. **`application-test.properties`**:
   - A profile-specific properties file for the `test` environment.
   - Loaded when the `test` profile is active.

4. **`messages.properties`**:
   - Typically used for internationalization (i18n) to store text messages or labels.
   - Allows localization by creating language-specific versions (e.g., `messages_en.properties`, `messages_fr.properties`).

---

### **How Spring Boot Loads Properties Files**

1. **Default Behavior**:
   - Spring Boot automatically loads the `application.properties` file.

2. **Profile-Specific Properties**:
   - When a specific profile is active (e.g., `production` or `test`), Spring Boot loads the corresponding profile-specific file (e.g., `application-production.properties` or `application-test.properties`).
   - Profile-specific files override the default properties in `application.properties`.

3. **Priority Order**:
   - Spring Boot merges properties from all applicable files, with profile-specific properties taking precedence over default ones.

---

### **Activating Profiles**

Profiles can be activated in multiple ways:

1. **In `application.properties`**:
   ```properties
   spring.profiles.active=production
   ```

2. **Using Environment Variables**:
   ```bash
   export SPRING_PROFILES_ACTIVE=test
   ```

3. **Using JVM Arguments**:
   ```bash
   java -Dspring.profiles.active=production -jar your-app.jar
   ```

---

### **Example Properties Files**

- **`application.properties`**:
  ```properties
  profile.default=default-file.txt
  ```

- **`application-production.properties`**:
  ```properties
  profile.production=production-file.txt
  ```

- **`application-test.properties`**:
  ```properties
  profile.test=test-file.txt
  ```

---

### **How the Code Works**

The following code dynamically loads properties based on the active profile:

```java
@SpringBootApplication
public static class TestApplication {

    @Component
    @Getter
    public static class ProfileProperties {

        @Value("${profile.default}")
        private String defaultFile;

        @Value("${profile.production}")
        private String productionFile;

        @Value("${profile.test}")
        private String testFile;
    }
}
```

#### **Explanation of the Code**

1. **`@Value` Annotation**:
   - Injects property values from the properties files into the fields.
   - `${property.name}` syntax is used to reference property keys.

2. **Fields**:
   - `defaultFile`: Holds the value of the `profile.default` property from `application.properties`.
   - `productionFile`: Holds the value of the `profile.production` property from `application-production.properties`.
   - `testFile`: Holds the value of the `profile.test` property from `application-test.properties`.

3. **Dynamic Behavior**:
   - When a specific profile is active, the corresponding property file is loaded, and the respective values are injected.

---

### **Example Scenarios**

#### **Scenario 1: Default Profile**
- No profile is explicitly set.
- Properties loaded:
  - From `application.properties`.
- Values:
  ```java
  defaultFile = "default-file.txt";
  productionFile = null;
  testFile = null;
  ```

#### **Scenario 2: Production Profile**
- Active profile: `production`.
- Properties loaded:
  - From `application.properties`.
  - Overridden by `application-production.properties`.
- Values:
  ```java
  defaultFile = "default-file.txt"; // From application.properties
  productionFile = "production-file.txt"; // From application-production.properties
  testFile = null;
  ```

#### **Scenario 3: Test Profile**
- Active profile: `test`.
- Properties loaded:
  - From `application.properties`.
  - Overridden by `application-test.properties`.
- Values:
  ```java
  defaultFile = "default-file.txt"; // From application.properties
  productionFile = null;
  testFile = "test-file.txt"; // From application-test.properties
  ```

---

### **Best Practices**

1. **Use Profile-Specific Files**:
   - Separate configurations for environments (e.g., `dev`, `test`, `prod`) into different files.

2. **Fallback to Defaults**:
   - Provide default configurations in `application.properties`.

3. **Secure Sensitive Data**:
   - Avoid storing sensitive information (e.g., database credentials) in properties files. Use Spring Boot's **encrypted configuration** or externalized configuration.

4. **Log Active Profiles**:
   - Log the active profile during startup for debugging purposes:
     ```java
     @PostConstruct
     public void logActiveProfiles() {
         System.out.println("Active Profiles: " + Arrays.toString(environment.getActiveProfiles()));
     }
     ```

5. **Externalize Configurations**:
   - Use externalized properties files or environment variables for deployment flexibility.

