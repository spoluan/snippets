### **Introduction to YAML in Spring Boot**

YAML (short for "YAML Ain't Markup Language") is a human-readable data serialization standard often used for configuration files. In Spring Boot, YAML (`.yml` or `.yaml`) is a popular alternative to traditional `.properties` files for handling externalized configuration.

YAML is hierarchical, which makes it easier to represent complex configurations like nested structures, lists, and maps. Spring Boot natively supports YAML files for configuration, and they can be used in place of or alongside `.properties` files.

---

### **Why Use YAML?**

1. **Readability**: YAML is more human-readable than properties files due to its hierarchical structure.
2. **Complex Data Structures**: YAML can represent nested structures, lists, and maps more naturally than properties files.
3. **Better Organization**: Related configurations can be grouped together visually.

---

### **Basic Syntax of YAML**

- **Key-Value Pairs**: Represented as `key: value`
- **Nested Properties**: Represented using indentation (2 spaces recommended)
- **Lists**: Represented using `-` (dash)
- **Comments**: Lines starting with `#`

##### **Example YAML Syntax**
```yaml
# Key-value pair
key: value

# Nested properties
parent:
  child: value

# List
items:
  - item1
  - item2
  - item3

# Map
mapping:
  key1: value1
  key2: value2
```

---

### **Using YAML in Spring Boot**

#### **1. Basic YAML Configuration**

Create a `application.yml` file in the `src/main/resources` directory.

##### **Example: `application.yml`**
```yaml
server:
  port: 8080
  servlet:
    context-path: /myapp

app:
  name: MyApplication
  version: 1.0.0
  description: A sample Spring Boot application using YAML.
```

##### **Accessing YAML Properties in Code**
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class AppConfig {

    @Value("${app.name}")
    private String appName;

    @Value("${app.version}")
    private String appVersion;

    @Value("${server.port}")
    private int serverPort;

    public void printConfig() {
        System.out.println("App Name: " + appName);
        System.out.println("App Version: " + appVersion);
        System.out.println("Server Port: " + serverPort);
    }
}
```

---

#### **2. Nested Properties**

YAML makes it easy to define nested properties using indentation.

##### **Example: `application.yml`**
```yaml
app:
  details:
    name: MyApplication
    version: 1.0.0
    description: This is a nested configuration example.
```

##### **Accessing Nested Properties**
```java
@Value("${app.details.name}")
private String appName;

@Value("${app.details.version}")
private String appVersion;

@Value("${app.details.description}")
private String appDescription;
```

---

#### **3. Lists in YAML**

You can define lists in YAML using the `-` (dash) symbol.

##### **Example: `application.yml`**
```yaml
app:
  servers:
    - server1.example.com
    - server2.example.com
    - server3.example.com
```

##### **Accessing Lists**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private List<String> servers;

    public List<String> getServers() {
        return servers;
    }

    public void setServers(List<String> servers) {
        this.servers = servers;
    }
}
```

---

#### **4. Maps in YAML**

You can define maps (key-value pairs) in YAML.

##### **Example: `application.yml`**
```yaml
app:
  settings:
    key1: value1
    key2: value2
    key3: value3
```

##### **Accessing Maps**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Map;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private Map<String, String> settings;

    public Map<String, String> getSettings() {
        return settings;
    }

    public void setSettings(Map<String, String> settings) {
        this.settings = settings;
    }
}
```

---

#### **5. Profile-Specific YAML Files**

Spring Boot supports profile-specific YAML files. For example:
- `application-dev.yml` for the development environment.
- `application-prod.yml` for the production environment.

##### **Example: `application-dev.yml`**
```yaml
server:
  port: 8081
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/devdb
    username: devuser
    password: devpass
```

##### **Example: `application-prod.yml`**
```yaml
server:
  port: 8080
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/proddb
    username: produser
    password: prodpass
```

##### **Activating a Profile**
Set the active profile via:
- **Command-line**: `--spring.profiles.active=dev`
- **Environment variable**: `SPRING_PROFILES_ACTIVE=prod`

---

#### **6. Combining YAML with Externalized Configuration**

You can use YAML files in externalized configurations by specifying the `spring.config.location` property.

##### **Example**
```bash
java -jar myapp.jar --spring.config.location=/path/to/config/application.yml
```

---

#### **7. Default Values with `@Value`**

You can specify default values for properties in case they are not defined in the YAML file.

##### **Example**
```java
@Value("${app.name:DefaultAppName}")
private String appName; // If app.name is not defined, "DefaultAppName" will be used.
```

---

#### **8. Complex Structures**

You can combine nested properties, lists, and maps to create complex structures.

##### **Example: `application.yml`**
```yaml
app:
  details:
    name: MyApplication
    version: 1.0.0
    description: An application with complex configurations.
  servers:
    - server1.example.com
    - server2.example.com
  settings:
    key1: value1
    key2: value2
    key3: value3
```

##### **Binding to Java Objects**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private Details details;
    private List<String> servers;
    private Map<String, String> settings;

    public static class Details {
        private String name;
        private String version;
        private String description;

        // Getters and Setters
    }

    // Getters and Setters for servers and settings
}
```

---

#### **9. Arrays in YAML**

YAML also supports arrays.

##### **Example: `application.yml`**
```yaml
app:
  colors: [red, green, blue]
```

##### **Accessing Arrays**
```java
@Value("${app.colors}")
private String[] colors;
```

---

#### **10. Overriding Properties**

You can override YAML properties using:
- **Command-line arguments**: `--app.details.name=OverriddenName`
- **Environment variables**: `APP_DETAILS_NAME=OverriddenName`

---

### **Best Practices for YAML in Spring Boot**

1. **Use Profiles for Environment-Specific Configurations**:
   - Create separate YAML files for each environment (e.g., `application-dev.yml`, `application-prod.yml`).
   - Activate profiles using `spring.profiles.active`.

2. **Organize Complex Configurations**:
   - Use YAML's hierarchical structure to group related properties together.

3. **Secure Sensitive Data**:
   - Avoid storing sensitive information (e.g., passwords) in YAML files. Use tools like Spring Cloud Config or HashiCorp Vault.

4. **Test Your Configuration**:
   - Validate your YAML configurations during development to catch syntax errors early.

---

### **Complete Example**

#### **File: `application.yml`**
```yaml
server:
  port: 8080

app:
  details:
    name: MyApplication
    version: 1.0.0
    description: A complete example of YAML configuration.

  servers:
    - server1.example.com
    - server2.example.com

  settings:
    key1: value1
    key2: value2
    key3: value3
```

#### **Binding to Java Objects**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private Details details;
    private List<String> servers;
    private Map<String, String> settings;

    public static class Details {
        private String name;
        private String version;
        private String description;

        // Getters and Setters
    }

    // Getters and Setters for servers and settings
}
```

#### **Main Application**
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class YamlExampleApplication {

    public static void main(String[] args) {
        SpringApplication.run(YamlExampleApplication.class, args);
    }

    @Bean
    CommandLineRunner run(AppConfig appConfig) {
        return args -> {
            System.out.println("App Name: " + appConfig.getDetails().getName());
            System.out.println("App Version: " + appConfig.getDetails().getVersion());
            System.out.println("App Description: " + appConfig.getDetails().getDescription());
            System.out.println("App Servers: " + appConfig.getServers());
            System.out.println("App Settings: " + appConfig.getSettings());
        };
    }
}
```

#### **Output**
```
App Name: MyApplication
App Version: 1.0.0
App Description: A complete example of YAML configuration.
App Servers: [server1.example.com, server2.example.com]
App Settings: {key1=value1, key2=value2, key3=value3}
```
