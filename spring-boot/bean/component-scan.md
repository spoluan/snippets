### **Understanding `@ComponentScan` in Spring Framework**

In Spring, the `@ComponentScan` annotation is used to automatically discover and register beans (components) in the application context. It works in conjunction with the `@Component` annotation and its variants (e.g., `@Service`, `@Repository`, `@Controller`) to enable Spring to manage these components.

---

### **Key Points About `@ComponentScan`**

1. **Purpose**:
   - It tells Spring where to look for components, configurations, and services to register them as beans in the application context.

2. **Default Behavior**:
   - If no explicit `@ComponentScan` is specified, Spring scans the package where the main application class resides and its sub-packages.

3. **Customizing the Scan**:
   - You can specify the base packages to scan or exclude/include specific classes or annotations.

4. **Annotations It Works With**:
   - `@Component`, `@Service`, `@Repository`, `@Controller`, `@RestController`, and `@Configuration`.

5. **Configuration Class**:
   - Typically used in a class annotated with `@Configuration` or in the main application class annotated with `@SpringBootApplication` (which includes `@ComponentScan` by default).

---

### **How `@ComponentScan` Works**

#### **Basic Example**

1. **Component Class**
```java
@Component
public class MyComponent {
    public void doSomething() {
        System.out.println("MyComponent is working!");
    }
}
```

2. **Configuration Class**
```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}
```

3. **Main Application**
```java
public class MainApp {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        MyComponent myComponent = context.getBean(MyComponent.class);
        myComponent.doSomething();
    }
}
```

---

### **Customizing `@ComponentScan`**

#### **1. Specifying Base Packages**

You can specify which packages to scan using the `basePackages` or `basePackageClasses` attributes.

```java
@ComponentScan(basePackages = {"com.example.package1", "com.example.package2"})
```

Alternatively, you can use `basePackageClasses` to specify classes, and Spring will scan the packages where those classes reside.

```java
@ComponentScan(basePackageClasses = {Class1.class, Class2.class})
```

---

#### **2. Excluding Classes**

Use the `excludeFilters` attribute to exclude specific classes or annotations from the scan.

```java
@ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Deprecated.class)
)
```
- This excludes all components annotated with `@Deprecated`.

---

#### **3. Including Specific Classes**

Use the `includeFilters` attribute to include only specific classes or annotations.

```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyCustomAnnotation.class),
    useDefaultFilters = false
)
```
- Setting `useDefaultFilters = false` disables the default behavior of scanning for `@Component` and its variants.

---

#### **4. Combining Include and Exclude Filters**

You can combine both `includeFilters` and `excludeFilters`.

```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE, classes = MyComponent.class),
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Deprecated.class)
)
```

---

#### **5. Using Custom Filters**

Spring supports several filter types:
- **ANNOTATION**: Filters by annotation.
- **ASSIGNABLE_TYPE**: Filters by class type.
- **ASPECTJ**: Filters using AspectJ expressions.
- **REGEX**: Filters using regular expressions.
- **CUSTOM**: Filters using a custom implementation of `TypeFilter`.

Example of a custom filter:
```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.CUSTOM, classes = MyCustomFilter.class)
)
public class AppConfig {
}

public class MyCustomFilter implements TypeFilter {
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) {
        // Custom logic to include/exclude classes
        return metadataReader.getClassMetadata().getClassName().contains("Specific");
    }
}
```

---

### **Variants of `@Component`**

The `@Component` annotation is a generic stereotype for any Spring-managed component. Spring provides specialized stereotypes for specific use cases:

1. **`@Service`**:
   - Used for service-layer components.
   - Example:
     ```java
     @Service
     public class MyService {
         public void execute() {
             System.out.println("Service executed!");
         }
     }
     ```

2. **`@Repository`**:
   - Used for data-access-layer components.
   - Example:
     ```java
     @Repository
     public class MyRepository {
         public void save() {
             System.out.println("Data saved!");
         }
     }
     ```

3. **`@Controller`**:
   - Used for web controllers in Spring MVC.
   - Example:
     ```java
     @Controller
     public class MyController {
         @RequestMapping("/hello")
         public String hello() {
             return "Hello, World!";
         }
     }
     ```

4. **`@RestController`**:
   - Combines `@Controller` and `@ResponseBody` for RESTful web services.
   - Example:
     ```java
     @RestController
     public class MyRestController {
         @GetMapping("/api/hello")
         public String hello() {
             return "Hello, REST!";
         }
     }
     ```

---

### **Default Behavior in Spring Boot**

In a Spring Boot application, the `@SpringBootApplication` annotation includes `@ComponentScan` by default. It scans the package where the main application class resides and all its sub-packages.

Example:
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

If you need to customize the scanning behavior in a Spring Boot application, you can override the default `@ComponentScan` like this:
```java
@SpringBootApplication
@ComponentScan(basePackages = "com.example.custom")
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }
}
```

---

### **Advanced Examples**

#### **Scanning Multiple Packages**
```java
@ComponentScan(basePackages = {"com.example.package1", "com.example.package2"})
```

#### **Disabling Default Scanning**
```java
@ComponentScan(basePackages = "com.example", useDefaultFilters = false)
```

#### **Scanning Specific Annotations**
```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = MyCustomAnnotation.class)
)
```

#### **Using AspectJ Expressions**
```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.ASPECTJ, pattern = "com.example..*Service")
)
```

#### **Using Regular Expressions**
```java
@ComponentScan(
    basePackages = "com.example",
    includeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = ".*Repository")
)
```
