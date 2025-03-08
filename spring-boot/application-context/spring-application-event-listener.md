### **Spring Application Events and Listeners**

Spring Boot provides a powerful event-driven mechanism to handle application lifecycle events. These events are triggered at various stages of the application lifecycle, such as startup, initialization, and shutdown. Developers can create custom listeners to respond to these events.

---

### **1. What Are Spring Application Events?**

Spring Application Events are events that are published during the lifecycle of a Spring Boot application. These events allow developers to hook into the application lifecycle and execute custom logic for specific stages.

#### **Key Points**:
- Events are triggered at predefined points in the application lifecycle.
- You can create listeners to handle these events.
- Some events are triggered **before** the `ApplicationContext` is fully initialized, so beans may not yet be available.

#### **Documentation**:
[SpringApplicationEvent Documentation](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/context/event/SpringApplicationEvent.html)

---

### **2. Common Spring Boot Events**

Here is a list of key events in the Spring Boot lifecycle:

| **Event**                          | **Description**                                           |
|-------------------------------------|-----------------------------------------------------------|
| **`ApplicationStartingEvent`**      | Triggered when the application starts.                   |
| **`ApplicationContextInitializedEvent`** | Triggered after the `ApplicationContext` is initialized. |
| **`ApplicationStartedEvent`**       | Triggered when the application has started.              |
| **`ApplicationFailedEvent`**        | Triggered when the application fails to start.           |
| **Others**                          | Additional events can be triggered for specific needs.   |

---

### **3. Adding Listeners**

You can create listeners to handle these events. There are two main ways to add listeners in Spring Boot:

#### **a. Using `ApplicationListener` Interface**
The `ApplicationListener` interface is used to create a listener for a specific event.

##### **Example**:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.context.event.ApplicationStartingEvent;
import org.springframework.context.ApplicationListener;

public class AppStartingListener implements ApplicationListener<ApplicationStartingEvent> {
    private static final Logger log = LoggerFactory.getLogger(AppStartingListener.class);

    @Override
    public void onApplicationEvent(ApplicationStartingEvent event) {
        log.info("Application is starting...");
    }
}
```

#### **b. Adding Listeners Programmatically**
Listeners can also be added programmatically when creating the `SpringApplication` instance.

##### **Example**:
```java
import org.springframework.boot.SpringApplication;

import java.util.List;

public class EventApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(EventApplication.class);

        // Add listeners programmatically
        application.setListeners(List.of(
            new AppStartingListener()
        ));

        application.run(args);
    }
}
```

---

### **4. Listener Lifecycle and Bean Availability**

- Some events, such as `ApplicationStartingEvent`, are triggered **before** the `ApplicationContext` is created. Beans are not yet available at this stage.
- For safety, listeners for early events should be added **programmatically** when creating the `SpringApplication` instance.

---

### **5. Example Workflow**

#### **Code: Creating a Listener**
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.context.event.ApplicationStartingEvent;
import org.springframework.context.ApplicationListener;

public class AppStartingListener implements ApplicationListener<ApplicationStartingEvent> {
    private static final Logger log = LoggerFactory.getLogger(AppStartingListener.class);

    @Override
    public void onApplicationEvent(ApplicationStartingEvent event) {
        log.info("Application is starting...");
    }
}
```

#### **Code: Adding Listener Programmatically**
```java
import org.springframework.boot.SpringApplication;

import java.util.List;

public class EventApplication {
    public static void main(String[] args) {
        SpringApplication application = new SpringApplication(EventApplication.class);

        // Add the listener
        application.setListeners(List.of(
            new AppStartingListener()
        ));

        application.run(args);
    }
}
```

#### **Output Example**:
When running the application, the listener logs:
```
INFO 12345 --- [main] AppStartingListener : Application is starting...
```

---

### **6. Summary**

#### **Key Concepts**:
- Spring Boot provides predefined events during the application lifecycle.
- You can create custom listeners using the `ApplicationListener` interface.
- Listeners for early events (e.g., `ApplicationStartingEvent`) should be added programmatically.

#### **Common Events**:
- **`ApplicationStartingEvent`**: Triggered at the very beginning of the application lifecycle.
- **`ApplicationContextInitializedEvent`**: Triggered after the `ApplicationContext` is initialized.
- **`ApplicationStartedEvent`**: Triggered after the application has started.
- **`ApplicationFailedEvent`**: Triggered if the application fails to start.

#### **Best Practices**:
- Use programmatic listeners for early events.
- Ensure listeners do not depend on beans if they are handling events triggered before the `ApplicationContext` is created.
