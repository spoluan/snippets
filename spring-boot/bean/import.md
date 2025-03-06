### **Understanding `@Import` in Spring Framework**

The `@Import` annotation in Spring allows you to import one or more configuration classes into another configuration class. This is particularly useful in complex applications where you want to modularize your Spring configurations by splitting them into multiple classes.

---

### **Key Points About `@Import`**

1. **Why `@Import`?**
   - In real-world applications, you often have multiple configuration classes instead of a single one. Using `@Import`, you can combine and manage these configurations efficiently.

2. **How Does It Work?**
   - You annotate a configuration class with `@Import` and specify the other configuration classes that should be included.
   - This allows Spring to load the beans defined in the imported configuration classes as part of the application context.

3. **Multiple Imports**
   - You can import more than one configuration class by listing them in the `value` attribute of `@Import`.

4. **Alternative to Component Scanning**
   - While component scanning (`@ComponentScan`) automatically detects beans, `@Import` gives you explicit control over which configuration classes to include.

---

### **Code Explanation**

#### **1. Configuration Class with `@Import`**
```java
@Configuration
@Import(value = {
    FooConfiguration.class,
    BarConfiguration.class
})
public class MainConfiguration {
}
```
- **`@Configuration`**: Marks this class as a Spring configuration class.
- **`@Import`**: Imports `FooConfiguration` and `BarConfiguration` classes, so their beans are included in the application context.

---

#### **2. Accessing Beans Using the Application Context**

```java
ConfigurableApplicationContext applicationContext =
    new AnnotationConfigApplicationContext(MainConfiguration.class);
applicationContext.registerShutdownHook();

Foo foo = applicationContext.getBean(Foo.class);
Bar bar = applicationContext.getBean(Bar.class);
```

- **`AnnotationConfigApplicationContext`**: Initializes the Spring application context with the specified configuration class (`MainConfiguration`).
- **`getBean()`**: Retrieves beans of type `Foo` and `Bar` from the application context.
- **`registerShutdownHook()`**: Ensures proper cleanup of resources when the application context is closed.

---

### **Alternative: Using `@Autowired` to Access Beans**

Instead of manually fetching beans from the application context, you can use **`@Autowired`** to inject beans directly into your classes. This is the preferred and more modern way to access beans in Spring.

#### **Example of `@Autowired`**

1. **Configuration Classes**
```java
@Configuration
public class FooConfiguration {
    @Bean
    public Foo foo() {
        return new Foo();
    }
}

@Configuration
public class BarConfiguration {
    @Bean
    public Bar bar() {
        return new Bar();
    }
}
```

2. **Main Configuration**
```java
@Configuration
@Import({FooConfiguration.class, BarConfiguration.class})
public class MainConfiguration {
}
```

3. **Service or Component**
```java
@Component
public class MyService {

    private final Foo foo;
    private final Bar bar;

    @Autowired
    public MyService(Foo foo, Bar bar) {
        this.foo = foo;
        this.bar = bar;
    }

    public void doSomething() {
        System.out.println("Using Foo: " + foo);
        System.out.println("Using Bar: " + bar);
    }
}
```

4. **Main Application**
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MyApp.class, args);

        MyService myService = context.getBean(MyService.class);
        myService.doSomething();
    }
}
```

---

### **Comparison: Manual Access vs `@Autowired`**

| **Aspect**                | **Manual Access**                              | **`@Autowired`**                       |
|---------------------------|-----------------------------------------------|----------------------------------------|
| **Code Style**            | Requires explicit calls to `getBean()`.       | Beans are injected automatically.      |
| **Flexibility**           | Useful for dynamic bean retrieval.            | Best for static dependencies.          |
| **Ease of Use**           | Requires more boilerplate code.               | Cleaner and more concise.              |
| **Preferred Use Case**    | When you need dynamic bean management.         | For most dependency injection needs.   |

---

### **When to Use `@Import`**

1. **Modular Configurations**:
   - If your application has multiple configuration classes, `@Import` helps you organize and combine them.

2. **Explicit Bean Inclusion**:
   - When you need precise control over which configurations to include in the application context.

3. **Integration with Legacy Code**:
   - Import existing configuration classes into a new configuration.
