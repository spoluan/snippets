### **Actuator `/loggers` Endpoint**

The `/actuator/loggers` endpoint in Spring Boot Actuator provides detailed information about the logging configuration of your application. It allows you to view and dynamically change the logging levels of different loggers in your application.

---

### **What is the `/loggers` Endpoint?**

1. **Purpose**:
   - To expose information about the application’s logging configuration.
   - To allow runtime inspection of loggers and their respective logging levels.
   - To dynamically update logging levels without restarting the application.

2. **Use Cases**:
   - Debugging by increasing the logging level for specific packages/classes.
   - Monitoring the current logging levels of various components.
   - Reducing noise in logs by lowering the logging level.

3. **Access**:
   - The endpoint is accessed at `/actuator/loggers`.

---

### **Features of `/loggers` Endpoint**

1. **View Logger Information**:
   - Lists all loggers in the application.
   - Displays the `effectiveLevel` (current logging level) of each logger.

2. **Dynamic Logger Configuration**:
   - Allows changing the logging level of specific loggers at runtime via a `POST` request.

3. **Hierarchy of Loggers**:
   - Loggers are organized hierarchically, e.g., `com.example` is a parent of `com.example.service`.

4. **Logging Levels**:
   - Supported levels are: `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`, and `OFF`.

---

### **How to Enable and Configure `/loggers`**

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

#### **2. Enable and Expose the `/loggers` Endpoint**

By default, the `/loggers` endpoint is enabled but not exposed. To expose it, configure your `application.properties` or `application.yml`:

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=loggers
management.endpoint.loggers.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: loggers
  endpoint:
    loggers:
      enabled: true
```

---

### **Accessing the `/loggers` Endpoint**

Once configured, you can access the `/actuator/loggers` endpoint at:
```
http://localhost:8080/actuator/loggers
```

---

### **Sample Configurations and Responses**

#### **1. View All Loggers**

**Request:**
```
GET http://localhost:8080/actuator/loggers
```

**Response:**
```json
{
  "levels": ["OFF", "ERROR", "WARN", "INFO", "DEBUG", "TRACE"],
  "loggers": {
    "ROOT": {
      "configuredLevel": "INFO",
      "effectiveLevel": "INFO"
    },
    "com.example": {
      "configuredLevel": null,
      "effectiveLevel": "INFO"
    },
    "com.example.service": {
      "configuredLevel": "DEBUG",
      "effectiveLevel": "DEBUG"
    },
    "com.zaxxer.hikari": {
      "configuredLevel": null,
      "effectiveLevel": "INFO"
    }
  }
}
```

- **`configuredLevel`**: The explicitly set level for the logger.
- **`effectiveLevel`**: The actual level being used by the logger (inherited if not explicitly set).

---

#### **2. View a Specific Logger**

You can view the details of a single logger by appending its name to the `/loggers` endpoint.

**Request:**
```
GET http://localhost:8080/actuator/loggers/com.example.service
```

**Response:**
```json
{
  "configuredLevel": "DEBUG",
  "effectiveLevel": "DEBUG"
}
```

---

#### **3. Change Logger Level Dynamically**

You can update the logging level of a specific logger at runtime using a `POST` request.

**Request:**
```
POST http://localhost:8080/actuator/loggers/com.example.service
```

**Request Body:**
```json
{
  "configuredLevel": "TRACE"
}
```

**Response:**
```json
{
  "configuredLevel": "TRACE",
  "effectiveLevel": "TRACE"
}
```

- The logger `com.example.service` will now log at the `TRACE` level.

---

#### **4. Reset Logger Level**

To reset the logging level of a logger (remove the explicitly set level), send a `POST` request with `null` as the `configuredLevel`.

**Request:**
```
POST http://localhost:8080/actuator/loggers/com.example.service
```

**Request Body:**
```json
{
  "configuredLevel": null
}
```

**Response:**
```json
{
  "configuredLevel": null,
  "effectiveLevel": "INFO"
}
```

- The logger `com.example.service` will now inherit its logging level from its parent logger.

---

### **Use Cases for `/loggers`**

1. **Debugging Specific Components**:
   - Temporarily increase the logging level of specific packages or classes to `DEBUG` or `TRACE` to troubleshoot issues.

2. **Reducing Log Noise**:
   - Lower the logging level of noisy components (e.g., third-party libraries) to `WARN` or `ERROR`.

3. **Monitoring Logging Configuration**:
   - Verify the current logging levels of various components in the application.

4. **Dynamic Log Management**:
   - Change logging levels dynamically in production without restarting the application.

---

### **Best Practices**

1. **Secure the Endpoint**:
   - Restrict access to the `/actuator/loggers` endpoint using Spring Security to prevent unauthorized changes to logging levels.

2. **Use for Debugging**:
   - Enable dynamic logging only in non-production environments or for temporary debugging in production.

3. **Combine with Centralized Logging**:
   - Use the `/loggers` endpoint in combination with centralized logging tools (e.g., ELK, Splunk) for better log management.

4. **Monitor Default Loggers**:
   - Regularly monitor the `ROOT` logger and other critical loggers to ensure proper logging configuration.
