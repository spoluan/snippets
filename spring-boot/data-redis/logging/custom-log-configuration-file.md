### Custom Log Configuration File in Spring Boot

Spring Boot provides a default logging configuration using **Logback**. However, if the default configuration does not meet your requirements, you can override it by creating a custom log configuration file. This allows you to define advanced logging behavior, including custom log levels, appenders, and log formats.

---

### Key Features of Custom Log Configuration

1. **Full Control**:
   - You can define detailed logging behavior, such as custom appenders, rolling policies, and filters.

2. **Automatic Detection**:
   - If Spring Boot detects a `logback.xml` or `logback-spring.xml` file in the `resources` directory, it automatically uses it instead of the default configuration.

3. **Advanced Logging**:
   - You can configure multiple appenders (e.g., console, file, database) and define specific logging rules for different packages or classes.

4. **Integration with MDC**:
   - Custom configurations allow you to include MDC (Mapped Diagnostic Context) values for better traceability in distributed systems.

---

### File Types for Custom Log Configuration

1. **`logback.xml`**:
   - A standard Logback configuration file.
   - Automatically detected by Spring Boot if placed in the `resources` directory.

2. **`logback-spring.xml`**:
   - A Spring Boot-specific Logback configuration file.
   - Allows the use of Spring placeholders (e.g., `${}`) for dynamic values.

---

### Steps to Create a Custom Log Configuration File

1. **Create a `logback.xml` or `logback-spring.xml` File**:
   - Place the file in the `src/main/resources` directory.

2. **Define Your Configuration**:
   - Use Logback's XML syntax to define appenders, encoders, and loggers.

3. **Spring Boot Detection**:
   - Spring Boot will automatically use your custom configuration file.

---

### Example 1: Basic `logback.xml` Configuration

```xml
<configuration>
    <!-- Console Appender -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- Root Logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

**Explanation**:
- Defines a console appender that prints logs to the console.
- Sets the root logger's level to `INFO`.

---

### Example 2: File Logging with Rolling Policy

```xml
<configuration>
    <!-- File Appender with Rolling Policy -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/application.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy">
            <fileNamePattern>logs/application.%i.log</fileNamePattern>
            <minIndex>1</minIndex>
            <maxIndex>5</maxIndex>
        </rollingPolicy>
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <maxFileSize>10MB</maxFileSize>
        </triggeringPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- Root Logger -->
    <root level="INFO">
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

**Explanation**:
- Logs are written to `logs/application.log`.
- A new log file is created when the size exceeds 10MB.
- A maximum of 5 log files are retained.

---

### Example 3: Multiple Appenders (Console and File)

```xml
<configuration>
    <!-- Console Appender -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- File Appender -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/application.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>logs/application.%d{yyyy-MM-dd}.log</fileNamePattern>
            <maxHistory>7</maxHistory>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- Root Logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
        <appender-ref ref="FILE" />
    </root>
</configuration>
```

**Explanation**:
- Logs are written to both the console and a rolling file.
- File logs are rotated daily, and logs are retained for 7 days.

---

### Example 4: Custom Logger for Specific Packages

```xml
<configuration>
    <!-- Console Appender -->
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <!-- Logger for a Specific Package -->
    <logger name="com.example.service" level="DEBUG">
        <appender-ref ref="CONSOLE" />
    </logger>

    <!-- Root Logger -->
    <root level="INFO">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

**Explanation**:
- Logs from the package `com.example.service` are set to `DEBUG` level.
- Other logs follow the root logger's `INFO` level.

---

### Example 5: Using MDC (Mapped Diagnostic Context)

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} [%X{requestId}] - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE" />
    </root>
</configuration>
```

**Explanation**:
- Adds the `requestId` value from the MDC to each log entry.
- Useful for tracking requests in distributed systems.

---

### Example 6: JSON Logging

```xml
<configuration>
    <appender name="JSON" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="net.logstash.logback.encoder.LogstashEncoder" />
    </appender>

    <root level="INFO">
        <appender-ref ref="JSON" />
    </root>
</configuration>
```

**Explanation**:
- Uses the `LogstashEncoder` to format logs as JSON.
- Useful for integration with ELK (Elasticsearch, Logstash, Kibana).

---

### Example 7: Environment-Specific Configuration (`logback-spring.xml`)

```xml
<configuration>
    <springProfile name="dev">
        <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
            <encoder>
                <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
            </encoder>
        </appender>
        <root level="DEBUG">
            <appender-ref ref="CONSOLE" />
        </root>
    </springProfile>

    <springProfile name="prod">
        <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
            <file>logs/application.log</file>
            <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
                <fileNamePattern>logs/application.%d{yyyy-MM-dd}.log</fileNamePattern>
                <maxHistory>30</maxHistory>
            </rollingPolicy>
            <encoder>
                <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
            </encoder>
        </appender>
        <root level="INFO">
            <appender-ref ref="FILE" />
        </root>
    </springProfile>
</configuration>
```

**Explanation**:
- Uses different configurations for `dev` and `prod` environments.
- Console logging is used in `dev`, while file logging is used in `prod`.

---

### Benefits of Custom Log Configuration

1. **Advanced Control**:
   - Tailor logging behavior for specific packages, classes, or environments.

2. **Improved Debugging**:
   - Include contextual data (e.g., request IDs) for better traceability.

3. **Integration**:
   - Use JSON or structured logging for compatibility with monitoring tools.

4. **Environment-Specific Behavior**:
   - Use different configurations for development, testing, and production.

