### **Introduction to `@Value` in Spring Boot**

In Spring Boot, the `@Value` annotation is used for injecting values into fields, methods, or constructors. These values can come from a variety of sources, such as:

- **Application Properties**: `application.properties` or `application.yml`.
- **Environment Variables**: System environment variables.
- **Default Values**: Hardcoded fallback values.
- **Expressions**: SpEL (Spring Expression Language) expressions.

This mechanism is commonly referred to as **Value Injection** and is a core feature of Spring's dependency injection.

---

### **How Does `@Value` Work?**

1. **Syntax**:  
   The `@Value` annotation uses the syntax `${property.name}` to inject a value from the application's configuration or environment.

2. **Property Resolution**:  
   - If the property exists, its value is injected.
   - If the property does not exist and no default value is provided, an error will occur.

3. **Default Value**:  
   You can specify a default value using the `:` operator, like this:  
   `@Value("${property.name:defaultValue}")`.

---

### **Example Code Explanation**

The provided code demonstrates how to use `@Value` to inject values into fields from the `application.properties` file or environment variables.

```java
@SpringBootApplication
public static class TestApplication {

    @Component
    @Getter
    public static class ApplicationProperties {

        // Injecting a String property (e.g., application.name)
        @Value("${application.name}")
        private String name;

        // Injecting an Integer property (e.g., application.version)
        @Value("${application.version}")
        private Integer version;

        // Injecting a Boolean property (e.g., application.production-mode)
        @Value("${application.production-mode}")
        private boolean productionMode;
    }
}
```

---

### **Detailed Explanation**

1. **`@SpringBootApplication`**:
   - Marks the main application class and enables component scanning, auto-configuration, and property support.

2. **`@Component`**:
   - Marks the `ApplicationProperties` class as a Spring-managed bean, making it eligible for dependency injection.

3. **`@Getter`**:
   - A Lombok annotation that generates getter methods for the fields, simplifying boilerplate code.

4. **`@Value`**:
   - Injects values from the `application.properties` file, environment variables, or default values into the fields.

---

### **Additional Examples of `@Value` Usage**

#### **1. Injecting Simple Properties**

**`application.properties`**:
```properties
app.title=My Spring Application
app.version=1.0
app.debug=true
```

**Java Code**:
```java
@Component
public class AppConfig {

    @Value("${app.title}")
    private String title;

    @Value("${app.version}")
    private String version;

    @Value("${app.debug}")
    private boolean debug;

    public void printConfig() {
        System.out.println("Title: " + title);
        System.out.println("Version: " + version);
        System.out.println("Debug Mode: " + debug);
    }
}
```

**Output**:
```
Title: My Spring Application
Version: 1.0
Debug Mode: true
```

---

#### **2. Using Default Values**

If a property is missing, you can provide a default value:

```java
@Component
public class DefaultConfig {

    @Value("${missing.property:Default Value}")
    private String defaultValue;

    public void printDefaultValue() {
        System.out.println("Default Value: " + defaultValue);
    }
}
```

**Output**:
```
Default Value: Default Value
```

---

#### **3. Injecting System Environment Variables**

You can inject system environment variables directly:

```java
@Component
public class SystemEnvConfig {

    @Value("${JAVA_HOME}")
    private String javaHome;

    public void printJavaHome() {
        System.out.println("JAVA_HOME: " + javaHome);
    }
}
```

**Output** (depends on your system):
```
JAVA_HOME: /usr/lib/jvm/java-11-openjdk
```

---

#### **4. Injecting Nested Properties**

**`application.properties`**:
```properties
app.settings.theme=dark
app.settings.language=en
```

**Java Code**:
```java
@Component
public class NestedConfig {

    @Value("${app.settings.theme}")
    private String theme;

    @Value("${app.settings.language}")
    private String language;

    public void printSettings() {
        System.out.println("Theme: " + theme);
        System.out.println("Language: " + language);
    }
}
```

**Output**:
```
Theme: dark
Language: en
```

---

#### **5. Using `@Value` with Expressions**

You can use **Spring Expression Language (SpEL)** to perform operations or conditional logic:

```java
@Component
public class SpelExample {

    @Value("#{2 + 3}")
    private int sum;

    @Value("#{systemProperties['user.name']}")
    private String userName;

    @Value("#{T(java.lang.Math).random() * 100}")
    private double randomValue;

    public void printValues() {
        System.out.println("Sum: " + sum);
        System.out.println("User Name: " + userName);
        System.out.println("Random Value: " + randomValue);
    }
}
```

**Output** (example):
```
Sum: 5
User Name: johndoe
Random Value: 42.56
```

---

### **Best Practices with `@Value`**

1. **Avoid Hardcoding**:
   - Use properties or environment variables instead of hardcoding values in your code.

2. **Use Default Values**:
   - Always provide default values for optional properties to avoid runtime errors.

3. **Centralize Configuration**:
   - Use a dedicated configuration class to manage all properties in one place.

4. **Prefer `@ConfigurationProperties` for Complex Configurations**:
   - For managing multiple related properties, consider using `@ConfigurationProperties` instead of `@Value`.

