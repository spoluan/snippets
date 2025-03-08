### **Spring Actuator in Spring Boot**

Spring Actuator is a powerful feature in Spring Boot that provides **production-ready monitoring and management tools** for applications. It allows developers to gain insights into the application’s health, metrics, environment, and other runtime information without requiring manual setup.

---

### **Why Use Spring Actuator?**

1. **Monitoring**:
   - Provides runtime information about the application, such as health status, metrics, and environment properties.

2. **Ease of Integration**:
   - Built into Spring Boot, requiring minimal configuration to enable monitoring features.

3. **Customizability**:
   - Allows developers to add custom health checks, metrics, and endpoints.

4. **Production-Ready**:
   - Integrates seamlessly with external monitoring tools like Prometheus, Grafana, and others.

5. **Security**:
   - Provides mechanisms to secure sensitive monitoring data.

---

### **Key Features of Spring Actuator**

1. **Endpoints**:
   - Actuator exposes various **endpoints** for monitoring and managing the application. Examples include `/actuator/health`, `/actuator/metrics`, and `/actuator/env`.

2. **Health Checks**:
   - Provides built-in health indicators (e.g., database, disk space) and allows custom health indicators.

3. **Metrics**:
   - Exposes application metrics (e.g., JVM memory, thread count, HTTP requests).

4. **Integration with External Tools**:
   - Can integrate with Prometheus, Grafana, ELK Stack, and others for advanced monitoring.

5. **Customizability**:
   - Developers can create custom endpoints, metrics, and health checks.

6. **Environment Information**:
   - Displays runtime environment properties, configuration, and system details.

---

### **How to Use Spring Actuator**

#### **1. Add Spring Actuator Dependency**

Add the following dependency to your `pom.xml` file:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

For Gradle:

```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

---

#### **2. Enable Actuator Endpoints**

By default, only the `/actuator/health` and `/actuator/info` endpoints are enabled. To enable all endpoints, update the `application.properties` or `application.yml`:

**`application.properties`:**
```properties
management.endpoints.web.exposure.include=*
```

**`application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

---

#### **3. Access Actuator Endpoints**

Run the application and access the following default endpoints:

- **Health Check**: `/actuator/health`
- **Application Info**: `/actuator/info`
- **Metrics**: `/actuator/metrics`
- **Environment Details**: `/actuator/env`

---

### **Common Actuator Endpoints**

| Endpoint          | Description                                                                 |
|--------------------|-----------------------------------------------------------------------------|
| `/actuator/health` | Displays the health status of the application.                             |
| `/actuator/info`   | Displays custom application information (can be configured).               |
| `/actuator/env`    | Shows environment properties, including system properties and environment variables. |
| `/actuator/metrics`| Provides application metrics, such as memory usage, thread count, etc.     |
| `/actuator/loggers`| Allows viewing and configuring log levels at runtime.                      |
| `/actuator/beans`  | Displays all Spring Beans loaded in the application context.               |
| `/actuator/mappings`| Shows all URL mappings in the application.                                |
| `/actuator/threaddump` | Provides a thread dump of the application.                             |
| `/actuator/heapdump`   | Exposes a heap dump of the application.                                |

---

### **Examples of Using Spring Actuator**

#### **1. Health Check Endpoint**

The `/actuator/health` endpoint provides the health status of the application.

**Default Response**:
```json
{
  "status": "UP"
}
```

**Custom Health Check**:
You can add a custom health check by implementing the `HealthIndicator` interface:

```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class CustomHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        boolean isHealthy = checkCustomHealthCondition();
        if (isHealthy) {
            return Health.up().withDetail("Custom Health", "All systems are operational").build();
        }
        return Health.down().withDetail("Custom Health", "Something is wrong").build();
    }

    private boolean checkCustomHealthCondition() {
        // Add your custom health logic here
        return true;
    }
}
```

---

#### **2. Metrics Endpoint**

The `/actuator/metrics` endpoint provides metrics such as memory usage, thread count, and more.

**Example Request**:
```bash
GET /actuator/metrics
```

**Example Response**:
```json
{
  "names": [
    "jvm.memory.used",
    "jvm.memory.max",
    "system.cpu.usage",
    "http.server.requests"
  ]
}
```

You can also query specific metrics:

**Example Request**:
```bash
GET /actuator/metrics/jvm.memory.used
```

**Example Response**:
```json
{
  "name": "jvm.memory.used",
  "measurements": [
    {
      "statistic": "VALUE",
      "value": 12345678
    }
  ],
  "availableTags": [
    {
      "tag": "area",
      "values": ["heap", "nonheap"]
    }
  ]
}
```

---

#### **3. Custom Info Endpoint**

You can customize the `/actuator/info` endpoint by adding properties to `application.properties`:

```properties
info.app.name=My Application
info.app.version=1.0.0
info.app.description=This is a sample Spring Boot application.
```

**Response from `/actuator/info`:**
```json
{
  "app": {
    "name": "My Application",
    "version": "1.0.0",
    "description": "This is a sample Spring Boot application."
  }
}
```

---

#### **4. Securing Actuator Endpoints**

You can secure sensitive Actuator endpoints by requiring authentication. For example, with Spring Security:

**Add Spring Security Dependency**:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**Configure Security**:
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;

@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .authorizeRequests()
            .antMatchers("/actuator/**").authenticated()
            .and()
            .httpBasic();
    }
}
```

---

#### **5. Custom Actuator Endpoint**

You can create a custom Actuator endpoint by extending the `AbstractEndpoint` class or using the `@Endpoint` annotation.

**Example**:
```java
import org.springframework.boot.actuate.endpoint.annotation.Endpoint;
import org.springframework.boot.actuate.endpoint.annotation.ReadOperation;
import org.springframework.stereotype.Component;

@Component
@Endpoint(id = "custom")
public class CustomEndpoint {

    @ReadOperation
    public String customEndpoint() {
        return "This is a custom endpoint";
    }
}
```

**Access**:
```bash
GET /actuator/custom
```

---

#### **6. Integration with Prometheus**

Spring Actuator integrates with Prometheus for advanced monitoring.

**Add Micrometer Dependency**:
```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

**Enable Prometheus Metrics**:
```properties
management.endpoints.web.exposure.include=prometheus
```

Access the Prometheus metrics at `/actuator/prometheus`.

---

### **Key Configurations**

1. **Expose Specific Endpoints**:
   ```properties
   management.endpoints.web.exposure.include=health,info,metrics
   ```

2. **Change Endpoint Base Path**:
   ```properties
   management.endpoints.web.base-path=/monitoring
   ```

3. **Disable Specific Endpoints**:
   ```properties
   management.endpoint.health.enabled=false
   ```

