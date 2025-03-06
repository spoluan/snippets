 

---

## **1. What Are Bean Scopes?**

A **bean scope** defines how a bean is created, shared, and managed in a Spring application. Spring provides several built-in scopes, and you can also define custom scopes if needed.

---

## **2. Types of Bean Scopes**

### **A. Singleton Scope (Default)**

- **Definition:** A single instance of the bean is created and shared across the entire Spring application context.
- **Use Case:** Use for stateless beans or beans that are shared across the application (e.g., services, repositories).

#### **Example**
```java
@Configuration
public class AppConfig {
    @Bean
    public Foo foo() {
        return new Foo();
    }
}
```

#### **Usage**
```java
Foo foo1 = context.getBean(Foo.class);
Foo foo2 = context.getBean(Foo.class);

System.out.println(foo1 == foo2); // true (same instance)
```

- **Key Point:** The same instance of `Foo` is returned every time it is requested.

---

### **B. Prototype Scope**

- **Definition:** A new instance of the bean is created each time it is requested.
- **Use Case:** Use for stateful beans or beans that need to maintain their own state (e.g., non-thread-safe objects).

#### **Example**
```java
@Configuration
public class AppConfig {
    @Bean
    @Scope("prototype")
    public Foo foo() {
        return new Foo();
    }
}
```

#### **Usage**
```java
Foo foo1 = context.getBean(Foo.class);
Foo foo2 = context.getBean(Foo.class);

System.out.println(foo1 == foo2); // false (different instances)
```

- **Key Point:** Each request for the `Foo` bean results in a new instance.

---

### **C. Request Scope (Web Applications Only)**

- **Definition:** A new instance of the bean is created for each HTTP request.
- **Use Case:** Use for request-specific data, such as user session details or request metadata.

#### **Example**
```java
@Component
@Scope("request")
public class RequestScopedBean {
    // Fields and methods specific to a single HTTP request
}
```

#### **Usage**
In a web application, you can inject this bean into a controller:
```java
@RestController
public class MyController {

    @Autowired
    private RequestScopedBean requestScopedBean;

    @GetMapping("/test")
    public String testScope() {
        return "Request Scoped Bean: " + requestScopedBean.toString();
    }
}
```

- **Key Point:** A new instance is created for every HTTP request, and it is destroyed after the request is processed.

---

### **D. Session Scope (Web Applications Only)**

- **Definition:** A single instance of the bean is created and shared within an HTTP session.
- **Use Case:** Use for session-specific data, such as user preferences or shopping cart details.

#### **Example**
```java
@Component
@Scope("session")
public class SessionScopedBean {
    // Fields and methods specific to an HTTP session
}
```

#### **Usage**
In a web application:
```java
@RestController
public class MyController {

    @Autowired
    private SessionScopedBean sessionScopedBean;

    @GetMapping("/session")
    public String testScope() {
        return "Session Scoped Bean: " + sessionScopedBean.toString();
    }
}
```

- **Key Point:** The bean instance is tied to the user session and is destroyed when the session ends.

---

### **E. Application Scope (Web Applications Only)**

- **Definition:** A single instance of the bean is created and shared across the entire ServletContext.
- **Use Case:** Use for global data that needs to be shared across the application (e.g., application-wide settings).

#### **Example**
```java
@Component
@Scope("application")
public class ApplicationScopedBean {
    // Fields and methods shared across the application
}
```

#### **Usage**
```java
@RestController
public class MyController {

    @Autowired
    private ApplicationScopedBean applicationScopedBean;

    @GetMapping("/app")
    public String testScope() {
        return "Application Scoped Bean: " + applicationScopedBean.toString();
    }
}
```

- **Key Point:** The bean instance is shared across all requests and sessions within the application.

---

### **F. Custom Scope**

- **Definition:** You can define your own custom scope to handle specific lifecycle requirements.
- **Use Case:** Use when none of the built-in scopes fit your requirements.

#### **Example**

1. **Define a Custom Scope**
```java
public class CustomScope implements Scope {

    private Map<String, Object> scopedObjects = new HashMap<>();

    @Override
    public Object get(String name, ObjectFactory<?> objectFactory) {
        return scopedObjects.computeIfAbsent(name, k -> objectFactory.getObject());
    }

    @Override
    public Object remove(String name) {
        return scopedObjects.remove(name);
    }

    @Override
    public void registerDestructionCallback(String name, Runnable callback) {
        // Handle destruction callbacks if necessary
    }

    @Override
    public Object resolveContextualObject(String key) {
        return null;
    }

    @Override
    public String getConversationId() {
        return "custom";
    }
}
```

2. **Register the Custom Scope**
```java
@Configuration
public class AppConfig {

    @Bean
    public static CustomScopeConfigurer customScopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        configurer.addScope("custom", new CustomScope());
        return configurer;
    }

    @Bean
    @Scope("custom")
    public Foo foo() {
        return new Foo();
    }
}
```

- **Key Point:** Custom scopes allow you to manage the lifecycle of beans in a way that fits your specific needs.

---

## **3. Custom Scope Example: Doubleton Scope**

### **What Is Doubleton Scope?**
In a **Doubleton Scope**, there are exactly **two instances of the bean** created and managed by the scope. Each time the bean is requested, the scope alternates between these two instances in a round-robin fashion.

---

### **Implementation**

#### **1. Doubleton Scope Logic**

The `get` method alternates between two instances of the bean:
```java
@Override
public Object get(String name, ObjectFactory<?> objectFactory) {
    counter++;
    if (objects.size() == 2) {
        return objects.get(counter % 2); // Return one of the two instances
    } else {
        Object object = objectFactory.getObject();
        objects.add(object); // Add a new instance if fewer than two exist
        return object;
    }
}
```

The `remove` method ensures proper cleanup:
```java
@Override
public Object remove(String name) {
    if (objects.size() != 0) {
        return objects.remove(0); // Remove the first instance
    }
    return null;
}
```

#### **2. Complete Doubleton Scope Implementation**
```java
import org.springframework.beans.factory.ObjectFactory;
import org.springframework.beans.factory.config.Scope;

import java.util.ArrayList;
import java.util.List;

public class DoubletonScope implements Scope {

    private final List<Object> objects = new ArrayList<>();
    private int counter = -1;

    @Override
    public Object get(String name, ObjectFactory<?> objectFactory) {
        counter++;
        if (objects.size() == 2) {
            return objects.get(counter % 2);
        } else {
            Object object = objectFactory.getObject();
            objects.add(object);
            return object;
        }
    }

    @Override
    public Object remove(String name) {
        if (objects.size() != 0) {
            return objects.remove(0);
        }
        return null;
    }

    @Override
    public void registerDestructionCallback(String name, Runnable callback) {}

    @Override
    public Object resolveContextualObject(String key) {
        return null;
    }

    @Override
    public String getConversationId() {
        return "doubleton";
    }
}
```

---

#### **3. Registering the Doubleton Scope**
```java
@Configuration
public class AppConfig {

    @Bean
    public static CustomScopeConfigurer customScopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        configurer.addScope("doubleton", new DoubletonScope());
        return configurer;
    }

    @Bean
    @Scope("doubleton")
    public Foo foo() {
        return new Foo();
    }
}
```

---

#### **4. Testing the Doubleton Scope**
```java
public class Main {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        Foo foo1 = context.getBean(Foo.class);
        Foo foo2 = context.getBean(Foo.class);
        Foo foo3 = context.getBean(Foo.class);

        System.out.println(foo1); // Instance 1
        System.out.println(foo2); // Instance 2
        System.out.println(foo3); // Instance 1 (round-robin)
    }
}
```
 
