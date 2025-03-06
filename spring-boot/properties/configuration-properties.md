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

  
### **Metadata Generation for Configuration Properties**

When you compile your project with Maven (or any build tool that supports annotation processing), the `spring-boot-configuration-processor` generates a file called `spring-configuration-metadata.json`. This file contains metadata about your custom configuration properties, including their names, types, and descriptions (if provided).

#### **Steps to Generate Metadata**

1. Ensure the `spring-boot-configuration-processor` dependency is included in your `pom.xml`:

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-configuration-processor</artifactId>
       <optional>true</optional>
   </dependency>
   ```

2. Annotate your configuration properties class with `@ConfigurationProperties`.

   Example:

   ```java
   @Getter
   @Setter
   @ConfigurationProperties("application")
   public class ApplicationProperties {
       private String name;           // Binds to application.name
       private Integer version;       // Binds to application.version
       private boolean productionMode; // Binds to application.productionMode
   }
   ```

3. Build the project using Maven:

   ```bash
   mvn clean install
   ```

4. During the build process, the annotation processor generates the `spring-configuration-metadata.json` file in the `META-INF` directory of the compiled JAR.

---

### **Example Output: `spring-configuration-metadata.json`**

After running the build, the following file will be generated in the `target/classes/META-INF` directory:

```json
{
  "groups": [
    {
      "name": "application",
      "type": "com.example.ApplicationProperties",
      "sourceType": "com.example.ApplicationProperties"
    }
  ],
  "properties": [
    {
      "name": "application.name",
      "type": "java.lang.String",
      "description": "Name of the application.",
      "sourceType": "com.example.ApplicationProperties"
    },
    {
      "name": "application.version",
      "type": "java.lang.Integer",
      "description": "Version of the application.",
      "sourceType": "com.example.ApplicationProperties"
    },
    {
      "name": "application.production-mode",
      "type": "java.lang.Boolean",
      "description": "Whether the application is in production mode.",
      "sourceType": "com.example.ApplicationProperties"
    }
  ]
}
```

---

### **Full Maven Build Output**

Here’s what you might see when you build the project with Maven and the `spring-boot-configuration-processor` dependency included:

```bash
[INFO] Scanning for projects...
[INFO] 
[INFO] ----------------< com.example:configuration-demo >----------------
[INFO] Building configuration-demo 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO] 
[INFO] --- maven-clean-plugin:3.1.0:clean (default-clean) @ configuration-demo ---
[INFO] Deleting /path/to/project/target
[INFO] 
[INFO] --- maven-resources-plugin:3.2.0:resources (default-resources) @ configuration-demo ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ configuration-demo ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 3 source files to /path/to/project/target/classes
[INFO] 
[INFO] --- spring-boot-configuration-processor:2.7.5:process (process-configuration-metadata) @ configuration-demo ---
[INFO] Processing additional configuration metadata...
[INFO] Writing metadata to '/path/to/project/target/classes/META-INF/spring-configuration-metadata.json'
[INFO] 
[INFO] --- maven-resources-plugin:3.2.0:testResources (default-testResources) @ configuration-demo ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource
[INFO] 
[INFO] --- maven-compiler-plugin:3.8.1:testCompile (default-testCompile) @ configuration-demo ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to /path/to/project/target/test-classes
[INFO] 
[INFO] --- maven-surefire-plugin:2.22.2:test (default-test) @ configuration-demo ---
[INFO] Tests are skipped.
[INFO] 
[INFO] --- maven-jar-plugin:3.2.0:jar (default-jar) @ configuration-demo ---
[INFO] Building jar: /path/to/project/target/configuration-demo-1.0-SNAPSHOT.jar
[INFO] 
[INFO] --- spring-boot-maven-plugin:2.7.5:repackage (repackage) @ configuration-demo ---
[INFO] Replacing main artifact with repackaged archive
[INFO] 
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ configuration-demo ---
[INFO] Installing /path/to/project/target/configuration-demo-1.0-SNAPSHOT.jar to /path/to/.m2/repository/com/example/configuration-demo/1.0-SNAPSHOT/configuration-demo-1.0-SNAPSHOT.jar
[INFO] Installing /path/to/project/pom.xml to /path/to/.m2/repository/com/example/configuration-demo/1.0-SNAPSHOT/configuration-demo-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  4.876 s
[INFO] Finished at: 2025-03-06T11:30:45+00:00
[INFO] ------------------------------------------------------------------------
```

---

### **Benefits of Metadata Generation**

1. **IDE Autocompletion**:
   - IDEs like IntelliJ or Eclipse can use the metadata to provide autocompletion for your custom properties in `application.properties` or `application.yml`.

2. **Improved Documentation**:
   - You can add descriptions to your configuration properties using the `@Description` annotation or Javadoc comments. These descriptions will appear in the metadata file and be visible in your IDE.

   Example:

   ```java
   @Getter
   @Setter
   @ConfigurationProperties("application")
   public class ApplicationProperties {
       /**
        * Name of the application.
        */
       private String name;

       /**
        * Version of the application.
        */
       private Integer version;

       /**
        * Whether the application is in production mode.
        */
       private boolean productionMode;
   }
   ```
 
### **Introduction to `@ConfigurationProperties` and Why It Needs to Be Enabled**

In Spring Boot, the `@ConfigurationProperties` annotation is used to map external configuration (like properties from `application.properties` or `application.yml`) to a Java class. This makes it easier to manage and access application settings in a type-safe way.

However, **`@ConfigurationProperties` alone does not work by itself**. For it to function, you must explicitly enable it. This can be done in one of two ways:
1. By using the `@EnableConfigurationProperties` annotation.
2. By annotating the class with `@Component` (or another Spring stereotype annotation).

The recommended way is to use `@EnableConfigurationProperties`, as it provides better modularity and separation of concerns.

---

### **Why Do We Need to Enable `@ConfigurationProperties`?**

The `@ConfigurationProperties` annotation is just a marker that tells Spring Boot to bind external properties to the fields of the class. But Spring Boot needs to know **how to manage this class as a bean**. Without enabling it explicitly, Spring won't recognize it as a bean, and the binding won't happen.

This is why you must either:
- Use `@EnableConfigurationProperties` to explicitly register the class as a bean.
- Or annotate the class with `@Component` to let Spring Boot automatically detect it.

---

### **Step-by-Step Guide with Examples**

#### **1. Using `@EnableConfigurationProperties` (Recommended Approach)**

This approach explicitly registers the `@ConfigurationProperties` class as a Spring bean, keeping your configuration classes focused on property binding.

---

**Step 1**: Create a Configuration Properties Class

Define a class annotated with `@ConfigurationProperties`. Use the `prefix` attribute to specify the namespace for the properties.

```java
import org.springframework.boot.context.properties.ConfigurationProperties;

@ConfigurationProperties(prefix = "application")
public class ApplicationProperties {
    private String name;
    private Integer version;
    private boolean productionMode;

    // Getters and setters
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getVersion() {
        return version;
    }

    public void setVersion(Integer version) {
        this.version = version;
    }

    public boolean isProductionMode() {
        return productionMode;
    }

    public void setProductionMode(boolean productionMode) {
        this.productionMode = productionMode;
    }
}
```

---

**Step 2**: Enable the Configuration Properties Class

In your main application class (or any configuration class), use the `@EnableConfigurationProperties` annotation to register the `ApplicationProperties` class as a bean.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;

@SpringBootApplication
@EnableConfigurationProperties(ApplicationProperties.class)
public class TestApplication {
    public static void main(String[] args) {
        SpringApplication.run(TestApplication.class, args);
    }
}
```

---

**Step 3**: Define Properties in `application.properties` or `application.yml`

Add the properties that match the fields in your `ApplicationProperties` class. Use the prefix you defined in the `@ConfigurationProperties` annotation.

Example (`application.properties`):

```properties
application.name=MyApp
application.version=1
application.production-mode=true
```

---

**Step 4**: Use the Configuration Properties Bean

You can now inject the `ApplicationProperties` bean into any Spring-managed component and access the bound properties.

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

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

#### **2. Using `@Component` (Alternative Approach)**

If you don't want to use `@EnableConfigurationProperties`, you can annotate the `@ConfigurationProperties` class with `@Component`. This will automatically register the class as a Spring bean.

---

**Step 1**: Modify the Configuration Properties Class

Add the `@Component` annotation to the class.

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "application")
public class ApplicationProperties {
    private String name;
    private Integer version;
    private boolean productionMode;

    // Getters and setters (same as above)
}
```

---

**Step 2**: Define Properties in `application.properties` or `application.yml`

Add the properties as before.

Example (`application.properties`):

```properties
application.name=MyApp
application.version=1
application.production-mode=true
```

---

**Step 3**: Use the Configuration Properties Bean

Inject the `ApplicationProperties` bean and use it as before.

```java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

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

### **Which Approach Should You Use?**

- **Use `@EnableConfigurationProperties` (Recommended)**:
  - Keeps the configuration class focused only on property binding.
  - Provides better separation of concerns and modularity.
  - Makes it explicit which classes are used for configuration.

- **Use `@Component` (Alternative)**:
  - Simpler for small applications or quick setups.
  - May mix concerns, as the class becomes both a configuration class and a Spring component.
