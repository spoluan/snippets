### **Event Listener in Java Spring Boot**

In Spring Boot, the **Event Listener** is a powerful feature that facilitates **communication between different components** of the application using events. It is based on the **Observer Design Pattern**, where an event publisher sends events, and listeners act upon those events.

---

### **Key Concepts of Event Listener in Spring Boot**

1. **Event**:
   - An event is an object that represents something that has happened in the application.
   - In Spring, events are typically subclasses of `ApplicationEvent`.

2. **Listener**:
   - A listener is a component that listens for specific events and performs actions when those events occur.
   - It implements the `ApplicationListener<E>` interface or uses the `@EventListener` annotation.

3. **Publisher**:
   - The publisher is responsible for publishing events to the application context.
   - It uses the `ApplicationEventPublisher` to publish events.

---

### **How Event Listener Works in Spring Boot**

1. **Event Creation**:
   - Create a class that extends `ApplicationEvent` to represent the event.

2. **Event Publishing**:
   - Use an instance of `ApplicationEventPublisher` to publish the event.

3. **Event Listening**:
   - Implement the `ApplicationListener<E>` interface or use the `@EventListener` annotation to handle the event.

---

### **Detailed Steps with Code Examples**

#### **1. Event Creation**
Create a custom event class by extending `ApplicationEvent`.

```java
import org.springframework.context.ApplicationEvent;

public class LoginSuccessEvent extends ApplicationEvent {
    private final String username;

    public LoginSuccessEvent(String username) {
        super(username);
        this.username = username;
    }

    public String getUsername() {
        return username;
    }
}
```

---

#### **2. Event Listener**
Implement a listener that listens for the event. There are two ways to do this:

##### **a. Using `ApplicationListener` Interface**
```java
import org.springframework.context.ApplicationListener;
import org.springframework.stereotype.Component;

@Component
public class LoginSuccessListener implements ApplicationListener<LoginSuccessEvent> {

    @Override
    public void onApplicationEvent(LoginSuccessEvent event) {
        System.out.println("Login successful for user: " + event.getUsername());
    }
}
```

##### **b. Using `@EventListener` Annotation**
```java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class LoginSuccessListener {

    @EventListener
    public void handleLoginSuccessEvent(LoginSuccessEvent event) {
        System.out.println("Login successful for user: " + event.getUsername());
    }
}
```

---

#### **3. Event Publisher**
Use the `ApplicationEventPublisher` to publish the event.

```java
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final ApplicationEventPublisher eventPublisher;

    public UserService(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void login(String username, String password) {
        // Simulate a successful login
        if ("password123".equals(password)) {
            System.out.println("User logged in: " + username);
            // Publish login success event
            eventPublisher.publishEvent(new LoginSuccessEvent(username));
        } else {
            System.out.println("Login failed for user: " + username);
        }
    }
}
```

---

#### **4. Testing the Event**
You can test the event by calling the `login` method in the `UserService` and observing the listener’s behavior.

```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.context.ApplicationContext;
import org.springframework.stereotype.Component;

@Component
public class AppRunner implements CommandLineRunner {

    private final ApplicationContext applicationContext;

    public AppRunner(ApplicationContext applicationContext) {
        this.applicationContext = applicationContext;
    }

    @Override
    public void run(String... args) throws Exception {
        UserService userService = applicationContext.getBean(UserService.class);
        userService.login("john_doe", "password123"); // Successful login
        userService.login("jane_doe", "wrong_password"); // Failed login
    }
}
```

---

### **Advanced Features**

1. **Publishing Events Without Extending `ApplicationEvent`**:
   - Since Spring 4.2, it is no longer mandatory to extend `ApplicationEvent`. You can publish any object as an event.
   ```java
   eventPublisher.publishEvent("Simple String Event");
   ```

2. **Asynchronous Event Listeners**:
   - You can make event listeners asynchronous by annotating the method with `@Async`.
   - Requires enabling asynchronous processing with `@EnableAsync`.
   ```java
   @Component
   public class AsyncLoginSuccessListener {

       @Async
       @EventListener
       public void handleLoginSuccessEvent(LoginSuccessEvent event) {
           System.out.println("Async listener: Login successful for user: " + event.getUsername());
       }
   }
   ```

3. **Conditional Event Handling**:
   - Use the `@EventListener` annotation with the `condition` attribute to handle events conditionally.
   ```java
   @EventListener(condition = "#event.username == 'admin'")
   public void handleAdminLogin(LoginSuccessEvent event) {
       System.out.println("Admin logged in: " + event.getUsername());
   }
   ```

4. **ApplicationContext Events**:
   - Spring provides built-in events such as:
     - `ContextRefreshedEvent`: Triggered when the application context is refreshed.
     - `ContextClosedEvent`: Triggered when the application context is closed.
     - `ContextStartedEvent`: Triggered when the application context is started.
     - `ContextStoppedEvent`: Triggered when the application context is stopped.
   ```java
   @Component
   public class ContextEventListener {

       @EventListener
       public void handleContextRefreshed(ContextRefreshedEvent event) {
           System.out.println("Context refreshed!");
       }
   }
   ```

---

### **When to Use Event Listeners**

1. **Decoupling Components**:
   - Event listeners allow different components to communicate without tightly coupling them.

2. **Cross-Cutting Concerns**:
   - Use events to handle cross-cutting concerns like logging, auditing, or sending notifications.

3. **Asynchronous Processing**:
   - Offload tasks to an asynchronous listener to improve application performance.

---

### **Best Practices**

1. **Keep Listeners Lightweight**:
   - Avoid heavy processing in listeners to minimize delays in event handling.

2. **Use Asynchronous Listeners When Needed**:
   - Use `@Async` for long-running tasks to prevent blocking the main thread.

3. **Avoid Overusing Events**:
   - Use events judiciously. Overusing them can make the application harder to debug and maintain.

4. **Test Event Flows**:
   - Write unit tests to ensure events are published and handled correctly.
