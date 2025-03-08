### **Actuator Endpoint: `/actuator/health`**

The `/actuator/health` endpoint in Spring Boot Actuator provides a way to monitor the health of your application. It offers detailed information about the application's status and its dependencies, such as databases, disk space, and other critical components. By default, this endpoint is **enabled** and provides a simple "UP" or "DOWN" status.

---

### **Features of the `/actuator/health` Endpoint**

1. **Health Status**:
   - Indicates whether the application is healthy (`UP`) or unhealthy (`DOWN`).

2. **Component-Level Health Checks**:
   - Provides detailed health checks for specific components like:
     - Database connectivity
     - Disk space availability
     - External services (e.g., messaging queues, caches)

3. **Custom Health Indicators**:
   - Allows developers to create custom health indicators for specific use cases.

4. **Default Health Indicators**:
   - Spring Boot Actuator includes built-in health indicators for common components (e.g., databases, disk space, messaging systems).

5. **Customizable Details**:
   - You can configure whether the endpoint shows detailed health information or just the summary status.

6. **Integration with Monitoring Tools**:
   - Works seamlessly with tools like Prometheus, Grafana, and Kubernetes for health monitoring.

---

### **How to Enable and Customize the `/actuator/health` Endpoint**

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

#### **2. Default Behavior**

By default, the `/actuator/health` endpoint is enabled and exposed. You can access it via:
```
http://localhost:8080/actuator/health
```

The default response is:
```json
{
  "status": "UP"
}
```

---

#### **3. Enable Detailed Health Information**

By default, detailed health information is hidden for security reasons. To enable it, update your `application.properties` or `application.yml`:

**For `application.properties`:**
```properties
management.endpoint.health.show-details=always
```

**For `application.yml`:**
```yaml
management:
  endpoint:
    health:
      show-details: always
```

With this configuration, the `/actuator/health` endpoint will display detailed information about each component's health.

---

#### **4. Expose the Endpoint**

To ensure the `/actuator/health` endpoint is accessible, include it in the exposed endpoints list:

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=health
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: health
```

---

### **Default Health Indicators**

Spring Boot Actuator includes several built-in health indicators. Some of the most commonly used ones are:

| **Health Indicator**           | **Description**                                                                 |
|---------------------------------|---------------------------------------------------------------------------------|
| `DiskSpaceHealthIndicator`      | Checks available disk space.                                                   |
| `DataSourceHealthIndicator`     | Verifies database connectivity and status.                                     |
| `MongoHealthIndicator`          | Checks MongoDB connectivity.                                                   |
| `RedisHealthIndicator`          | Verifies Redis server connectivity.                                            |
| `CassandraHealthIndicator`      | Checks Cassandra database connectivity.                                        |
| `RabbitHealthIndicator`         | Verifies RabbitMQ server connectivity.                                         |
| `ElasticsearchHealthIndicator`  | Checks Elasticsearch cluster status.                                           |
| `LivenessStateHealthIndicator`  | Provides the application's liveness state (useful for Kubernetes).             |
| `ReadinessStateHealthIndicator` | Provides the application's readiness state (useful for Kubernetes).            |
| `PingHealthIndicator`           | A simple "ping" check to verify that the application is running.               |

---

### **Sample Responses**

#### **1. Basic Response**
When no detailed information is enabled:
```json
{
  "status": "UP"
}
```

#### **2. Detailed Response**
When detailed information is enabled:
```json
{
  "status": "UP",
  "components": {
    "db": {
      "status": "UP",
      "details": {
        "database": "MySQL",
        "validationQuery": "isValid()"
      }
    },
    "diskSpace": {
      "status": "UP",
      "details": {
        "total": 994662584320,
        "free": 817259040768,
        "threshold": 10485760,
        "path": "/"
      }
    },
    "ping": {
      "status": "UP"
    }
  }
}
```

---

### **Creating Custom Health Indicators**

You can create custom health indicators by implementing the `HealthIndicator` interface or extending the `AbstractHealthIndicator` class.

#### **Example 1: Simple Custom Health Indicator**
```java
import org.springframework.boot.actuate.health.Health;
import org.springframework.boot.actuate.health.HealthIndicator;
import org.springframework.stereotype.Component;

@Component
public class MyCustomHealthIndicator implements HealthIndicator {

    @Override
    public Health health() {
        boolean isHealthy = checkCustomHealthLogic();
        if (isHealthy) {
            return Health.up().withDetail("customHealth", "All systems are operational").build();
        } else {
            return Health.down().withDetail("customHealth", "Something is wrong").build();
        }
    }

    private boolean checkCustomHealthLogic() {
        // Add custom health logic here
        return true;
    }
}
```

**Response:**
```json
{
  "status": "UP",
  "components": {
    "myCustomHealthIndicator": {
      "status": "UP",
      "details": {
        "customHealth": "All systems are operational"
      }
    }
  }
}
```

---

#### **Example 2: Advanced Custom Health Indicator**
Using the `AbstractHealthIndicator` class:
```java
import org.springframework.boot.actuate.health.AbstractHealthIndicator;
import org.springframework.boot.actuate.health.Health;
import org.springframework.stereotype.Component;

@Component
public class AdvancedHealthIndicator extends AbstractHealthIndicator {

    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        // Custom health check logic
        boolean isHealthy = performAdvancedChecks();
        if (isHealthy) {
            builder.up()
                   .withDetail("app", "OK")
                   .withDetail("error", "NO ERROR");
        } else {
            builder.down()
                   .withDetail("app", "FAIL")
                   .withDetail("error", "Critical issue detected");
        }
    }

    private boolean performAdvancedChecks() {
        // Add your advanced health check logic here
        return true; // Simulating a healthy state
    }
}
```

**Response:**
```json
{
  "status": "UP",
  "components": {
    "advancedHealthIndicator": {
      "status": "UP",
      "details": {
        "app": "OK",
        "error": "NO ERROR"
      }
    }
  }
}
```

---

### **Securing the `/actuator/health` Endpoint**

You can secure the health endpoint using Spring Security to restrict access to authorized users only.

**Example Security Configuration:**
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
            .antMatchers("/actuator/health").authenticated()
            .and()
            .httpBasic();
    }
}
```

---

### **Best Practices**

1. **Limit Details in Production**:
   - Avoid exposing detailed health information in production to prevent sensitive data leaks.
   - Use `management.endpoint.health.show-details=when_authorized`.

2. **Use Liveness and Readiness Probes**:
   - For Kubernetes, use `LivenessStateHealthIndicator` and `ReadinessStateHealthIndicator` to manage container health.

3. **Secure the Endpoint**:
   - Always secure the `/actuator/health` endpoint using Spring Security or custom security configurations.

4. **Monitor Critical Components**:
   - Ensure health checks are implemented for critical components like databases, caches, and external services.
