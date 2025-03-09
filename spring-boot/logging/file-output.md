### **File Output in Logging (Spring Boot)**

By default, Spring Boot logs messages only to the **console**. However, Spring Boot provides built-in support to enable logging output to a **file**. This feature is useful for applications that require persistent logs for debugging, auditing, or monitoring purposes.

---

### **1. Configuring File Output in Spring Boot**

You can configure file output for logging using the following properties in the `application.properties` or `application.yml` file.

#### **Key Properties for File Logging**

1. **`logging.file.name`**:
   - Specifies the name of the log file.
   - Example: `application.log` or `/tmp/application.log`.

2. **`logging.file.path`**:
   - Specifies the directory where the log file will be created.
   - The file name will default to `spring.log` if only the path is provided.
   - Example: `/tmp/` will create the log file `/tmp/spring.log`.

---

#### **Example 1: Using `logging.file.name`**

```properties
logging.file.name=application.log
```

- This creates the log file **`application.log`** in the current working directory.

---

#### **Example 2: Using `logging.file.path`**

```properties
logging.file.path=/tmp/
```

- This creates the log file **`/tmp/spring.log`** in the specified directory.

---

#### **Example 3: Combining Console and File Logging**

Spring Boot allows simultaneous logging to both the console and a file.

```properties
logging.file.name=logs/myapp.log
logging.level.root=info
```

- Logs will be printed to both the console and the file `logs/myapp.log`.

---

### **2. File Logging with `application.yml`**

```yaml
logging:
  file:
    name: application.log
  level:
    root: info
```

This configuration creates a file named `application.log` in the current directory and logs messages at the `INFO` level or higher.

---

### **3. Customizing Log File Format**

Spring Boot allows you to customize the log format for file output using the `logging.pattern.file` property.

#### **Example: Custom Log Format**

```properties
logging.file.name=application.log
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n
```

- **`%d{}`**: Date and time.
- **`[%thread]`**: Thread name.
- **`%-5level`**: Log level (e.g., INFO, WARN).
- **`%logger{36}`**: Logger name (up to 36 characters).
- **`%msg`**: Log message.

**Sample Output:**
```
2025-03-09 15:00:00 [main] INFO  com.example.MyApp - Application started successfully
```

---

### **4. File Rotation and Size Management**

To manage log file size and rotation, you can configure a custom logging framework like **Logback** (default in Spring Boot).

#### **Logback Configuration for File Rotation**

Create a `logback-spring.xml` file in the `src/main/resources` directory:

```xml
<configuration>
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/myapp.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/myapp.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory> <!-- Keep logs for 7 days -->
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

- **`TimeBasedRollingPolicy`**:
  - Rotates the log file daily.
  - Keeps logs for the last 7 days.

---

### **5. File Logging with Log Levels**

You can set different logging levels for file output using `logging.level`.

#### **Example: Setting Log Levels**

```properties
logging.file.name=application.log
logging.level.root=info
logging.level.com.example=debug
```

- Logs at `INFO` level or higher for all packages.
- Logs at `DEBUG` level or higher for the `com.example` package.

---

### **6. Testing File Logging**

#### **Sample Code**

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@Slf4j
@SpringBootApplication
public class LoggingApplication {

    public static void main(String[] args) {
        SpringApplication.run(LoggingApplication.class, args);

        log.info("Application started successfully.");
        log.warn("This is a warning message.");
        log.error("This is an error message.");
    }
}
```

#### **Configuration**

```properties
logging.file.name=application.log
logging.level.root=info
```

#### **Output in `application.log`**

```
2025-03-09 15:00:00 [main] INFO  com.example.LoggingApplication - Application started successfully.
2025-03-09 15:00:01 [main] WARN  com.example.LoggingApplication - This is a warning message.
2025-03-09 15:00:02 [main] ERROR com.example.LoggingApplication - This is an error message.
```

---

### **7. Advanced Examples**

#### **7.1. Logging to a File in a Specific Directory**

```properties
logging.file.path=/var/logs/myapp/
```

- Logs will be written to `/var/logs/myapp/spring.log`.

---

#### **7.2. Logging Different Levels for Console and File**

You can configure **Logback** to log different levels for console and file outputs.

**`logback-spring.xml` Example:**

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/myapp.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/myapp.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <logger name="com.example" level="debug" additivity="false">
        <appender-ref ref="CONSOLE" />
    </logger>

    <root level="info">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

- **Console** logs messages at the `DEBUG` level for `com.example`.
- **File** logs messages at the `INFO` level for all packages.

---

### **8. Best Practices for File Logging**

1. **Rotate Logs**:
   - Use file rotation to avoid large log files.
   - Example: Use `TimeBasedRollingPolicy` in Logback.

2. **Archive Old Logs**:
   - Keep logs for a limited period (e.g., 7 days) and archive or delete old logs.

3. **Separate Logs by Environment**:
   - Use different log files for development, testing, and production environments.

4. **Monitor Logs**:
   - Use tools like **ELK Stack** (Elasticsearch, Logstash, Kibana) or **Splunk** for centralized log monitoring.

5. **Avoid Logging Sensitive Data**:
   - Do not log passwords, tokens, or other sensitive information.

