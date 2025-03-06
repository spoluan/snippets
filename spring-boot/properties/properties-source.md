### **Introduction to `@PropertySource` in Spring**

The `@PropertySource` annotation in Spring is used to load property files (e.g., `.properties` files) into the Spring Environment. By using this annotation, you can specify external property files that your application can use for configuration.

This is particularly useful when you want to separate configuration properties from your code to make it more maintainable and flexible.

---

### **How `@PropertySource` Works**

1. **Declaration**:
   - The `@PropertySource` annotation is used to specify the location of the property file.
   - The file can be located on the classpath or in an external directory.

2. **Integration with `@Value`**:
   - Once the property file is loaded, its key-value pairs can be injected into Spring components using the `@Value` annotation.

3. **Support for Multiple Files**:
   - You can load multiple property files by using the `@PropertySources` annotation.

---

### **Example Code Explanation**

The provided code demonstrates how to use `@PropertySource` to load a property file (`sample.properties`) and inject its values into fields using `@Value`.

#### **Code Example**
```java
@SpringBootApplication
@PropertySources({
    @PropertySource("classpath:/sample.properties") // Load the property file from the classpath
})
public static class TestApplication {

    @Component
    @Getter
    public static class SampleProperties {

        // Injecting a String property (e.g., sample.name)
        @Value("${sample.name}")
        private String name;

        // Injecting an Integer property (e.g., sample.version)
        @Value("${sample.version}")
        private Integer version;
    }
}
```

---

### **Detailed Explanation**

1. **`@SpringBootApplication`**:
   - Marks the main application class and enables auto-configuration, component scanning, and property support.

2. **`@PropertySource`**:
   - Specifies the location of the property file. In this case, `classpath:/sample.properties` indicates that the file is located in the application's classpath.

3. **`@PropertySources`**:
   - Used to specify multiple `@PropertySource` annotations when loading more than one property file.

4. **`@Component`**:
   - Marks the `SampleProperties` class as a Spring-managed bean, making it eligible for dependency injection.

5. **`@Value`**:
   - Injects values from the `sample.properties` file into the fields.

6. **`@Getter`**:
   - A Lombok annotation that generates getter methods for the fields.

---

### **Sample `sample.properties` File**

Here is an example of what the `sample.properties` file might look like:

```properties
sample.name=My Application
sample.version=1
```

---

### **Output Example**

If the `sample.properties` file contains the above values, the injected fields will hold the following values:

- `name`: `"My Application"`
- `version`: `1`

---

### **Multiple Property Files Example**

If you need to load multiple property files, you can use `@PropertySources`:

```java
@SpringBootApplication
@PropertySources({
    @PropertySource("classpath:/sample.properties"),
    @PropertySource("classpath:/extra.properties")
})
public static class TestApplication {

    @Component
    @Getter
    public static class MultiProperties {

        @Value("${sample.name}")
        private String sampleName;

        @Value("${extra.feature.enabled}")
        private boolean featureEnabled;
    }
}
```

**`extra.properties`**:
```properties
extra.feature.enabled=true
```

---

### **Default Values with `@Value`**

If a property is missing from the property file, you can provide a default value using `@Value`:

```java
@Component
public static class DefaultPropertyExample {

    @Value("${missing.property:Default Value}")
    private String defaultValue;

    public String getDefaultValue() {
        return defaultValue;
    }
}
```

---

### **Best Practices with `@PropertySource`**

1. **Use `application.properties` or `application.yml`**:
   - For most cases, Spring Boot automatically loads `application.properties` or `application.yml`. Use `@PropertySource` only when you need to load additional files.

2. **Organize Property Files**:
   - Keep different property files for different environments (e.g., `dev`, `prod`) to manage environment-specific configurations.

3. **Avoid Hardcoding Paths**:
   - Use `classpath:` or externalized configuration paths to make property files portable.

4. **Fallback to Defaults**:
   - Always provide default values for optional properties to avoid runtime errors.

