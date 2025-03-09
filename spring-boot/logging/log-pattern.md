### Log Pattern in Spring Boot

**Log Pattern** refers to the format in which log messages are displayed in the console or written to files. By default, Spring Boot uses a predefined log pattern, but you can customize it to suit your needs. This customization allows you to include additional information, such as timestamps, thread names, log levels, logger names, and contextual data.

---

### Key Features of Log Pattern Customization

1. **Customizable Console and File Patterns**:
   - Use `logging.pattern.console` to customize the log format for the console.
   - Use `logging.pattern.file` to customize the log format for file-based logs.

2. **Dynamic Content**:
   - Include dynamic content like timestamps, thread names, log levels, and MDC (Mapped Diagnostic Context) values.

3. **Improved Readability**:
   - Format logs to make them easier to read and debug.

4. **Application Properties Support**:
   - Configure log patterns directly in `application.properties` or `application.yml`.

---

### Log Pattern Placeholders

Spring Boot uses **Logback** under the hood, so you can use Logback's placeholders to define your log patterns. Below are some commonly used placeholders:

| **Placeholder**      | **Description**                                                                 |
|-----------------------|---------------------------------------------------------------------------------|
| `%d{pattern}`         | Timestamp with a specified format (e.g., `%d{yyyy-MM-dd HH:mm:ss.SSS}`).        |
| `%thread`             | Name of the thread that logged the message.                                    |
| `%level`              | Log level (e.g., INFO, DEBUG, WARN, ERROR).                                    |
| `%logger{length}`     | Logger name. You can specify the length (e.g., `%logger{36}`).                  |
| `%msg`                | The actual log message.                                                        |
| `%n`                  | Newline character.                                                             |
| `%X{key}`             | Retrieves a value from the MDC (Mapped Diagnostic Context).                    |
| `%F`                  | File name where the log statement was written.                                 |
| `%L`                  | Line number where the log statement was written.                               |
| `%M`                  | Method name where the log statement was written.                               |

---

### How to Customize Log Patterns

You can customize log patterns in the `application.properties` or `application.yml` file using the following properties:

1. **Console Log Pattern**:
   ```properties
   logging.pattern.console=<pattern>
   ```

2. **File Log Pattern**:
   ```properties
   logging.pattern.file=<pattern>
   ```

---

### Examples of Log Pattern Customization

#### Example 1: Default Log Pattern for Console

```properties
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
```

**Explanation**:
- `%d{yyyy-MM-dd HH:mm:ss.SSS}`: Adds a timestamp with the format `yyyy-MM-dd HH:mm:ss.SSS`.
- `[%thread]`: Adds the thread name in square brackets.
- `%-5level`: Adds the log level (e.g., INFO, DEBUG) with a fixed width of 5 characters.
- `%logger{36}`: Adds the logger name, truncated to 36 characters.
- `%msg`: Adds the actual log message.
- `%n`: Adds a newline character.

---

#### Example 2: Adding MDC Context Information

```properties
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - [%X{requestId}] - %msg%n
```

**Explanation**:
- `%X{requestId}`: Adds the value of the `requestId` key from the MDC (useful for tracking requests in distributed systems).

---

#### Example 3: Custom File Log Pattern

```properties
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n
```

**Explanation**:
- This pattern is similar to the console log pattern but omits the milliseconds from the timestamp.

---

#### Example 4: Including File Name and Line Number

```properties
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} (%F:%L) - %msg%n
```

**Explanation**:
- `(%F:%L)`: Adds the file name and line number where the log message was generated (e.g., `(MyClass.java:42)`).

---

#### Example 5: Minimalistic Log Pattern

```properties
logging.pattern.console=%d{HH:mm:ss} %-5level - %msg%n
```

**Explanation**:
- A shorter log pattern that only includes the time, log level, and message.

---

#### Example 6: JSON Log Pattern for File

```properties
logging.pattern.file={"timestamp":"%d{yyyy-MM-dd'T'HH:mm:ss.SSSZ}","level":"%level","thread":"%thread","logger":"%logger","message":"%msg"}%n
```

**Explanation**:
- Format logs as JSON for easier integration with log management tools like ELK (Elasticsearch, Logstash, Kibana).

---

### Example Configuration in `application.yml`

```yaml
logging:
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n"
    file: "%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n"
  file:
    name: logs/application.log
```

---

### Advanced Use Case: Combining Log Pattern with Rolling File Logging

```properties
# Log patterns
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} %-5level [%thread] %logger{36} - %msg%n

# Rolling file logging settings
logging.file.name=logs/application.log
logging.logback.rollingpolicy.file-name-pattern=logs/application-%d{yyyy-MM-dd}.%i.log
logging.logback.rollingpolicy.max-file-size=10MB
logging.logback.rollingpolicy.max-history=7
logging.logback.rollingpolicy.total-size-cap=50MB
```

---

### Benefits of Customizing Log Patterns

1. **Improved Debugging**:
   - Add context-specific information (e.g., request IDs, thread names) to make logs more useful.
   
2. **Consistency**:
   - Define a consistent format for logs across different environments (development, staging, production).

3. **Integration with Log Management Tools**:
   - Use JSON or structured formats for easier parsing by tools like ELK or Splunk.

4. **Readability**:
   - Customize the format to make logs more readable for developers.

