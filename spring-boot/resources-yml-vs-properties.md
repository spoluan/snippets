 
# Spring Boot Configuration: `application.yml` vs `application.properties`

In Spring Boot, configuration files are essential for defining application settings such as database connections, server ports, and other environment-specific properties. Spring Boot supports two main formats for configuration files:

1. **`application.properties`**
2. **`application.yml`**

Both formats are supported out of the box, and Spring Boot automatically detects and loads them from the `src/main/resources` directory.

---

## **Using `application.yml`**

### Why Use `application.yml`?
- YAML is more structured and readable compared to `.properties`.
- It allows for hierarchical configurations, making it easier to manage complex settings.

### Example: `application.yml`
Here’s an example of how you can define your configuration in `application.yml`:

```yaml
spring:
  application:
    name: demo
  datasource:
    url: jdbc:postgresql://localhost:5432/db_interview
    username: postgres
    password: root
    driver-class-name: org.postgresql.Driver
server:
  port: 8080
```

### Benefits of `application.yml`
- **Readability**: The hierarchical structure is easier to read and manage.
- **Grouping**: Related properties are grouped together, which improves clarity.

---

## **Using `application.properties`**

If you prefer a flat structure, you can use `application.properties`. Here’s the equivalent configuration in `.properties` format:

```properties
spring.application.name=demo
server.port=8080

spring.datasource.url=jdbc:postgresql://localhost:5432/db_interview
spring.datasource.username=postgres
spring.datasource.password=root
spring.datasource.driver-class-name=org.postgresql.Driver
```

---

## **How Spring Boot Handles Configuration Files**

1. If both `application.yml` and `application.properties` are present, Spring Boot will load both files.
2. **Priority**: Properties in `application.yml` take precedence over those in `application.properties` if there are overlaps.
3. By default, Spring Boot looks for these files in the `src/main/resources` directory.

---

## **Switching Between the Two Formats**

You can use either `application.yml` or `application.properties` without any additional configuration. Simply place the file in the `src/main/resources` directory, and Spring Boot will automatically detect and use it.

---

## **Custom Configuration File Names or Locations**

If you want to use a custom file name or location, you can specify it using the `spring.config.name` or `spring.config.location` properties. For example:

### Custom File Name:
```properties
spring.config.name=my-config
```

### Custom File Location:
```properties
spring.config.location=/opt/config/my-config.yml
```

You can also pass these settings as command-line arguments:
```bash
java -jar myapp.jar --spring.config.name=my-config
java -jar myapp.jar --spring.config.location=/opt/config/my-config.yml
```
 
