### Rolling File Logging in Spring Boot with Logback

**Rolling file logging** is a mechanism to manage log files effectively by limiting their size and number. When a log file reaches a certain size, it is archived (rolled) into a new file, allowing the application to continue logging without issues like disk space exhaustion or performance degradation. Spring Boot simplifies this process by providing built-in support for rolling file logging using **Logback**.

---

### Key Features of Rolling File Logging
1. **Automatic Archiving**: Automatically creates new log files when the current file reaches a specified size.
2. **Retention Policies**: Controls how many old log files to keep and the total disk space used by archived logs.
3. **Customizable Patterns**: Allows custom naming conventions for log files.
4. **Startup Cleanup**: Optionally cleans up old log files when the application starts.

---

### Configuration Properties for Rolling File Logging

Here are the key properties you can configure in `application.properties` or `application.yml`:

| **Property**                                      | **Description**                                                                 |
|---------------------------------------------------|---------------------------------------------------------------------------------|
| `logging.logback.rollingpolicy.file-name-pattern` | Defines the filename pattern for rolled log files.                             |
| `logging.logback.rollingpolicy.clean-history-on-start` | Cleans up old log archives on application startup.                             |
| `logging.logback.rollingpolicy.max-file-size`     | Sets the maximum size of a log file before it is archived.                     |
| `logging.logback.rollingpolicy.total-size-cap`    | Sets the total size limit for all archived log files.                          |
| `logging.logback.rollingpolicy.max-history`       | Specifies the maximum number of archived log files to retain (default is 7).   |

---

### Example Configuration in `application.properties`

```properties
# Log file location and name
logging.file.name=logs/application.log

# Rolling policy configurations
logging.logback.rollingpolicy.file-name-pattern=logs/application-%d{yyyy-MM-dd}.%i.log
logging.logback.rollingpolicy.max-file-size=10MB
logging.logback.rollingpolicy.total-size-cap=100MB
logging.logback.rollingpolicy.max-history=10
logging.logback.rollingpolicy.clean-history-on-start=true
```

**Explanation**:
- `logs/application-%d{yyyy-MM-dd}.%i.log`: The log files will be named with the date and an index (e.g., `application-2025-03-09.1.log`).
- `max-file-size=10MB`: Each log file will roll over after reaching 10MB.
- `total-size-cap=100MB`: The total size of all archived log files will not exceed 100MB.
- `max-history=10`: Keeps up to 10 archived log files.
- `clean-history-on-start=true`: Cleans old log files when the application starts.

---

### Example Configuration in `application.yml`

```yaml
logging:
  file:
    name: logs/application.log
  logback:
    rollingpolicy:
      file-name-pattern: logs/application-%d{yyyy-MM-dd}.%i.log
      max-file-size: 10MB
      total-size-cap: 100MB
      max-history: 10
      clean-history-on-start: true
```

---

### Example Code to Test Rolling File Logging

Below is a test case to generate log entries and observe the rolling behavior:

#### Code Example: `LoggingTest.java`

```java
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.TestPropertySource;

@Slf4j
@SpringBootTest
@TestPropertySource("classpath:application.properties")
public class LoggingTest {

    @Test
    void testLongLogging() {
        for (int i = 0; i < 100_000; i++) {
            log.warn("Hello World {}", i);
        }
    }
}
```

**Explanation**:
- This test generates 100,000 log entries.
- The log size will grow rapidly, triggering the rolling policy.
- Observe the log directory (`logs/`) to see the rolled log files.

---

### Advanced Logback Configuration (XML)

For more advanced scenarios, you can configure rolling policies directly in `logback-spring.xml`:

```xml
<configuration>
    <appender name="ROLLING" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>logs/application.log</file>
        <rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
            <fileNamePattern>logs/application-%d{yyyy-MM-dd}.%i.log</fileNamePattern>
            <maxFileSize>10MB</maxFileSize>
            <maxHistory>10</maxHistory>
            <totalSizeCap>100MB</totalSizeCap>
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%thread] %-5level %logger{36} - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="warn">
        <appender-ref ref="ROLLING" />
    </root>
</configuration>
```

---

### Key Points to Remember
1. **Performance**: Ensure rolling policies are set to avoid excessive disk usage without degrading performance.
2. **Retention Policies**: Adjust `max-history` and `total-size-cap` to balance between disk usage and log retention needs.
3. **Testing**: Always test rolling configurations in a staging environment before deploying to production.
4. **Log File Location**: Use absolute paths for log files in production to avoid issues with relative paths.

---

### Use Cases
1. **Debugging Production Issues**: Retain a limited number of historical logs for debugging.
2. **Disk Space Management**: Prevent logs from consuming excessive disk space.
3. **Compliance**: Maintain logs for a specific period to comply with regulations.
