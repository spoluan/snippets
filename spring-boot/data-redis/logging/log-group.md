### Log Group in Spring Boot

**Log Group** is a feature in Spring Boot that allows you to group multiple packages or loggers under a single name. This simplifies logging configuration when you want to apply the same logging level to multiple packages or classes without specifying each one individually.

---

### Key Features of Log Group

1. **Group Multiple Loggers**: Combine multiple packages or loggers into a single group for easier management.
2. **Simplified Configuration**: Configure logging levels for a group instead of individual packages.
3. **Dynamic Group Naming**: Create custom log groups based on your application structure.
4. **Built-in Groups**: Spring Boot provides predefined log groups like `web` and `sql` for common use cases.

---

### How to Use Log Group

To define a log group, you can use the `logging.group.<group-name>` property in your `application.properties` or `application.yml`.

#### Syntax:
```properties
logging.group.<group-name>=<package1>,<package2>,<package3>
```

Once a log group is defined, you can set its logging level using:
```properties
logging.level.<group-name>=<level>
```

---

### Example 1: Custom Log Group

#### `application.properties` Example:
```properties
# Define a custom log group named 'dbgroup'
logging.group.dbgroup=test,com.test,com.sevendi

# Set logging levels
logging.level.root=info
logging.level.dbgroup=warn

# File logging settings
logging.file.name=application.log
logging.logback.rollingpolicy.max-file-size=10KB
logging.logback.rollingpolicy.max-history=10
logging.logback.rollingpolicy.total-size-cap=1GB
```

**Explanation**:
- The log group `dbgroup` includes three packages: `test`, `com.test`, and `com.sevendi`.
- The logging level for the `dbgroup` group is set to `WARN`, while the root level is `INFO`.

---

### Example 2: Built-in Log Groups

Spring Boot comes with predefined log groups for common use cases:

| **Log Group Name** | **Loggers Included**                                                                                  |
|---------------------|------------------------------------------------------------------------------------------------------|
| `web`              | `org.springframework.core.codec`, `org.springframework.http`, `org.springframework.web`, etc.       |
| `sql`              | `org.springframework.jdbc.core`, `org.hibernate.SQL`, `org.jooq.tools.LoggerListener`, etc.         |

#### `application.properties` Example:
```properties
# Enable detailed logging for web-related components
logging.level.web=debug

# Enable SQL query logging
logging.level.sql=trace
```

**Explanation**:
- The `web` log group includes all loggers related to Spring Web components.
- The `sql` log group includes loggers for JDBC, Hibernate, and JOOQ.

---

### Example 3: Multiple Custom Log Groups

#### `application.yml` Example:
```yaml
logging:
  group:
    service: com.example.service,com.example.utils
    controller: com.example.controller
  level:
    root: info
    service: debug
    controller: warn
```

**Explanation**:
- `service` group includes `com.example.service` and `com.example.utils`.
- `controller` group includes `com.example.controller`.
- The logging level for `service` is set to `DEBUG`, while `controller` is set to `WARN`.

---

### Example 4: Using Log Groups with Rolling File Logging

You can combine log groups with rolling file logging for better log management.

#### `application.properties` Example:
```properties
# Define log groups
logging.group.business=com.example.business,com.example.operations
logging.group.api=com.example.api,com.example.web

# Set logging levels
logging.level.business=debug
logging.level.api=info

# Rolling file logging settings
logging.file.name=logs/application.log
logging.logback.rollingpolicy.file-name-pattern=logs/application-%d{yyyy-MM-dd}.%i.log
logging.logback.rollingpolicy.max-file-size=5MB
logging.logback.rollingpolicy.max-history=7
logging.logback.rollingpolicy.total-size-cap=50MB
```

**Explanation**:
- `business` group includes business-related packages, and `api` group includes API-related packages.
- Logs are rolled over after they reach 5MB, with a maximum of 7 files retained.

---

### Benefits of Log Groups

1. **Simplifies Configuration**: Instead of setting levels for each package, you can define a group and configure it once.
2. **Improves Readability**: Easier to understand and maintain logging configurations.
3. **Built-in Support**: Leverages predefined groups like `web` and `sql` for common use cases.
4. **Customizability**: Allows creating custom groups tailored to your application's structure.

---

### Use Cases for Log Groups

1. **Microservices**: Group logs by service layers (e.g., `service`, `controller`, `repository`).
2. **Debugging**: Temporarily increase logging levels for specific groups during troubleshooting.
3. **Performance Monitoring**: Enable detailed logs for SQL queries or web requests to monitor performance.
4. **Compliance**: Control logging levels for sensitive components to comply with data protection policies.

