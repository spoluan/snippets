### **Customizing Spring Application**

Spring Boot provides the ability to customize the behavior of a Spring application before the **ApplicationContext** is created. This is particularly useful when you want to modify settings like disabling the banner, setting up profiles, or configuring additional properties programmatically.

---

### **1. Why Customize Spring Applications?**

There are scenarios where you might want to configure the application programmatically instead of relying solely on `application.properties` or `application.yml`. For example:
- Disabling the Spring Boot startup banner.
- Adding custom initialization logic.
- Configuring profiles or environment variables dynamically.
- Using alternative ways to build the application context.

---

### **2. Key Classes for Customization**

Spring Boot provides two main classes for customizing the application:

#### **a. `SpringApplication`**
- The main class used to bootstrap a Spring Boot application.
- Provides methods to configure the application before it starts.

#### **b. `SpringApplicationBuilder`**
- A fluent API alternative to `SpringApplication`.
- Useful for chaining multiple configurations in a more readable way.

#### **Documentation Links**:
- [`SpringApplication`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/SpringApplication.html)
- [`SpringApplicationBuilder`](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/builder/SpringApplicationBuilder.html)

---

### **3. Customizing with `SpringApplication`**

You can directly use the `SpringApplication` class to customize the application. Below is an example of how to do this:

#### **Code Example**:
```java
import org.springframework.boot.Banner;
import org.springframework.boot.SpringApplication;
import org.springframework.context.ConfigurableApplicationContext;

public class FooApplication {
    public static void main(String[] args) {
        // Create a SpringApplication instance
        SpringApplication application = new SpringApplication(FooApplication.class);

        // Disable the banner
        application.setBannerMode(Banner.Mode.OFF);

        // Run the application and get the context
        ConfigurableApplicationContext context = application.run(args);

        // Retrieve a bean from the context (if needed)
        Foo foo = context.getBean(Foo.class);
        System.out.println(foo);
    }
}
```

#### **Explanation**:
1. **Create a `SpringApplication` instance**:
   - This allows you to configure the application before running it.
2. **Disable the Banner**:
   - Use `application.setBannerMode(Banner.Mode.OFF)` to turn off the banner display.
3. **Run the Application**:
   - Call `application.run(args)` to start the application and create the `ApplicationContext`.
4. **Access Beans**:
   - You can retrieve beans from the application context using `context.getBean(Class)`.

---

### **4. Customizing with `SpringApplicationBuilder`**

You can also use the `SpringApplicationBuilder` for a more fluent configuration.

#### **Code Example**:
```java
import org.springframework.boot.Banner;
import org.springframework.boot.builder.SpringApplicationBuilder;

public class FooApplication {
    public static void main(String[] args) {
        new SpringApplicationBuilder(FooApplication.class)
            .bannerMode(Banner.Mode.OFF) // Disable the banner
            .run(args);
    }
}
```

#### **Key Differences**:
- `SpringApplicationBuilder` allows for method chaining, making the configuration more concise.
- It is particularly useful when you need to set multiple configurations at once.

---

### **5. Disabling the Banner**

As shown in the examples above, you can disable the banner using either:
- **`SpringApplication`**:
  ```java
  application.setBannerMode(Banner.Mode.OFF);
  ```
- **`SpringApplicationBuilder`**:
  ```java
  new SpringApplicationBuilder(FooApplication.class)
      .bannerMode(Banner.Mode.OFF)
      .run(args);
  ```

Alternatively, you can disable the banner in `application.properties`:
```properties
spring.main.banner-mode=off
```

---

### **6. Customizing the Application Context**

When customizing the application, you can also retrieve and manipulate the **`ApplicationContext`**. For example:
- Access beans programmatically.
- Perform additional initialization logic.

#### **Example**:
```java
ConfigurableApplicationContext context = application.run(args);
Foo foo = context.getBean(Foo.class);
System.out.println("Bean retrieved: " + foo);
```

---

### **7. Output Example**

When the above code is executed with the banner disabled, the console output will look like this:

```
21-10-07 23:10:02.867 INFO 41566 --- [main] p.s.core.application.FooApplication : Starting FooApplication
21-10-07 23:10:03.230 INFO 41566 --- [main] p.s.core.application.FooApplication : Started FooApplication
programmerzamannow.spring.core.data.Foo@56781d96
Process finished with exit code 0
```

- **No Banner**:
  - The startup banner is not displayed because it has been disabled.
- **Log Messages**:
  - Spring Boot logs the application startup and shutdown messages.
- **Bean Output**:
  - The `Foo` bean is retrieved and printed.

---

### **8. Summary**

#### **Why Customize?**
- To modify application behavior before the `ApplicationContext` is created.

#### **Key Methods**:
- **`SpringApplication`**:
  - Use `application.setBannerMode()` to control the banner.
  - Access the `ApplicationContext` after running the application.
- **`SpringApplicationBuilder`**:
  - Use a fluent API for chaining configurations.

#### **Common Use Cases**:
1. Disabling the banner.
2. Setting profiles programmatically.
3. Adding custom initialization logic.
4. Accessing and manipulating the `ApplicationContext`.

