### **Logging in Java Spring Boot**

Logging is an essential part of any application, and Spring Boot provides a seamless way to integrate logging into your Java applications. By default, Spring Boot uses **SLF4J** (Simple Logging Facade for Java) as a logging API and **Logback** as the default logging implementation. This allows developers to log application behavior and debug issues efficiently.

---

### **1. Introduction to Logging in Spring**

- **Spring Framework** itself does not have a dedicated logging feature. Instead, it relies on the logging libraries available in Java.
- By default, Spring Boot uses **Logback** for logging, but you can configure it to use other logging frameworks like **Log4j2** or **Java Util Logging**.
- Spring Boot provides additional features to simplify logging configuration using **application.properties** or **application.yml**.

---

### **2. Default Logging Settings in Spring Boot**

When you create a Spring Boot application:
- Logging is automatically configured.
- Logs are printed to the **console** by default.
- A default **log format** is used, which includes:
  - Timestamp
  - Log level (INFO, WARN, ERROR, etc.)
  - Thread name
  - Logger name
  - Log message

#### **Default Log Format Example**
```
2023-03-09 14:56:32.123 INFO 12345 --- [main] com.example.MyApp : Application started successfully
```

---

### **3. Logging Levels**

Logging levels determine the severity of the log messages and allow you to filter logs accordingly. The common logging levels are:
1. **TRACE**: Most detailed logs, used for debugging.
2. **DEBUG**: Detailed information for debugging purposes.
3. **INFO**: General application information.
4. **WARN**: Indicates potential issues.
5. **ERROR**: Errors that need immediate attention.
6. **OFF**: Turns off logging.

---

### **4. Configuring Logging in Spring Boot**

You can configure logging in Spring Boot using:
- **application.properties** or **application.yml**.
- External configuration files like **logback.xml** or **log4j2.xml**.

#### **Using `application.properties`**

You can set logging levels for specific packages or classes using the `logging.level` prefix.

```properties
# Set the root logging level
logging.level.root=info

# Set logging level for a specific package
logging.level.com.example=debug
```

#### **Using `application.yml`**

```yaml
logging:
  level:
    root: info
    com.example: debug
```

---

### **5. Logging with SLF4J**

Spring Boot applications typically use **SLF4J** with a logging implementation like Logback. To use logging in your application:
1. Add the `@Slf4j` annotation to your class.
2. Use the `log` object to log messages at different levels.

#### **Example: Logging with SLF4J**

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Service;

@Slf4j
@Service
public class MyService {

    public void performTask() {
        log.info("Performing a task...");
        log.warn("This is a warning.");
        log.error("An error occurred!");
    }
}
```

#### **Output**
```
2023-03-09 14:56:32.123 INFO 12345 --- [main] com.example.MyService : Performing a task...
2023-03-09 14:56:32.124 WARN 12345 --- [main] com.example.MyService : This is a warning.
2023-03-09 14:56:32.125 ERROR 12345 --- [main] com.example.MyService : An error occurred!
```

---

### **6. Advanced Logging Configuration**

#### **6.1. Logback Configuration**

Logback is the default logging framework in Spring Boot. You can customize Logback by creating a `logback-spring.xml` file in the `src/main/resources` directory.

Example `logback-spring.xml`:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="info">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

---

#### **6.2. Log4j2 Configuration**

If you prefer **Log4j2**, include the dependency in your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

Then, create a `log4j2-spring.xml` file:

```xml
<Configuration>
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} [%t] %-5level %logger{36} - %msg%n" />
        </Console>
    </Appenders>
    <Loggers>
        <Root level="info">
            <AppenderRef ref="Console" />
        </Root>
    </Loggers>
</Configuration>
```

---

### **7. Logging to a File**

You can configure Spring Boot to write logs to a file using `application.properties`:

```properties
# Enable file logging
logging.file.name=logs/myapp.log

# Set maximum file size and backup
logging.file.max-size=10MB
logging.file.max-history=5
```

---

### **8. Customizing Log Format**

To customize the log format, use the `logging.pattern.console` and `logging.pattern.file` properties.

Example:

```properties
# Custom console log format
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n

# Custom file log format
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n
```

---

### **9. Testing Logging**

Spring Boot provides tools to test logging in your application.

#### **Example: Logging Test**

```java
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@Slf4j
@SpringBootTest
public class LoggingTest {

    @Test
    void testLogging() {
        log.info("Hello World");
        log.warn("Hello Spring");
        log.error("Hello Programmer Zaman Now");
    }
}
```

#### **Output**
```
2023-03-09 14:56:32.123 INFO 12345 --- [main] com.example.LoggingTest : Hello World
2023-03-09 14:56:32.124 WARN 12345 --- [main] com.example.LoggingTest : Hello Spring
2023-03-09 14:56:32.125 ERROR 12345 --- [main] com.example.LoggingTest : Hello Programmer Zaman Now
```

---

### **10. Externalizing Logging Configuration**

You can externalize logging configuration by placing `logback-spring.xml` or `log4j2-spring.xml` in an external directory and referencing it using the `logging.config` property.

```properties
logging.config=/path/to/external/logback-spring.xml
```

---

### **11. Examples of Logging Use Cases**

#### **11.1. Conditional Logging**

```java
if (log.isDebugEnabled()) {
    log.debug("Debugging information: {}", data);
}
```

#### **11.2. Logging Exceptions**

```java
try {
    // Some code
} catch (Exception e) {
    log.error("An error occurred: ", e);
}
```

#### **11.3. Structured Logging**

Use placeholders for dynamic values:

```java
log.info("User {} logged in at {}", username, loginTime);
```

---

### **12. Best Practices for Logging**

1. **Use Appropriate Log Levels**:
   - Use `DEBUG` for development and `INFO` for production.
   - Avoid using `TRACE` or `DEBUG` in production unless necessary.

2. **Avoid Logging Sensitive Data**:
   - Do not log passwords, tokens, or other sensitive information.

3. **Log Exceptions Properly**:
   - Always include the exception stack trace when logging errors.

4. **Use Externalized Configuration**:
   - Keep logging configuration flexible using external files.

5. **Monitor Logs**:
   - Use tools like **ELK Stack** (Elasticsearch, Logstash, Kibana) or **Splunk** for centralized log management.

