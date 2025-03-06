
# **Asynchronous Programming in Spring Boot with Future and CompletableFuture**

Spring Boot provides powerful tools for handling asynchronous programming, enabling you to execute tasks in a non-blocking manner. Two commonly used interfaces for this purpose are `Future` and `CompletableFuture`.

---

## **What is `Future`?**

`Future` is a Java concurrency interface that represents the result of an asynchronous computation. It allows you to execute tasks asynchronously and retrieve their results at a later time.

### **Key Features of `Future`**
1. **Asynchronous Result**: Represents the result of a computation that may not yet be completed.
2. **Non-Blocking**: You can perform other operations while waiting for the result.
3. **Methods**:
   - `get()`: Blocks until the result is available.
   - `isDone()`: Checks if the computation is complete.
   - `cancel()`: Attempts to cancel the computation.
   - `isCancelled()`: Checks if the computation was canceled.

---

## **Using `Future` in Spring Boot**

Spring Boot simplifies working with `Future` by integrating it with the `@Async` annotation. You can use `@Async` to declare methods that return `Future` objects, allowing you to execute tasks asynchronously.

---

### **Basic Example of `Future`**

#### **1. Enable Async Support**

Add the `@EnableAsync` annotation to a configuration class to enable asynchronous processing.

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.EnableAsync;

@Configuration
@EnableAsync
public class AsyncConfig {
}
```

#### **2. Define an Asynchronous Service**

Create a service with an asynchronous method that returns a `Future`.

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.scheduling.annotation.AsyncResult;
import org.springframework.stereotype.Service;

import java.util.concurrent.Future;

@Service
public class AsyncService {

    @Async
    public Future<String> performAsyncTask() {
        try {
            // Simulate a long-running task
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return new AsyncResult<>("Task Completed");
    }
}
```

#### **3. Call the Asynchronous Method**

Invoke the asynchronous method and retrieve the result using `Future`.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

@Component
public class AsyncCaller {

    @Autowired
    private AsyncService asyncService;

    public void callAsyncTask() {
        System.out.println("Calling async task...");
        Future<String> future = asyncService.performAsyncTask();

        try {
            // Perform other tasks while waiting for the result
            System.out.println("Doing something else...");

            // Get the result (blocks until the task is complete)
            String result = future.get();
            System.out.println("Result from async task: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

---

### **Advanced Features of `Future`**

#### **1. Handling Timeout**
Use the overloaded `get()` method with a timeout to prevent indefinite blocking.

```java
try {
    String result = future.get(2, TimeUnit.SECONDS);
    System.out.println("Result: " + result);
} catch (TimeoutException e) {
    System.out.println("Task timed out!");
}
```

#### **2. Canceling a Task**
Cancel a task if it's taking too long.

```java
if (!future.isDone()) {
    future.cancel(true);
    System.out.println("Task was canceled");
}
```

#### **3. Combining Multiple `Future` Results**
Execute multiple asynchronous tasks and combine their results.

```java
Future<String> future1 = asyncService.performAsyncTask();
Future<String> future2 = asyncService.performAsyncTask();

String result1 = future1.get();
String result2 = future2.get();

System.out.println("Result 1: " + result1);
System.out.println("Result 2: " + result2);
```

---

## **Hello Future Example**

This example demonstrates how to use `CompletableFuture` with the `@Async` annotation to perform an asynchronous task.

### **Code**

```java
import lombok.SneakyThrows;
import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

import java.time.Duration;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.Future;

@Slf4j
@Component
public class HelloAsync {

    @Async
    @SneakyThrows
    public Future<String> hello(final String name) {
        CompletableFuture<String> future = new CompletableFuture<>();
        
        // Simulate a delay of 2 seconds
        Thread.sleep(Duration.ofSeconds(2));
        
        // Complete the future with a message
        future.complete("Hello " + name + " from Thread " + Thread.currentThread());
        
        return future;
    }
}
```

### **Caller Code**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

@Component
public class HelloCaller {

    @Autowired
    private HelloAsync helloAsync;

    public void callHello() {
        System.out.println("Calling async hello...");

        // Call the asynchronous method
        Future<String> future = helloAsync.hello("John");

        try {
            // Perform other tasks while waiting for the result
            System.out.println("Doing something else...");
            
            // Retrieve the result (blocks until the task is complete)
            String result = future.get();
            System.out.println("Result: " + result);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
        }
    }
}
```

### **Expected Output**

```
Calling async hello...
Doing something else...
Result: Hello John from Thread Thread-1
```

---

## **What is `CompletableFuture`?**

While `Future` is useful, it has limitations:
- It cannot be explicitly completed.
- It does not support chaining or combining tasks easily.
- It lacks support for functional programming.

`CompletableFuture`, introduced in Java 8, overcomes these limitations and provides a more advanced alternative.

---

### **Key Features of `CompletableFuture`**

1. **Chaining**: Supports chaining multiple tasks using methods like `thenApply`, `thenAccept`, etc.
2. **Completion Handling**: Allows handling of success and failure scenarios.
3. **Manual Completion**: You can explicitly complete a `CompletableFuture`.

---

### **Example with `CompletableFuture`**

```java
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;

import java.util.concurrent.CompletableFuture;

@Service
public class CompletableFutureService {

    @Async
    public CompletableFuture<String> performAsyncTask() {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return CompletableFuture.completedFuture("Task Completed");
    }
}
```

---

## **When to Use `Future` vs `CompletableFuture`**

| **Feature**            | **Future**                          | **CompletableFuture**               |
|-------------------------|--------------------------------------|--------------------------------------|
| **Chaining**            | Not Supported                      | Supported                           |
| **Completion Handling** | Not Supported                      | Supported (`thenApply`, `thenRun`)  |
| **Manual Completion**   | Not Supported                      | Supported                           |
| **Asynchronous Support**| Basic                              | Advanced                            |

For most modern Spring Boot applications, **`CompletableFuture`** is the recommended choice due to its enhanced capabilities.
