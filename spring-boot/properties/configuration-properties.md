### **Introduction to Configuration Properties in Spring Boot**

Spring Boot provides a powerful feature called **Configuration Properties**, which allows you to bind values from configuration files (e.g., `application.properties` or `application.yml`) directly to Java objects (Java Beans). This makes it easier to manage and access configuration values in a structured and type-safe way.

---

### **Key Features of Configuration Properties**

1. **Automatic Binding**:
   - Keys in the `application.properties` or `application.yml` file can be automatically bound to fields in a Java Bean class.

2. **Type Safety**:
   - Configuration Properties ensure that the values are converted to the correct data types (e.g., `String`, `Integer`, `Boolean`).

3. **Ease of Use**:
   - Instead of manually fetching properties using `@Value`, you can group related properties into a single class.

4. **Scalability**:
   - Ideal for managing large configurations by grouping them into logical structures.

5. **Dependency Requirement**:
   - To use this feature, you need to include the `spring-boot-configuration-processor` dependency in your project.

---

### **Dependency for Configuration Properties**

To enable the use of `@ConfigurationProperties`, add the following dependency to your `pom.xml` (for Maven):

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

- The `optional` tag ensures that this dependency is only used during development (e.g., for annotation processing) and not included in the final build.

---

### **Creating a Configuration Properties Class**

Here’s how you can define a Java Bean class to use Configuration Properties:

#### **Example Code: Java Bean Properties**

```java
@Getter
@Setter
@ConfigurationProperties("application") // Prefix for properties in application.properties
public class ApplicationProperties {
    private String name;           // Binds to application.name
    private Integer version;       // Binds to application.version
    private boolean productionMode; // Binds to application.productionMode
}
```

---

### **Step-by-Step Explanation**

#### 1. **Annotate the Class with `@ConfigurationProperties`**:
   - The `@ConfigurationProperties` annotation is used to specify the prefix for the properties. For example:
     - If the prefix is `"application"`, the properties in the file should start with `application.` (e.g., `application.name`, `application.version`).

#### 2. **Enable Getter and Setter Methods**:
   - Use Lombok annotations like `@Getter` and `@Setter` (or manually write getter and setter methods) to allow the framework to set and retrieve values.

#### 3. **Register the Configuration Class**:
   - To make the configuration class work, you need to register it as a Spring-managed bean. You can do this by:
     - Adding `@Component` to the class.
     - Or, defining it in a `@Configuration` class with `@EnableConfigurationProperties`.

---

### **Example: Application Properties File**

Here’s an example of an `application.properties` file that corresponds to the above Java Bean:

```properties
application.name=MyApp
application.version=1
application.productionMode=true
```

When the application starts, Spring Boot will automatically bind these values to the fields in the `ApplicationProperties` class.

---

### **Accessing Configuration Properties**

Once the configuration properties are bound to the Java Bean, you can inject the bean wherever needed using `@Autowired` or constructor injection.

#### Example:

```java
@RestController
public class ConfigController {

    private final ApplicationProperties applicationProperties;

    public ConfigController(ApplicationProperties applicationProperties) {
        this.applicationProperties = applicationProperties;
    }

    @GetMapping("/config")
    public String getConfig() {
        return "App Name: " + applicationProperties.getName() +
               ", Version: " + applicationProperties.getVersion() +
               ", Production Mode: " + applicationProperties.isProductionMode();
    }
}
```

---

### **Benefits of Using Configuration Properties**

1. **Centralized Configuration**:
   - Group related properties into a single class for better organization.

2. **Type Safety**:
   - Avoid errors caused by incorrect type conversions.

3. **Reusability**:
   - Configuration classes can be reused across multiple components or services.

4. **Environment-Specific Configurations**:
   - Easily manage configurations for different environments (e.g., `dev`, `test`, `prod`) using Spring profiles.

---

### **Best Practices**

1. **Use Prefixes Wisely**:
   - Use meaningful prefixes to group related properties (e.g., `application`, `database`, `security`).

2. **Validate Properties**:
   - Use `@Validated` and validation annotations (e.g., `@NotNull`, `@Min`) to ensure that required properties are set and valid.

3. **Externalize Sensitive Data**:
   - Avoid hardcoding sensitive data (e.g., passwords) in configuration files. Use environment variables or external configuration management tools.

4. **Document Your Properties**:
   - Provide clear documentation for custom configuration properties to help other developers understand their purpose.

