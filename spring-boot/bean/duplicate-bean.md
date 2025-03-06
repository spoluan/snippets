# Handling Duplicate Beans in Spring Boot

In Spring Boot, the Spring IoC (Inversion of Control) container manages Beans and their dependencies. However, there are scenarios where multiple Beans of the same type are defined in the application context, leading to conflicts or ambiguity when Spring attempts to inject a Bean. This document explains how to handle duplicate Beans in Spring Boot, why they occur, and strategies to resolve them.

---

## What Causes Duplicate Beans?

Duplicate Beans occur when two or more Beans of the same type are registered in the Spring application context. This can happen in the following scenarios:

1. **Multiple `@Component` Classes**: Two or more classes annotated with `@Component` implement or extend the same type.
2. **Multiple `@Bean` Methods**: Two or more `@Bean` methods in `@Configuration` classes return the same type.
3. **Third-Party Libraries**: A third-party library may register Beans of the same type as those defined in your application.
4. **Manual Bean Registration**: Beans are manually registered in the application context with the same type.

---

## The Problem with Duplicate Beans

When Spring encounters multiple Beans of the same type, it cannot determine which one to inject into a dependency. This leads to an exception such as:

```
NoUniqueBeanDefinitionException: No qualifying bean of type 'com.example.MyBean' available: expected single matching bean but found 2: beanOne,beanTwo
```

---

## Strategies to Resolve Duplicate Beans

### 1. Use `@Primary`

The `@Primary` annotation marks one Bean as the default Bean to be injected when multiple Beans of the same type exist. This is the simplest way to resolve ambiguity.

```java
@Component
@Primary
public class PrimaryBean implements MyBean {
    // This Bean will be injected by default
}
```

```java
@Component
public class SecondaryBean implements MyBean {
    // This Bean will not be injected unless explicitly specified
}
```

```java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(MyBean myBean) {
        this.myBean = myBean;
    }
}
```

In this case, `PrimaryBean` will be injected into `MyService`.

---

### 2. Use `@Qualifier`

The `@Qualifier` annotation allows you to specify the exact Bean to inject when multiple Beans of the same type exist. This approach provides more fine-grained control.

```java
@Component("beanOne")
public class BeanOne implements MyBean {
    // Bean implementation
}

@Component("beanTwo")
public class BeanTwo implements MyBean {
    // Bean implementation
}
```

```java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(@Qualifier("beanOne") MyBean myBean) {
        this.myBean = myBean;
    }
}
```

In this example, `BeanOne` will be injected into `MyService`.

---

### 3. Use `@Bean` with Names

When defining Beans using the `@Bean` annotation, you can give each Bean a unique name. This allows you to reference specific Beans during injection.

```java
@Configuration
public class MyConfig {

    @Bean(name = "beanOne")
    public MyBean beanOne() {
        return new BeanOne();
    }

    @Bean(name = "beanTwo")
    public MyBean beanTwo() {
        return new BeanTwo();
    }
}
```

```java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(@Qualifier("beanTwo") MyBean myBean) {
        this.myBean = myBean;
    }
}
```

Here, `beanTwo` will be injected into `MyService`.

---

### 4. Use `@Profile` for Environment-Specific Beans

The `@Profile` annotation allows you to define Beans that are only active in specific environments. This is useful when you want to include different Beans for different profiles (e.g., `dev`, `prod`).

```java
@Component
@Profile("dev")
public class DevBean implements MyBean {
    // Bean for development environment
}

@Component
@Profile("prod")
public class ProdBean implements MyBean {
    // Bean for production environment
}
```

You can activate a profile in your `application.properties` file:

```properties
spring.profiles.active=dev
```

---

### 5. Use Custom Annotations for Qualifiers

Instead of using `@Qualifier` directly, you can create custom annotations to make your code more readable and maintainable.

```java
@Qualifier
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.METHOD})
public @interface BeanOneQualifier {
}
```

```java
@Component
@BeanOneQualifier
public class BeanOne implements MyBean {
    // Bean implementation
}
```

```java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(@BeanOneQualifier MyBean myBean) {
        this.myBean = myBean;
    }
}
```

---

### 6. Use `Map` or `List` for Multiple Beans

If you want to inject all Beans of a certain type, you can use a `Map` or `List` to collect them.

```java
@Component
public class MyService {

    private final Map<String, MyBean> myBeans;

    @Autowired
    public MyService(Map<String, MyBean> myBeans) {
        this.myBeans = myBeans;
    }

    public void printBeans() {
        myBeans.forEach((name, bean) -> System.out.println(name + ": " + bean.getClass().getSimpleName()));
    }
}
```

This will inject all Beans of type `MyBean` into the `myBeans` map, where the keys are the Bean names and the values are the Bean instances.

---

### 7. Avoid Duplicate Beans

The best way to handle duplicate Beans is to avoid them altogether. Here are some tips:
- Ensure that only one `@Component` or `@Bean` definition exists for a given type.
- Use `@ComponentScan` carefully to avoid scanning the same package multiple times.
- Be mindful of third-party libraries that may register Beans automatically.

---

## Example Code

Here’s an example of resolving duplicate Beans using `@Primary` and `@Qualifier`:

```java
// PrimaryBean.java
@Component
@Primary
public class PrimaryBean implements MyBean {
    @Override
    public String getMessage() {
        return "Hello from PrimaryBean!";
    }
}

// SecondaryBean.java
@Component
public class SecondaryBean implements MyBean {
    @Override
    public String getMessage() {
        return "Hello from SecondaryBean!";
    }
}

// MyService.java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(@Qualifier("secondaryBean") MyBean myBean) {
        this.myBean = myBean;
    }

    public void printMessage() {
        System.out.println(myBean.getMessage());
    }
}

// DemoApplication.java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(DemoApplication.class, args);

        MyService myService = context.getBean(MyService.class);
        myService.printMessage();
    }
}
```

Output:
```
Hello from SecondaryBean!
```

