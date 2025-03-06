
## **Introduction to Property Conversion in Spring Boot**

Property conversion in Spring Boot allows you to map configuration properties from files like `application.properties` or `application.yml` into Java objects. Spring Boot provides **default converters** for common data types and also allows you to define **custom converters** for more complex or custom types.

### **Why Use Property Conversion?**

1. **Type Safety:** Strongly typed configuration ensures no runtime errors due to incorrect data types.
2. **Custom Formats:** Simplify configuration by using custom formats (e.g., converting a single string into a complex object).
3. **Readability:** Make configuration files easier to read and maintain.
4. **Reusability:** Encapsulate conversion logic into reusable components.

---

## **Default Conversion in Spring Boot**

Spring Boot provides built-in conversion for the following types:

1. **Primitive Types:** `String`, `int`, `boolean`, `double`, etc.
2. **Collections:** `List`, `Set`, `Map`.
3. **Java Time API:** `Duration`, `Period`, `LocalDate`, `LocalDateTime`, etc.
4. **Special Types:** `DataSize`, `UUID`, `InetAddress`, etc.
5. **Enums:** Automatically converts string values to enum constants.

---

## **Custom Converters in Spring Boot**

When default conversion is not enough, you can create **custom converters** using:
1. **`@ConfigurationPropertiesBinding`:** For custom types in `@ConfigurationProperties`.
2. **`Converter` Interface:** Implement Spring's `Converter` interface for specific type conversions.
3. **`GenericConverter`:** For handling multiple types of conversions.
4. **Custom Logic in `@Value`:** Use `@Value` with custom parsing logic.

---

## **Examples of Property Conversion**

Let’s dive into examples of both **default conversions** and **custom converters**.

---

### **1. Default Conversion Examples**

#### **1.1 Primitive Types**

##### **application.properties**
```properties
app.name=MyApplication
app.version=1.0
app.enabled=true
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private String name;
    private double version;
    private boolean enabled;

    // Getters and Setters
}
```

---

#### **1.2 Collections**

##### **application.yml**
```yaml
app:
  servers:
    - server1.example.com
    - server2.example.com
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private List<String> servers;

    // Getters and Setters
}
```

---

#### **1.3 Duration**

Spring Boot supports `java.time.Duration` natively.

##### **application.properties**
```properties
app.timeout=30s
app.cache.expiration=5m
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.time.Duration;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private Duration timeout;
    private Duration cacheExpiration;

    // Getters and Setters
}
```

---

#### **1.4 DataSize**

Spring Boot supports `org.springframework.util.unit.DataSize` for file sizes.

##### **application.properties**
```properties
app.max-upload-size=10MB
app.buffer-size=512KB
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import org.springframework.util.unit.DataSize;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private DataSize maxUploadSize;
    private DataSize bufferSize;

    // Getters and Setters
}
```

---

#### **1.5 Enums**

Spring Boot can automatically map string values to enums.

##### **application.properties**
```properties
app.environment=PRODUCTION
```

##### **Enum Definition**
```java
public enum Environment {
    DEVELOPMENT,
    TESTING,
    PRODUCTION
}
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private Environment environment;

    // Getters and Setters
}
```

---

#### **1.6 Dates**

Spring Boot supports `java.time.LocalDate` and `java.time.LocalDateTime`.

##### **application.properties**
```properties
app.start-date=2025-03-06
app.end-date-time=2025-03-06T15:30:00
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.time.LocalDate;
import java.time.LocalDateTime;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private LocalDate startDate;
    private LocalDateTime endDateTime;

    // Getters and Setters
}
```

---

### **2. Custom Conversion Examples**

#### **2.1 Custom Object Conversion**

Suppose you want to convert a string property into a custom `Address` object.

##### **application.properties**
```properties
app.address=123 Main St, Springfield, USA
```

##### **Address Class**
```java
public class Address {
    private String street;
    private String city;
    private String country;

    public Address(String street, String city, String country) {
        this.street = street;
        this.city = city;
        this.country = country;
    }

    // Getters and toString()
}
```

##### **Custom Converter**
```java
import org.springframework.boot.context.properties.ConfigurationPropertiesBinding;
import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

@Component
@ConfigurationPropertiesBinding
public class AddressConverter implements Converter<String, Address> {

    @Override
    public Address convert(String source) {
        String[] parts = source.split(", ");
        return new Address(parts[0], parts[1], parts[2]);
    }
}
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private Address address;

    // Getters and Setters
}
```

---

#### **2.2 Range Conversion**

Convert a string like `10-20` into a custom `Range` object.

##### **application.properties**
```properties
app.range=10-20
```

##### **Range Class**
```java
public class Range {
    private int start;
    private int end;

    public Range(int start, int end) {
        this.start = start;
        this.end = end;
    }

    // Getters and toString()
}
```

##### **Custom Converter**
```java
import org.springframework.boot.context.properties.ConfigurationPropertiesBinding;
import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

@Component
@ConfigurationPropertiesBinding
public class RangeConverter implements Converter<String, Range> {

    @Override
    public Range convert(String source) {
        String[] parts = source.split("-");
        return new Range(Integer.parseInt(parts[0]), Integer.parseInt(parts[1]));
    }
}
```

##### **Java Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {
    private Range range;

    // Getters and Setters
}
```

---

#### **2.3 Using `GenericConverter`**

If you need to handle multiple custom conversions, use `GenericConverter`.

##### **Example**
```java
import org.springframework.core.convert.converter.GenericConverter;
import org.springframework.stereotype.Component;

import java.util.HashSet;
import java.util.Set;

@Component
public class GenericStringToObjectConverter implements GenericConverter {

    @Override
    public Set<ConvertiblePair> getConvertibleTypes() {
        Set<ConvertiblePair> pairs = new HashSet<>();
        pairs.add(new ConvertiblePair(String.class, Address.class));
        pairs.add(new ConvertiblePair(String.class, Range.class));
        return pairs;
    }

    @Override
    public Object convert(Object source, TypeDescriptor sourceType, TypeDescriptor targetType) {
        if (targetType.getType() == Address.class) {
            String[] parts = ((String) source).split(", ");
            return new Address(parts[0], parts[1], parts[2]);
        } else if (targetType.getType() == Range.class) {
            String[] parts = ((String) source).split("-");
            return new Range(Integer.parseInt(parts[0]), Integer.parseInt(parts[1]));
        }
        return null;
    }
}
```

---

### **Best Practices**

1. **Use `@ConfigurationProperties` for Hierarchical Configurations:**  
   This is ideal for grouping related properties.

2. **Validate Properties:**  
   Use `@Valid` and `@NotNull` to validate configurations.

3. **Encapsulate Conversion Logic:**  
   Use `Converter` or `GenericConverter` for reusable and testable conversion logic.



## **ConversionService in Spring**

The **`ConversionService`** is a core Spring interface for type conversion. It is used internally by Spring to convert properties from one type to another during property binding, such as when mapping configuration properties to Java objects.

### Why Register Converters with ConversionService?

1. **Custom Converters:**  
   If you create custom converters (e.g., for `@Value` injection or other use cases), they must be registered with the `ConversionService` to be recognized.

2. **Global Converters:**  
   You can register converters globally to make them available throughout the application.

3. **Custom Use Cases:**  
   For advanced scenarios where Spring’s default property binding is insufficient, you may need to customize the `ConversionService`.

---

## **How Spring Boot Handles Converters Automatically**

Spring Boot simplifies the process of registering converters for `@ConfigurationProperties` by using the `@ConfigurationPropertiesBinding` annotation. This annotation tells Spring Boot to automatically register the converter with a dedicated `ConversionService` for configuration properties.

However, for converters used outside `@ConfigurationProperties` (e.g., with `@Value`), you need to register them manually.

---

## **Registering Converters with ConversionService**

### **1. Automatic Registration for `@ConfigurationProperties`**

If you are using the `@ConfigurationPropertiesBinding` annotation, Spring Boot automatically registers the custom converter with the `ConversionService` used for configuration properties.

#### Example: Automatic Registration
```java
import org.springframework.boot.context.properties.ConfigurationPropertiesBinding;
import org.springframework.core.convert.converter.Converter;
import org.springframework.stereotype.Component;

@Component
@ConfigurationPropertiesBinding
public class AddressConverter implements Converter<String, Address> {
    @Override
    public Address convert(String source) {
        String[] parts = source.split(", ");
        return new Address(parts[0], parts[1], parts[2]);
    }
}
```

No manual registration is needed in this case.

---

### **2. Manual Registration with a Custom ConversionService**

If you want to use custom converters globally (e.g., for `@Value` injection or other use cases), you need to register them manually with the `ConversionService`.

#### **Steps to Register a Custom Converter**

1. **Create a Custom Converter**
   ```java
   import org.springframework.core.convert.converter.Converter;

   public class StringToRangeConverter implements Converter<String, Range> {
       @Override
       public Range convert(String source) {
           String[] parts = source.split("-");
           return new Range(Integer.parseInt(parts[0]), Integer.parseInt(parts[1]));
       }
   }
   ```

2. **Define a Custom ConversionService Bean**
   ```java
   import org.springframework.context.annotation.Bean;
   import org.springframework.context.annotation.Configuration;
   import org.springframework.core.convert.converter.Converter;
   import org.springframework.format.support.DefaultFormattingConversionService;

   @Configuration
   public class ConversionServiceConfig {

       @Bean
       public DefaultFormattingConversionService conversionService() {
           DefaultFormattingConversionService conversionService = new DefaultFormattingConversionService();
           // Register custom converters
           conversionService.addConverter(new StringToRangeConverter());
           return conversionService;
       }
   }
   ```

3. **Using the Custom ConversionService**
   Once registered, the `ConversionService` is used globally, including for `@Value` injection.

   ##### Example:
   ```java
   import org.springframework.beans.factory.annotation.Value;
   import org.springframework.stereotype.Component;

   @Component
   public class RangeConfig {

       @Value("${app.range}")
       private Range range;

       public Range getRange() {
           return range;
       }
   }
   ```

   ##### **application.properties**
   ```properties
   app.range=10-20
   ```

---

### **3. Using Spring's Configurable ConversionService**

If you want to customize the existing `ConversionService`, you can do so by overriding the default behavior.

#### Example:
```java
import org.springframework.context.annotation.Configuration;
import org.springframework.format.FormatterRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new StringToRangeConverter());
    }
}
```

This approach is useful for web-related conversions (e.g., when dealing with request parameters).

---

### **4. Using `ApplicationConversionService`**

Spring Boot 2.0 introduced the `ApplicationConversionService`, which is the default `ConversionService` used by the framework. You can customize it by adding your custom converters.

#### Example:
```java
import org.springframework.boot.convert.ApplicationConversionService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ConversionServiceConfig {

    @Bean
    public ApplicationConversionService applicationConversionService() {
        ApplicationConversionService conversionService = new ApplicationConversionService();
        conversionService.addConverter(new StringToRangeConverter());
        return conversionService;
    }
}
```

---

## **Complete Example: Custom Converter with Registration**

Let’s put it all together into a single example.

### **Scenario: Convert a String to a Custom Type (`Address`)**

#### **application.properties**
```properties
app.address=123 Main St, Springfield, USA
```

#### **Custom Type**
```java
public class Address {
    private String street;
    private String city;
    private String country;

    public Address(String street, String city, String country) {
        this.street = street;
        this.city = city;
        this.country = country;
    }

    // Getters and toString()
    @Override
    public String toString() {
        return "Address{" +
                "street='" + street + '\'' +
                ", city='" + city + '\'' +
                ", country='" + country + '\'' +
                '}';
    }
}
```

#### **Custom Converter**
```java
import org.springframework.core.convert.converter.Converter;

public class StringToAddressConverter implements Converter<String, Address> {
    @Override
    public Address convert(String source) {
        String[] parts = source.split(", ");
        return new Address(parts[0], parts[1], parts[2]);
    }
}
```

#### **Register the Converter**
```java
import org.springframework.boot.convert.ApplicationConversionService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ConversionServiceConfig {

    @Bean
    public ApplicationConversionService applicationConversionService() {
        ApplicationConversionService conversionService = new ApplicationConversionService();
        conversionService.addConverter(new StringToAddressConverter());
        return conversionService;
    }
}
```

#### **Using the Converter**
```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class AddressConfig {

    @Value("${app.address}")
    private Address address;

    public Address getAddress() {
        return address;
    }
}
```

#### **Main Application**
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class ConverterExampleApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConverterExampleApplication.class, args);
    }

    @Bean
    CommandLineRunner run(AddressConfig addressConfig) {
        return args -> {
            System.out.println("Address: " + addressConfig.getAddress());
        };
    }
}
```

---

## **Output**
```
Address: Address{street='123 Main St', city='Springfield', country='USA'}
```

---

## **Conclusion**

1. **Default Conversions:** Spring Boot supports many types like `Duration`, `DataSize`, `Enum`, and more out of the box.
2. **Custom Converters:** For custom types, you can create converters and register them with the `ConversionService`.
3. **Automatic Registration:** Use `@ConfigurationPropertiesBinding` for automatic registration with `@ConfigurationProperties`.
4. **Manual Registration:** For global or `@Value` use cases, register converters manually with the `ConversionService`. 
