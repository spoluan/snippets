 

### **1. Introduction: Why Use AOP?**

Imagine a scenario where your application has multiple methods, and you need to log information every time a method is executed. If the number of methods keeps increasing, what would you do?

- You would need to **manually add logging code** to each method, which is tedious and error-prone.
- Upon closer inspection, the **log messages are often repetitive**, simply displaying which method is being accessed.

This is where **Aspect-Oriented Programming (AOP)** comes to the rescue. AOP allows you to create an **Aspect** that applies to all methods of an object. With AOP, you only need to write the logging code **once** in the Aspect, and it will automatically handle logging for all methods.

---

### **2. Enabling AOP in Spring**

By default, AOP is not enabled in Spring. To activate it, you need to use the `@EnableAspectJAutoProxy` annotation in your configuration class.

#### **Steps to Enable AOP:**
1. Add `@EnableAspectJAutoProxy` to your Spring Boot application or configuration class.
2. This ensures that AOP proxies will be created for the beans where aspects are applied.

#### **Example: Enabling AOP**

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@SpringBootApplication
@EnableAspectJAutoProxy // Enable AOP
public class AopApplication {
    public static void main(String[] args) {
        SpringApplication.run(AopApplication.class, args);
    }
}
```

**Reference:** [Spring Documentation: EnableAspectJAutoProxy](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/EnableAspectJAutoProxy.html)

---

### **3. Example Without AOP**

Let’s start with a simple service class that performs a task (e.g., processing an order) and logs messages manually.

#### **OrderService.java (Without AOP)**

```java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class OrderService {
    public void placeOrder(String product, int quantity) {
        // Logging manually
        System.out.println("Logging: Order placement started...");
        
        // Business logic
        System.out.println("Placing order for product: " + product + ", quantity: " + quantity);
        
        // Logging manually
        System.out.println("Logging: Order placement completed.");
    }
}
```

#### **OrderController.java**

```java
package com.example.demo.controller;

import com.example.demo.service.OrderService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class OrderController {

    private final OrderService orderService;

    public OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @GetMapping("/order")
    public String placeOrder(@RequestParam String product, @RequestParam int quantity) {
        orderService.placeOrder(product, quantity);
        return "Order placed successfully!";
    }
}
```

#### **Output (Without AOP)**
When you call `/order?product=Laptop&quantity=2`, the console will show:
```
Logging: Order placement started...
Placing order for product: Laptop, quantity: 2
Logging: Order placement completed.
```

---

### **4. Refactoring Code Using AOP**

Now, let’s remove the manual logging and use AOP to handle it automatically.

#### **LoggingAspect.java (With AOP)**

```java
package com.example.demo.aspect;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.After;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    // Pointcut for all methods in the OrderService class
    @Before("execution(* com.example.demo.service.OrderService.*(..))")
    public void logBefore() {
        System.out.println("Logging: Method execution started...");
    }

    @After("execution(* com.example.demo.service.OrderService.*(..))")
    public void logAfter() {
        System.out.println("Logging: Method execution completed.");
    }
}
```

#### **OrderService.java (Refactored)**

```java
package com.example.demo.service;

import org.springframework.stereotype.Service;

@Service
public class OrderService {
    public void placeOrder(String product, int quantity) {
        // Business logic only
        System.out.println("Placing order for product: " + product + ", quantity: " + quantity);
    }
}
```

#### **Output (With AOP)**
When you call `/order?product=Laptop&quantity=2`, the console will show:
```
Logging: Method execution started...
Placing order for product: Laptop, quantity: 2
Logging: Method execution completed.
```

---

### **5. Advanced AOP Use Cases**

Here are all the possible examples of using AOP in Spring:

---

#### **1. Logging with `@Before` and `@After`**

```java
@Before("execution(* com.example.demo.service.*.*(..))")
@After("execution(* com.example.demo.service.*.*(..))")
```

---

#### **2. Performance Monitoring with `@Around`**

```java
@Around("execution(* com.example.demo.service.*.*(..))")
public Object monitorPerformance(ProceedingJoinPoint joinPoint) throws Throwable {
    long start = System.currentTimeMillis();
    Object result = joinPoint.proceed(); // Proceed with method execution
    long end = System.currentTimeMillis();
    System.out.println("Execution time: " + (end - start) + "ms");
    return result;
}
```

---

#### **3. Exception Handling with `@AfterThrowing`**

```java
@AfterThrowing(pointcut = "execution(* com.example.demo.service.*.*(..))", throwing = "exception")
public void handleException(Exception exception) {
    System.out.println("Exception caught: " + exception.getMessage());
}
```

---

#### **4. Method Execution Control with `@Around`**

```java
@Around("execution(* com.example.demo.service.*.*(..))")
public Object controlExecution(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("Before execution...");
    Object result = joinPoint.proceed(); // Proceed with method execution
    System.out.println("After execution...");
    return result;
}
```

---

#### **5. Accessing Method Arguments**

```java
@Before("execution(* com.example.demo.service.*.*(..)) && args(product, quantity)")
public void logArguments(String product, int quantity) {
    System.out.println("Product: " + product + ", Quantity: " + quantity);
}
```

---

#### **6. Custom Annotations**

##### **Define Custom Annotation**
```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Loggable {}
```

##### **Use Custom Annotation**
```java
@Loggable
public void placeOrder(String product, int quantity) {
    System.out.println("Placing order...");
}
```

##### **Aspect for Custom Annotation**
```java
@Before("@annotation(com.example.demo.annotation.Loggable)")
public void logCustomAnnotation() {
    System.out.println("Executing method annotated with @Loggable...");
}
```

---

#### **7. Combining Multiple Pointcuts**

```java
@Pointcut("execution(* com.example.demo.service.*.*(..))")
public void serviceMethods() {}

@Pointcut("execution(* com.example.demo.controller.*.*(..))")
public void controllerMethods() {}

@Before("serviceMethods() || controllerMethods()")
public void logServiceAndController() {
    System.out.println("Executing service or controller method...");
}
```

---

#### **8. Securing Methods**

```java
@Before("execution(* com.example.demo.service.*.*(..))")
public void checkAuthentication() {
    System.out.println("Checking authentication...");
    // Add authentication logic here
}
```
