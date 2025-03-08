### **Micrometer: Overview and Examples**

Micrometer is a powerful library for application observability and metric monitoring. It provides a vendor-neutral API for collecting and exporting metrics to various monitoring systems. Micrometer is widely used in Spring Boot applications, especially with Spring Boot Actuator, to expose detailed metrics about the application's behavior and performance.

---

### **What is Micrometer?**

1. **Definition**:
   - Micrometer is a metrics instrumentation library that helps developers monitor and observe application performance.
   - It acts as a facade over different observability systems, allowing you to instrument your code in a standardized way without being tied to a specific monitoring system.

2. **Key Features**:
   - Vendor-neutral: Works with multiple monitoring systems like Prometheus, Grafana, New Relic, and Datadog.
   - Lightweight: Designed for JVM-based applications with minimal performance impact.
   - Flexible: Supports dimensional metrics (metrics with tags) for better filtering and analysis.

3. **Integration**:
   - Micrometer is integrated into Spring Boot Actuator by default.
   - It collects and exposes application metrics via endpoints like `/actuator/metrics`.

4. **Use Case**:
   - Observing application behavior.
   - Monitoring resource usage (CPU, memory, threads, etc.).
   - Tracking application-specific metrics (e.g., custom counters, timers).
   - Exporting metrics to external monitoring systems.

---

### **How Micrometer Works**

- **Instrumentation**:
  - Micrometer instruments your application code to collect metrics.
  - Metrics can be counters, gauges, timers, or distribution summaries.

- **Exporting**:
  - Micrometer supports exporting metrics to various monitoring systems like Prometheus, Datadog, New Relic, and more.

- **Tags**:
  - Metrics can have tags (key-value pairs) to provide additional context, making it easier to filter and analyze metrics.

---

### **Supported Monitoring Systems**

Micrometer integrates with a wide range of monitoring and observability tools, including:

1. **Prometheus**: A popular open-source monitoring system with a pull-based data collection model.
2. **Grafana**: A visualization tool often used with Prometheus.
3. **Datadog**: A SaaS-based monitoring and analytics platform.
4. **New Relic**: A cloud-based observability platform.
5. **Elastic**: Elasticsearch for storing and Kibana for visualizing metrics.
6. **Dynatrace**: An application performance monitoring (APM) tool.
7. **CloudWatch**: Amazon's monitoring service.
8. **Wavefront**: A SaaS-based metrics monitoring and analytics platform.
9. **JMX**: For local monitoring via Java Management Extensions.
10. **OpenTelemetry**: A CNCF project for telemetry data standards.

---

### **Key Metric Types in Micrometer**

1. **Counter**:
   - A counter is used to track the number of times an event occurs.
   - Example: Counting the number of HTTP requests.

2. **Gauge**:
   - A gauge represents a value that can go up or down.
   - Example: Monitoring memory usage or active threads.

3. **Timer**:
   - A timer measures how long an operation takes and counts the number of invocations.
   - Example: Measuring the duration of database queries.

4. **Distribution Summary**:
   - Tracks the distribution of values, such as the size of payloads or response sizes.
   - Example: Tracking the size of files uploaded to a server.

5. **Long Task Timer**:
   - Measures the duration of long-running tasks.
   - Example: Tracking the time taken by background jobs.

---

### **Using Micrometer in Spring Boot**

#### **1. Adding Micrometer to Your Project**

Micrometer is included in Spring Boot Actuator by default. To use it, add the following dependency:

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

#### **2. Exposing Metrics**

Expose metrics by enabling the `/actuator/metrics` endpoint in your `application.properties` or `application.yml`:

```properties
management.endpoints.web.exposure.include=metrics
management.endpoint.metrics.enabled=true
```

---

#### **3. Creating a Custom Metric**

You can create custom metrics using the `MeterRegistry` provided by Micrometer.

**Example: Counter Metric**
```java
import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class MyScheduledTask {

    private final MeterRegistry meterRegistry;

    public MyScheduledTask(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
    }

    @Scheduled(fixedRate = 10000) // Executes every 10 seconds
    public void hello() {
        meterRegistry.counter("my.scheduled.task").increment();
        System.out.println("Hello World");
    }
}
```

- This creates a counter metric named `my.scheduled.task`.
- The counter increments every time the `hello()` method is executed.

**Access the Metric**:
```
http://localhost:8080/actuator/metrics/my.scheduled.task
```

**Response Example**:
```json
{
  "name": "my.scheduled.task",
  "measurements": [
    {
      "statistic": "COUNT",
      "value": 3
    }
  ],
  "availableTags": []
}
```

---

#### **4. Timer Metric Example**

**Code Example:**
```java
import io.micrometer.core.instrument.MeterRegistry;
import io.micrometer.core.instrument.Timer;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class MyService {

    private final Timer timer;

    public MyService(MeterRegistry meterRegistry) {
        this.timer = meterRegistry.timer("my.service.timer");
    }

    public void performTask() {
        timer.record(() -> {
            // Simulate task
            try {
                TimeUnit.MILLISECONDS.sleep(500);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });
    }
}
```

**Access the Metric**:
```
http://localhost:8080/actuator/metrics/my.service.timer
```

---

#### **5. Gauge Metric Example**

**Code Example:**
```java
import io.micrometer.core.instrument.Gauge;
import io.micrometer.core.instrument.MeterRegistry;
import org.springframework.stereotype.Component;

import java.util.concurrent.atomic.AtomicInteger;

@Component
public class MyGaugeMetric {

    private final AtomicInteger value = new AtomicInteger(0);

    public MyGaugeMetric(MeterRegistry meterRegistry) {
        Gauge.builder("my.gauge.metric", value, AtomicInteger::get)
             .description("A custom gauge metric")
             .register(meterRegistry);
    }

    public void increment() {
        value.incrementAndGet();
    }

    public void decrement() {
        value.decrementAndGet();
    }
}
```

**Access the Metric**:
```
http://localhost:8080/actuator/metrics/my.gauge.metric
```

---

### **Exporting Metrics to Monitoring Systems**

Micrometer supports exporting metrics to various monitoring systems. Here’s how you can configure some popular ones:

#### **1. Prometheus**
Add the Prometheus dependency:
```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

Expose the Prometheus scrape endpoint:
```properties
management.endpoints.web.exposure.include=prometheus
```

Access metrics at:
```
http://localhost:8080/actuator/prometheus
```

#### **2. Datadog**
Add the Datadog dependency:
```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-datadog</artifactId>
</dependency>
```

Configure Datadog properties:
```properties
management.metrics.export.datadog.api-key=your-datadog-api-key
management.metrics.export.datadog.enabled=true
```

#### **3. New Relic**
Add the New Relic dependency:
```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-new-relic</artifactId>
</dependency>
```

Configure New Relic properties:
```properties
management.metrics.export.newrelic.api-key=your-new-relic-api-key
management.metrics.export.newrelic.account-id=your-account-id
```

---

### **Best Practices**

1. **Start with Built-In Metrics**:
   - Use the default metrics provided by Spring Boot Actuator before adding custom ones.

2. **Tag Metrics**:
   - Use tags to add context to your metrics for better filtering and analysis.

3. **Monitor Critical Metrics**:
   - Focus on metrics that provide insights into application performance and health (e.g., HTTP request counts, memory usage).

4. **Integrate with Monitoring Tools**:
   - Export your metrics to tools like Prometheus and Grafana for visualization and alerting.

