### **Ways to Access a Bean in Spring**

In Spring, beans are managed by the **Spring IoC (Inversion of Control) container**, and there are multiple ways to access these beans. Depending on the use case, you can retrieve and use beans programmatically or through dependency injection (DI). Below is a detailed explanation of all the ways to access beans in Spring.

---

### **1. Dependency Injection (Preferred Way)**

Dependency Injection (DI) is the most common and recommended way to access beans in Spring. The container automatically injects the required dependencies into your classes.

#### **Types of Dependency Injection**

1. **Constructor-Based Injection**
   - Dependencies are injected via the constructor.
   - Best for mandatory dependencies.
   - Promotes immutability and is the most recommended approach.

**Example**:
```java
@Component
public class MyService {
    private final MyRepository myRepository;

    @Autowired
    public MyService(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    public void performTask() {
        myRepository.saveData();
    }
}
```

2. **Setter-Based Injection**
   - Dependencies are injected via setter methods.
   - Useful for optional or configurable dependencies.

**Example**:
```java
@Component
public class MyService {
    private MyRepository myRepository;

    @Autowired
    public void setMyRepository(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    public void performTask() {
        myRepository.saveData();
    }
}
```

3. **Field-Based Injection**
   - Dependencies are injected directly into fields using `@Autowired`.
   - Simplest form but not recommended for production due to poor testability and lack of immutability.

**Example**:
```java
@Component
public class MyService {
    @Autowired
    private MyRepository myRepository;

    public void performTask() {
        myRepository.saveData();
    }
}
```

---

### **2. Accessing Beans Programmatically**

Sometimes you may need to access beans programmatically, such as in non-managed Spring classes or dynamic scenarios. This is done using the `ApplicationContext`.

#### **Steps to Access Beans Programmatically**
1. **Obtain the ApplicationContext**:
   - The `ApplicationContext` is the Spring container that manages all beans.
   - It can be obtained through configuration or injected into your class.

2. **Retrieve the Bean by Name or Type**:
   - Use `getBean()` method of `ApplicationContext`.

---

#### **Ways to Access Beans Programmatically**

1. **Using `ApplicationContext`**
   - The `ApplicationContext` interface provides methods to retrieve beans.

**Example**:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        MyService myService = context.getBean(MyService.class);
        myService.performTask();
    }
}
```

2. **Using `BeanFactory`**
   - `BeanFactory` is a lower-level container compared to `ApplicationContext`.
   - Use it for lightweight applications where you don't need advanced features like event handling.

**Example**:
```java
import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
    public static void main(String[] args) {
        BeanFactory factory = new ClassPathXmlApplicationContext("beans.xml");

        MyService myService = factory.getBean(MyService.class);
        myService.performTask();
    }
}
```

---

### **3. Using `@Autowired` in Non-Spring Classes**

If you need to access beans in a non-Spring-managed class (e.g., utility classes), you can use the following approaches:

1. **Manually Inject the ApplicationContext**
   - Use `@Autowired` to inject the `ApplicationContext` and retrieve beans programmatically.

**Example**:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

@Component
public class BeanAccessor {
    @Autowired
    private ApplicationContext context;

    public MyService getMyService() {
        return context.getBean(MyService.class);
    }
}
```

2. **Static ApplicationContext Holder**
   - Create a static utility class to hold the `ApplicationContext` and retrieve beans globally.

**Example**:
```java
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

@Component
public class ApplicationContextHolder {
    private static ApplicationContext context;

    @Autowired
    public ApplicationContextHolder(ApplicationContext applicationContext) {
        context = applicationContext;
    }

    public static <T> T getBean(Class<T> beanClass) {
        return context.getBean(beanClass);
    }
}

// Usage
MyService myService = ApplicationContextHolder.getBean(MyService.class);
```

---

### **4. Using `@Lookup` for Prototype Beans**

If you need to access a **prototype-scoped bean** dynamically, you can use the `@Lookup` annotation. This ensures that a new instance of the bean is created every time it is accessed.

**Example**:
```java
import org.springframework.beans.factory.annotation.Lookup;
import org.springframework.stereotype.Component;

@Component
public abstract class SingletonBean {
    public void usePrototypeBean() {
        PrototypeBean prototypeBean = getPrototypeBean();
        prototypeBean.doSomething();
    }

    @Lookup
    protected abstract PrototypeBean getPrototypeBean();
}
```

---

### **5. Using `@Bean` Method**

You can define and access beans directly in a configuration class using `@Bean`.

**Example**:
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyService();
    }
}

// Accessing the bean
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
MyService myService = context.getBean(MyService.class);
```

---

### **6. Using `@ComponentScan` and Stereotype Annotations**

Spring automatically detects and registers beans using annotations like `@Component`, `@Service`, `@Repository`, and `@Controller` when `@ComponentScan` is enabled.

**Example**:
```java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig { }

// Classes annotated with @Component, @Service, etc., are automatically registered.
```

---

### **7. Using JNDI (Java Naming and Directory Interface)**

For enterprise applications, you can access beans via JNDI. This is less common in modern Spring applications but can still be used for integration with external systems.

**Example**:
```java
import javax.naming.Context;
import javax.naming.InitialContext;

public class JndiExample {
    public static void main(String[] args) throws Exception {
        Context context = new InitialContext();
        MyService myService = (MyService) context.lookup("java:comp/env/myService");
        myService.performTask();
    }
}
```

---

### **8. Accessing Beans in Spring Boot**

Spring Boot simplifies bean access by automatically configuring the `ApplicationContext`. Beans can be accessed programmatically or through dependency injection.

**Example**:
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class Application implements CommandLineRunner {

    private final ApplicationContext context;

    public Application(ApplicationContext context) {
        this.context = context;
    }

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        MyService myService = context.getBean(MyService.class);
        myService.performTask();
    }
}
```

---

### **Summary of Ways to Access Beans**

| **Method**                     | **Description**                                                                                   | **Use Case**                                                                                      |
|---------------------------------|---------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Dependency Injection**        | Automatically injects beans via constructor, setter, or field.                                   | Preferred for most scenarios.                                                                    |
| **ApplicationContext**          | Programmatically retrieves beans using `getBean()`.                                              | Useful for dynamic or non-Spring-managed classes.                                                |
| **BeanFactory**                 | Lightweight container for retrieving beans.                                                      | Suitable for simple applications.                                                                |
| **Static ApplicationContext**   | Provides global access to beans via a static utility class.                                      | Useful for utility or legacy code.                                                               |
| **@Lookup**                     | Dynamically retrieves prototype beans.                                                           | Use when prototype beans are required in singleton beans.                                         |
| **@Bean Method**                | Defines beans in configuration classes.                                                          | Useful for manual bean creation.                                                                 |
| **Stereotype Annotations**      | Automatically registers beans using annotations like `@Component`, `@Service`, etc.             | Simplifies bean registration in Spring Boot or Spring applications.                              |
| **JNDI**                        | Accesses beans via JNDI for enterprise applications.                                              | Rarely used in modern Spring applications.                                                       |

---

### **Best Practices**
1. **Use Dependency Injection**:
   - Constructor-based DI is the most recommended approach.
2. **Avoid Programmatic Access**:
   - Prefer DI over programmatic access for better testability and cleaner code.
3. **Use `@Lookup` for Prototypes**:
   - Use `@Lookup` to handle prototype beans in singleton beans.
4. **Leverage Spring Boot Features**:
   - Use auto-configuration and dependency injection to simplify bean management.

  
### **Programmatic Access Using `ApplicationContext.getBean()`**

#### **Syntax**
The `ApplicationContext.getBean()` method allows you to retrieve a bean by:
1. **Bean Name**: Specify the name of the bean as a string.
2. **Bean Type**: Specify the class type of the bean to ensure type safety.

#### **Example**
```java
CustomerRepository normalCustomerRepository = 
    applicationContext.getBean("normalCustomerRepository", CustomerRepository.class);

CustomerRepository premiumCustomerRepository = 
    applicationContext.getBean("premiumCustomerRepository", CustomerRepository.class);
```

In this example:
- `applicationContext.getBean(String name, Class<T> requiredType)` is used to access the bean.
- The bean names (`normalCustomerRepository` and `premiumCustomerRepository`) are explicitly provided in the `getBean()` method.
- The type of the bean (`CustomerRepository`) ensures that the returned object is of the expected type.

---

### **When to Use This Approach**
1. **Dynamic Bean Selection**: When the bean to be retrieved is determined at runtime (e.g., based on user input or configuration).
2. **Non-Spring-Managed Classes**: When accessing beans from classes that are not managed by the Spring container.
3. **Multiple Beans of the Same Type**: When there are multiple beans of the same type, and you need to retrieve a specific one by its name.

---

### **Complete Example**

#### **Bean Definitions**
```java
@Component("normalCustomerRepository")
public class NormalCustomerRepository implements CustomerRepository {
    @Override
    public void saveCustomer() {
        System.out.println("Saving normal customer");
    }
}

@Component("premiumCustomerRepository")
public class PremiumCustomerRepository implements CustomerRepository {
    @Override
    public void saveCustomer() {
        System.out.println("Saving premium customer");
    }
}
```

#### **Accessing Beans**
```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        // Access beans by name
        CustomerRepository normalCustomerRepository = 
            context.getBean("normalCustomerRepository", CustomerRepository.class);
        normalCustomerRepository.saveCustomer();

        CustomerRepository premiumCustomerRepository = 
            context.getBean("premiumCustomerRepository", CustomerRepository.class);
        premiumCustomerRepository.saveCustomer();
    }
}
```

#### **Output**
```
Saving normal customer
Saving premium customer
```

---

### **Best Practices**
1. **Prefer `@Qualifier` for Dependency Injection**:
   - If possible, use `@Qualifier` to specify the bean by name during dependency injection.
   - Example:
     ```java
     @Autowired
     @Qualifier("normalCustomerRepository")
     private CustomerRepository customerRepository;
     ```

2. **Use `getBean()` Sparingly**:
   - Programmatic access to beans using `getBean()` should be limited to scenarios where dependency injection is not feasible.

3. **Ensure Bean Names Are Unique**:
   - To avoid conflicts, ensure that each bean has a unique name if multiple beans of the same type exist.

