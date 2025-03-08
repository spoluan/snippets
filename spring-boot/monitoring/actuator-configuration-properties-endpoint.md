### **Configuration Properties in Spring Boot Actuator**

The **Configuration Properties** feature in Spring Boot Actuator provides insights into the configuration properties used by your application. These properties are often defined in the `application.properties` or `application.yml` files and are mapped to classes annotated with `@ConfigurationProperties`. The `/actuator/configprops` endpoint allows you to monitor all the configuration properties available in your application.

---

### **What are Configuration Properties?**

1. **Definition**:
   - Configuration properties are key-value pairs that define the behavior of a Spring Boot application.
   - These properties can be externalized (e.g., in `application.properties`, `application.yml`, or environment variables).

2. **Source**:
   - Most configuration properties are mapped to classes annotated with `@ConfigurationProperties`.

3. **Purpose**:
   - To centralize and externalize configurations for easy management and customization.

4. **Actuator Monitoring**:
   - The `/actuator/configprops` endpoint provides a detailed view of all configuration properties and their default or overridden values.

---

### **Features of `/actuator/configprops` Endpoint**

1. **Lists All Configuration Properties**:
   - Displays all configuration properties mapped to `@ConfigurationProperties` classes.

2. **Shows Default and Overridden Values**:
   - Highlights overridden values (e.g., from `application.properties` or environment variables).

3. **Supports Secure Value Masking**:
   - Sensitive values (e.g., passwords) are masked with `*****` by default.

4. **Customizable**:
   - You can enable or disable the endpoint, control value visibility, and secure sensitive information.

---

### **How to Enable and Configure `/actuator/configprops`**

#### **1. Add Actuator Dependency**

Add the following dependency to your project:

**Maven:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**Gradle:**
```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

---

#### **2. Enable and Expose the Endpoint**

By default, the `/actuator/configprops` endpoint is enabled but not exposed. To expose it, update your `application.properties` or `application.yml`:

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=configprops
management.endpoint.configprops.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: configprops
  endpoint:
    configprops:
      enabled: true
```

---

#### **3. Show Configuration Values**

By default, sensitive values (e.g., passwords) are masked. To show all values, configure the following:

**For `application.properties`:**
```properties
management.endpoint.configprops.show-values=always
```

**For `application.yml`:**
```yaml
management:
  endpoint:
    configprops:
      show-values: always
```

---

### **Accessing the `/actuator/configprops` Endpoint**

Once configured, you can access the `/actuator/configprops` endpoint at:
```
http://localhost:8080/actuator/configprops
```

---

### **Sample Configurations and Responses**

#### **1. Default Configuration**

**Configuration (`application.properties`):**
```properties
management.endpoints.web.exposure.include=configprops
management.endpoint.configprops.enabled=true
```

**Response:**
```json
{
  "contexts": {
    "application": {
      "beans": {
        "dataSourceProperties": {
          "prefix": "spring.datasource",
          "properties": {
            "url": "jdbc:mysql://localhost:3306/mydb",
            "username": "root",
            "password": "*****"
          }
        },
        "serverProperties": {
          "prefix": "server",
          "properties": {
            "port": 8080,
            "address": null
          }
        }
      }
    }
  }
}
```

---

#### **2. Show All Values**

**Configuration (`application.properties`):**
```properties
management.endpoints.web.exposure.include=configprops
management.endpoint.configprops.enabled=true
management.endpoint.configprops.show-values=always
```

**Response:**
```json
{
  "contexts": {
    "application": {
      "beans": {
        "dataSourceProperties": {
          "prefix": "spring.datasource",
          "properties": {
            "url": "jdbc:mysql://localhost:3306/mydb",
            "username": "root",
            "password": "mypassword"
          }
        },
        "serverProperties": {
          "prefix": "server",
          "properties": {
            "port": 8080,
            "address": null
          }
        }
      }
    }
  }
}
```

---

#### **3. Custom Configuration Properties**

You can define custom configuration properties by creating a class annotated with `@ConfigurationProperties`.

**Example:**

**Custom Configuration Class:**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "custom")
public class CustomProperties {
    private String name;
    private String version;

    // Getters and Setters
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }
}
```

**Configuration (`application.properties`):**
```properties
custom.name=Custom Application
custom.version=1.0.0
```

**Response:**
```json
{
  "contexts": {
    "application": {
      "beans": {
        "customProperties": {
          "prefix": "custom",
          "properties": {
            "name": "Custom Application",
            "version": "1.0.0"
          }
        }
      }
    }
  }
}
```

---

### **Use Cases for `/actuator/configprops`**

1. **Debugging Configuration Issues**:
   - Identify and troubleshoot misconfigured properties.

2. **Monitoring Defaults**:
   - View default property values provided by Spring Boot or third-party libraries.

3. **Custom Property Validation**:
   - Ensure that custom properties have been correctly set.

4. **Security Auditing**:
   - Verify that sensitive properties (e.g., passwords) are masked or hidden.

---

### **Best Practices**

1. **Secure the Endpoint**:
   - Restrict access to the `/actuator/configprops` endpoint using Spring Security to prevent unauthorized access.

2. **Mask Sensitive Values**:
   - Ensure sensitive values (e.g., passwords, API keys) are masked by default.

3. **Use for Debugging**:
   - Enable the endpoint only in non-production environments for debugging purposes.

4. **Combine with Custom Properties**:
   - Leverage `@ConfigurationProperties` to externalize and monitor custom application configurations.

