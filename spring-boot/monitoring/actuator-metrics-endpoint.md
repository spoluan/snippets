### **Metrics in Spring Boot Actuator**

Metrics provide insights into the performance and behavior of an application by measuring various aspects such as resource usage, application health, and request/response details. Spring Boot Actuator includes a `/actuator/metrics` endpoint that exposes a variety of useful metrics for monitoring and debugging.

---

### **What are Metrics?**

1. **Definition**:
   - Metrics are numerical measurements that represent the state or behavior of an application or its components.
   - Examples include CPU usage, memory consumption, disk space, HTTP request counts, and database connection details.

2. **Purpose**:
   - To monitor application performance and resource usage.
   - To debug issues such as slow responses, high resource consumption, or bottlenecks.

3. **Access**:
   - Metrics can be accessed via the `/actuator/metrics` endpoint.

---

### **How to Enable and Configure `/metrics`**

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

#### **2. Enable and Expose the `/metrics` Endpoint**

By default, the `/metrics` endpoint is enabled but not exposed. You need to configure your `application.properties` or `application.yml` to expose it.

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=metrics
management.endpoint.metrics.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: metrics
  endpoint:
    metrics:
      enabled: true
```

---

### **Accessing the `/metrics` Endpoint**

Once configured, you can access the `/metrics` endpoint at:
```
http://localhost:8080/actuator/metrics
```

This endpoint provides a list of all available metrics in your application.

You can also access specific metrics using:
```
http://localhost:8080/actuator/metrics/{metricName}
```

For example:
```
http://localhost:8080/actuator/metrics/http.server.requests
```

---

### **Features of the `/metrics` Endpoint**

1. **List of Metrics**:
   - The `/metrics` endpoint provides a list of all available metrics in your application.

2. **Detailed Metrics**:
   - Access detailed information about a specific metric, including measurements and tags.

3. **Custom Metrics**:
   - Add custom metrics using libraries like Micrometer.

---

### **Sample Response from `/metrics` Endpoint**

**1. List of Metrics**:
When accessing `/metrics`, you will see a JSON response containing all available metrics.

**Example:**
```json
{
  "names": [
    "application.ready.time",
    "application.started.time",
    "disk.free",
    "disk.total",
    "executor.active",
    "executor.completed",
    "executor.pool.core",
    "executor.pool.max",
    "executor.pool.size",
    "executor.queue.remaining",
    "http.server.requests",
    "jvm.memory.used",
    "jvm.memory.max",
    "process.uptime",
    "system.cpu.usage"
  ]
}
```

---

**2. Detailed Metric Information**:
When accessing a specific metric (e.g., `http.server.requests`), you will see detailed information about that metric.

**Example:**
```json
{
  "name": "http.server.requests",
  "baseUnit": "seconds",
  "measurements": [
    {
      "statistic": "COUNT",
      "value": 3
    },
    {
      "statistic": "TOTAL_TIME",
      "value": 0.051352791999999994
    },
    {
      "statistic": "MAX",
      "value": 0.024333167
    }
  ],
  "availableTags": [
    {
      "tag": "exception",
      "values": ["none"]
    },
    {
      "tag": "method",
      "values": ["GET", "POST"]
    },
    {
      "tag": "uri",
      "values": ["/actuator/metrics", "/api/resource"]
    }
  ]
}
```

---

### **Key Fields in the Response**

1. **Base Fields**:
   - `name`: The name of the metric.
   - `baseUnit`: The unit of measurement (e.g., seconds, bytes).

2. **Measurements**:
   - `statistic`: The type of measurement (e.g., `COUNT`, `TOTAL_TIME`, `MAX`).
   - `value`: The value of the measurement.

3. **Available Tags**:
   - Tags provide additional context for the metric (e.g., HTTP method, URI, exceptions).

---

### **Common Metrics**

Here are some commonly available metrics in Spring Boot Actuator:

1. **Application Metrics**:
   - `application.ready.time`: Time taken for the application to be ready.
   - `application.started.time`: Time taken for the application to start.

2. **Disk Metrics**:
   - `disk.free`: Free disk space.
   - `disk.total`: Total disk space.

3. **Executor Metrics**:
   - `executor.active`: Active threads in the executor.
   - `executor.completed`: Completed tasks.
   - `executor.queue.remaining`: Remaining queue capacity.

4. **HTTP Metrics**:
   - `http.server.requests`: Metrics related to HTTP requests, including count, total time, and max time.

5. **JVM Metrics**:
   - `jvm.memory.used`: Memory currently in use by the JVM.
   - `jvm.memory.max`: Maximum memory available to the JVM.
   - `jvm.threads.live`: Number of live threads.

6. **Database Metrics** (e.g., HikariCP):
   - `hikaricp.connections`: Current active database connections.
   - `hikaricp.connections.usage`: Usage percentage of database connections.

7. **System Metrics**:
   - `system.cpu.usage`: CPU usage of the system.
   - `process.uptime`: Uptime of the application process.

---

### **Custom Metrics**

You can add custom metrics to your application using Micrometer, the default metrics library in Spring Boot.

**Example: Custom Counter Metric**
```java
import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.stereotype.Component;

@Component
public class CustomMetrics {

    private final Counter customCounter;

    public CustomMetrics(MeterRegistry meterRegistry) {
        this.customCounter = meterRegistry.counter("custom.metric.counter");
    }

    public void incrementCounter() {
        customCounter.increment();
    }
}
```

**Access the Custom Metric**:
```
http://localhost:8080/actuator/metrics/custom.metric.counter
```

---

### **Best Practices for Metrics**

1. **Secure the Endpoint**:
   - Restrict access to the `/metrics` endpoint using Spring Security to prevent unauthorized access.

2. **Monitor Critical Metrics**:
   - Focus on key metrics such as memory usage, CPU usage, request count, and database connections.

3. **Use Tags for Context**:
   - Add tags to metrics for better filtering and analysis (e.g., HTTP method, URI, or exception type).

4. **Integrate with Monitoring Tools**:
   - Export metrics to external monitoring tools like Prometheus, Grafana, or New Relic.

5. **Optimize Custom Metrics**:
   - Avoid creating too many custom metrics, as they can impact performance.

