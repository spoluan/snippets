### **Event Listener Annotation in Spring Boot**

The `@EventListener` annotation in Spring Boot provides a flexible and concise way to create event listeners. It is an alternative to implementing the `ApplicationListener` interface and simplifies the process of handling events in a Spring application.

---

### **Key Features of `@EventListener`**

1. **Annotation-Based Listener**:
   - Instead of implementing `ApplicationListener<E>`, you can annotate a method with `@EventListener` to handle specific events.
   - This makes the code more readable and less verbose.

2. **Flexibility**:
   - It supports handling multiple types of events, conditional event handling, and asynchronous processing.

3. **Automatic Registration**:
   - Spring automatically detects methods annotated with `@EventListener` and registers them as listeners.

---

### **How `@EventListener` Works**

1. **Bean Post Processor**:
   - The `@EventListener` annotation works with a **Bean Post Processor** called `EventListenerMethodProcessor`.
   - `EventListenerMethodProcessor` scans the Spring context for beans with methods annotated with `@EventListener`.

2. **Listener Registration**:
   - When a method with `@EventListener` is found, Spring automatically registers it as a listener in the application context using `ApplicationContext.addApplicationListener()`.

3. **Event Handling**:
   - When an event is published, Spring invokes the corresponding `@EventListener` method and passes the event object.

---

### **Basic Usage of `@EventListener`**

#### **1. Event Class**
Create a custom event class.

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
Use the `@EventListener` annotation to listen for the event.

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class UserListener {

    @EventListener(classes = LoginSuccessEvent.class)
    public void onLoginSuccessEvent(LoginSuccessEvent event) {
        log.info("Success login for user: {}", event.getUsername());
    }
}
```

- **`@EventListener(classes = EventClass.class)`**:
  - Specifies the type of event this method listens to.
  - If omitted, Spring infers the event type from the method's parameter.

---

#### **3. Event Publisher**
Publish the event using `ApplicationEventPublisher`.

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
        if ("password123".equals(password)) {
            System.out.println("User logged in: " + username);
            eventPublisher.publishEvent(new LoginSuccessEvent(username));
        } else {
            System.out.println("Login failed for user: " + username);
        }
    }
}
```

---

### **Advanced Features of `@EventListener`**

#### **1. Conditional Event Handling**
- Use the `condition` attribute to handle events conditionally.
- The condition is expressed as a SpEL (Spring Expression Language) expression.

```java
@EventListener(condition = "#event.username == 'admin'")
public void handleAdminLogin(LoginSuccessEvent event) {
    System.out.println("Admin logged in: " + event.getUsername());
}
```

---

#### **2. Asynchronous Event Listeners**
- Annotate the method with `@Async` to process the event asynchronously.
- Requires enabling asynchronous processing with `@EnableAsync`.

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

@Component
public class AsyncUserListener {

    @Async
    @EventListener
    public void handleLoginSuccessEvent(LoginSuccessEvent event) {
        System.out.println("Async listener: Login successful for user: " + event.getUsername());
    }
}
```

- **Steps to Enable Asynchronous Listeners**:
  - Add `@EnableAsync` to your configuration class.
  ```java
  import org.springframework.context.annotation.Configuration;
  import org.springframework.scheduling.annotation.EnableAsync;

  @Configuration
  @EnableAsync
  public class AsyncConfig {
  }
  ```

---

#### **3. Listening to Multiple Events**
A single method can listen to multiple types of events by specifying them in the `classes` attribute.

```java
@EventListener(classes = {LoginSuccessEvent.class, AnotherEvent.class})
public void handleMultipleEvents(Object event) {
    System.out.println("Event received: " + event);
}
```

---

#### **4. Handling Non-Application Events**
Since Spring 4.2, you can use `@EventListener` to handle any object as an event, not just subclasses of `ApplicationEvent`.

```java
@EventListener
public void handleCustomEvent(String event) {
    System.out.println("Custom event received: " + event);
}
```

---

### **How `@EventListener` Compares to `ApplicationListener`**

| Feature                       | `@EventListener`                     | `ApplicationListener`          |
|-------------------------------|---------------------------------------|---------------------------------|
| **Ease of Use**               | Simple and concise                   | Requires implementing an interface |
| **Flexibility**               | Supports multiple events and conditions | Handles only one event type    |
| **Asynchronous Support**      | Directly supports `@Async`           | Requires custom implementation |
| **Registration**              | Automatic                            | Manual (via interface)         |

---

### **How `EventListenerMethodProcessor` Works**

- `EventListenerMethodProcessor` is a Spring component responsible for scanning beans and registering methods annotated with `@EventListener`.
- It works as a **Bean Post Processor**:
  1. Detects beans with methods annotated with `@EventListener`.
  2. Automatically registers these methods as listeners in the `ApplicationContext`.

---

### **Best Practices for Using `@EventListener`**

1. **Keep Listener Methods Lightweight**:
   - Avoid heavy processing in listener methods. Offload long-running tasks to asynchronous methods.

2. **Use Conditional Event Handling**:
   - Use the `condition` attribute to filter events and reduce unnecessary processing.

3. **Leverage Asynchronous Listeners**:
   - For non-critical tasks, use `@Async` to process events in a separate thread.

4. **Avoid Overusing Events**:
   - Overuse of events can make the application harder to debug and maintain. Use them judiciously.

5. **Write Unit Tests**:
   - Ensure that events are published and listeners are triggered correctly by writing comprehensive tests.

