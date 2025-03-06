### **Example of Using Spring Async**
 
---

### **1. Asynchronous Service: `HelloAsync`**

#### **Code:**
```java
@Slf4j
@Component
public class HelloAsync {

    @Async
    @SneakyThrows
    public void hello() {
        // Simulate a task that takes 2 seconds
        Thread.sleep(Duration.ofSeconds(2));
        log.info("hello after 2 seconds {}", Thread.currentThread());
    }
}
```

#### **Explanation:**
- **`@Async`**:
  - Marks the `hello()` method to run asynchronously in a separate thread.
- **`Thread.sleep(2 seconds)`**:
  - Simulates a delay of 2 seconds to mimic a time-consuming task.
- **`log.info()`**:
  - Logs the current thread name to show that the method is executed in a separate thread.
- **`@SneakyThrows`**:
  - Automatically handles checked exceptions like `InterruptedException`.

---

### **2. Test Class: `HelloAsyncTest`**

#### **Code:**
```java
@Slf4j
@SpringBootTest
class HelloAsyncTest {

    @Autowired
    private HelloAsync helloAsync;

    @Test
    void helloAsync() throws InterruptedException {
        // Call the async method 10 times in a loop
        for (int i = 0; i < 10; i++) {
            helloAsync.hello();
        }

        // Log after all async calls
        log.info("after call hello async");

        // Wait for a few seconds to ensure async tasks finish
        Thread.sleep(Duration.ofSeconds(3));
    }
}
```

#### **Explanation:**
- **`@SpringBootTest`**:
  - Indicates that this is a Spring Boot test class.
- **`@Autowired`**:
  - Injects the `HelloAsync` component.
- **Async Method Calls**:
  - The `hello()` method is called 10 times in a loop.
  - Each call runs asynchronously in a separate thread.
- **`log.info("after call hello async")`**:
  - Logs a message immediately after the async calls, showing that the main thread is not blocked.
- **`Thread.sleep(3 seconds)`**:
  - Allows time for the async tasks to complete before the test ends.

---

### **3. Console Output**

#### **Sample Output:**
```
main] p.async.HelloAsyncTest              : after call hello async
task-2] com.async.HelloAsync : hello after 2 seconds Thread[#39, task-2,5,main]
task-1] com.async.HelloAsync : hello after 2 seconds Thread[#38, task-1,5,main]
task-8] com.async.HelloAsync : hello after 2 seconds Thread[#45, task-8,5,main]
task-5] com.async.HelloAsync : hello after 2 seconds Thread[#42, task-5,5,main]
task-3] com.async.HelloAsync : hello after 2 seconds Thread[#40, task-3,5,main]
...
```

#### **Key Observations:**
1. **Main Thread Continues**:
   - The log message `after call hello async` is printed immediately after initiating the async calls, showing that the main thread is not blocked.
2. **Tasks Run in Separate Threads**:
   - Each `hello()` method call is executed in a separate thread (e.g., `task-1`, `task-2`, etc.).
3. **Tasks Complete Independently**:
   - The `hello()` logs appear after 2 seconds, confirming that tasks are running asynchronously.

---

### **Summary of the Example**

- **Objective**:
  - Demonstrates how to use `@Async` to execute methods asynchronously.
- **Key Features**:
  - Asynchronous execution with minimal configuration.
  - Non-blocking behavior of the main thread.
  - Parallel execution of multiple tasks in separate threads.
- **Use Case**:
  - Suitable for tasks like sending emails, processing files, or any long-running background operations.
 
