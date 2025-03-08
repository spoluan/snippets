### **Actuator `/env` Endpoint**

The `/actuator/env` endpoint in Spring Boot Actuator provides detailed information about the environment in which the application is running. This includes environment variables, system properties, application properties, and configuration values from different property sources.

---

### **What is the `/env` Endpoint?**

1. **Purpose**:
   - To expose information about the application's environment, including:
     - Environment variables from the operating system.
     - System properties (e.g., Java runtime properties).
     - Application-specific properties (e.g., `application.properties` or `application.yml` values).
     - Custom property sources (e.g., cloud configurations).

2. **Use Cases**:
   - Debugging configuration issues.
   - Verifying environment variable values.
   - Monitoring application-specific properties and their sources.

3. **Access**:
   - The endpoint is accessed at `/actuator/env`.

---

### **Features of `/env` Endpoint**

1. **Environment Variables**:
   - Displays all environment variables available to the application.

2. **System Properties**:
   - Lists Java system properties such as `java.version`, `java.home`, etc.

3. **Application Properties**:
   - Shows application-specific properties defined in `application.properties` or `application.yml`.

4. **Property Sources**:
   - Displays the origin of each property (e.g., `systemProperties`, `applicationConfig`, `environmentVariables`).

5. **Secure Value Masking**:
   - Sensitive information (e.g., passwords) is masked by default.

---

### **How to Enable and Configure `/env`**

#### **1. Add Actuator Dependency**

Include the Spring Boot Actuator dependency in your project.

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

#### **2. Enable and Expose the `/env` Endpoint**

By default, the `/actuator/env` endpoint is enabled but not exposed. To expose it, configure your `application.properties` or `application.yml`:

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=env
management.endpoint.env.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: env
  endpoint:
    env:
      enabled: true
```

---

#### **3. Show All Values (Optional)**

By default, sensitive values (e.g., passwords) are masked. To show all values, configure the following:

**For `application.properties`:**
```properties
management.endpoint.env.show-values=always
```

**For `application.yml`:**
```yaml
management:
  endpoint:
    env:
      show-values: always
```

---

### **Accessing the `/env` Endpoint**

Once configured, you can access the `/actuator/env` endpoint at:
```
http://localhost:8080/actuator/env
```

---

### **Sample Configurations and Responses**

#### **1. Default Configuration**

**Configuration (`application.properties`):**
```properties
management.endpoints.web.exposure.include=env
management.endpoint.env.enabled=true
```

**Response:**
```json
{
  "activeProfiles": [],
  "propertySources": [
    {
      "name": "systemProperties",
      "properties": {
        "java.version": {
          "value": "17"
        },
        "user.timezone": {
          "value": "UTC"
        }
      }
    },
    {
      "name": "systemEnvironment",
      "properties": {
        "PATH": {
          "value": "/usr/local/bin:/usr/bin:/bin"
        },
        "JAVA_HOME": {
          "value": "/usr/lib/jvm/java-17"
        }
      }
    },
    {
      "name": "applicationConfig: [classpath:/application.properties]",
      "properties": {
        "server.port": {
          "value": "8080"
        },
        "spring.datasource.url": {
          "value": "jdbc:mysql://localhost:3306/mydb"
        }
      }
    }
  ]
}
```

---

#### **2. Show All Values**

**Configuration (`application.properties`):**
```properties
management.endpoints.web.exposure.include=env
management.endpoint.env.enabled=true
management.endpoint.env.show-values=always
```

**Response:**
```json
{
  "activeProfiles": [],
  "propertySources": [
    {
      "name": "systemProperties",
      "properties": {
        "java.version": {
          "value": "17"
        },
        "user.timezone": {
          "value": "UTC"
        }
      }
    },
    {
      "name": "systemEnvironment",
      "properties": {
        "PATH": {
          "value": "/usr/local/bin:/usr/bin:/bin"
        },
        "JAVA_HOME": {
          "value": "/usr/lib/jvm/java-17"
        }
      }
    },
    {
      "name": "applicationConfig: [classpath:/application.properties]",
      "properties": {
        "server.port": {
          "value": "8080"
        },
        "spring.datasource.url": {
          "value": "jdbc:mysql://localhost:3306/mydb"
        },
        "spring.datasource.username": {
          "value": "root"
        },
        "spring.datasource.password": {
          "value": "mypassword"
        }
      }
    }
  ]
}
```

---

#### **3. Custom Environment Variables**

You can define and access custom environment variables.

**Example:**

**Custom Environment Variable (`application.properties`):**
```properties
custom.env.name=My Application
custom.env.version=1.0.0
```

**Response:**
```json
{
  "activeProfiles": [],
  "propertySources": [
    {
      "name": "applicationConfig: [classpath:/application.properties]",
      "properties": {
        "custom.env.name": {
          "value": "My Application"
        },
        "custom.env.version": {
          "value": "1.0.0"
        }
      }
    }
  ]
}
```

---

#### **4. Access Specific Environment Variables**

You can access a specific environment variable by appending its name to the `/env` endpoint.

**Example:**
```
http://localhost:8080/actuator/env/custom.env.name
```

**Response:**
```json
{
  "property": {
    "source": "applicationConfig: [classpath:/application.properties]",
    "value": "My Application"
  }
}
```

---

### **Use Cases for `/actuator/env`**

1. **Debugging Environment Issues**:
   - Identify incorrect or missing environment variables.
   - Verify that system properties and application properties are correctly loaded.

2. **Monitoring Configuration Sources**:
   - Understand where each property is coming from (e.g., system environment, application properties, or custom property sources).

3. **Custom Property Validation**:
   - Ensure custom environment variables are correctly set and accessible.

4. **Integration with External Systems**:
   - Verify environment variables used for external integrations (e.g., database URLs, API keys).

---

### **Best Practices**

1. **Secure the Endpoint**:
   - Restrict access to the `/actuator/env` endpoint using Spring Security to prevent unauthorized access.

2. **Mask Sensitive Values**:
   - Ensure sensitive values (e.g., passwords, API keys) are masked by default.

3. **Enable in Non-Production Environments**:
   - Use the `/env` endpoint primarily in development or staging environments for debugging purposes.

4. **Combine with Custom Properties**:
   - Leverage custom environment variables to externalize configuration and monitor them using `/actuator/env`.
