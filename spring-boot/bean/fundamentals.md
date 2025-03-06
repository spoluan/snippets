# Understanding Beans in Java Spring Boot

In Spring Boot, a **Bean** is an object that is instantiated, assembled, and managed by the Spring IoC (Inversion of Control) container. Beans are the core building blocks of Spring applications, as they encapsulate the business logic and collaborate with other Beans.

---

## What is a Bean?

A **Bean** in Spring is any object that is managed by the Spring IoC container. The container is responsible for the lifecycle of the Bean, including its creation, initialization, and destruction. Beans are typically used to define services, repositories, and other components that are required by your application.

---

## How to Define a Bean

There are two common ways to define a Bean in Spring Boot:

### Using `@Component`

The `@Component` annotation is used to mark a class as a Bean. Spring automatically scans for classes annotated with `@Component` and registers them in the application context.

```java
@Component
public class MyBean {
    public void sayHello() {
        System.out.println("Hello from MyBean!");
    }
}
```

To enable component scanning, your main application class should be annotated with `@SpringBootApplication`, which includes `@ComponentScan` by default.

### Using `@Bean`

The `@Bean` annotation is used in a configuration class (annotated with `@Configuration`) to explicitly declare a Bean. This is useful when you need to define Beans for third-party classes or when more control over Bean creation is required.

```java
@Configuration
public class MyConfig {

    @Bean
    public MyBean myBean() {
        return new MyBean();
    }
}
```

---

## Bean Scopes

Spring Beans can have different scopes, which determine their lifecycle and visibility. The most commonly used scopes are:

1. **Singleton** (Default): A single instance of the Bean is created and shared across the application context.
2. **Prototype**: A new Bean instance is created every time it is requested.
3. **Request**: A new Bean instance is created for each HTTP request (used in web applications).
4. **Session**: A new Bean instance is created for each HTTP session (used in web applications).
5. **Application**: A single Bean instance is created and shared across the entire application lifecycle.

You can specify the scope of a Bean using the `@Scope` annotation:

```java
@Component
@Scope("prototype")
public class PrototypeBean {
    // Prototype-scoped Bean
}
```

---

## Bean Lifecycle

The lifecycle of a Bean in Spring involves several stages:

1. **Instantiation**: The Bean is created by the Spring IoC container.
2. **Dependency Injection**: Dependencies are injected into the Bean.
3. **Initialization**: Custom initialization logic is executed (if defined).
4. **Usage**: The Bean is used in the application.
5. **Destruction**: Custom destruction logic is executed (if defined).

### Custom Initialization and Destruction Methods

You can define custom initialization and destruction methods for a Bean:

- Using annotations:
  - `@PostConstruct` for initialization.
  - `@PreDestroy` for destruction.

```java
@Component
public class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("MyBean is initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("MyBean is destroyed");
    }
}
```

- Using interfaces:
  - Implement `InitializingBean` for initialization.
  - Implement `DisposableBean` for destruction.

```java
@Component
public class MyBean implements InitializingBean, DisposableBean {

    @Override
    public void afterPropertiesSet() {
        System.out.println("MyBean is initialized");
    }

    @Override
    public void destroy() {
        System.out.println("MyBean is destroyed");
    }
}
```

---

## Dependency Injection

Beans can be injected into other Beans using Spring's **Dependency Injection** mechanism. Spring supports three types of injection:

1. **Constructor Injection** (preferred):
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

2. **Setter Injection**:
   ```java
   @Component
   public class MyService {

       private MyBean myBean;

       @Autowired
       public void setMyBean(MyBean myBean) {
           this.myBean = myBean;
       }
   }
   ```

3. **Field Injection** (not recommended):
   ```java
   @Component
   public class MyService {

       @Autowired
       private MyBean myBean;
   }
   ```

---

## Example Code

Here’s an example of a complete Spring Boot application with Beans:

```java
// MyBean.java
@Component
public class MyBean {
    public String getMessage() {
        return "Hello from MyBean!";
    }
}

// MyService.java
@Component
public class MyService {

    private final MyBean myBean;

    @Autowired
    public MyService(MyBean myBean) {
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
Hello from MyBean!
```
