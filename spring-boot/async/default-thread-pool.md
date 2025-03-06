### **Default Thread Pool in Spring Boot for `@Async`**

When using the `@Async` annotation in Spring Boot, the framework automatically provides a default thread pool to manage the execution of asynchronous tasks. Here's an overview of how it works, how to customize it, and how the key parameters (`core-size`, `max-size`, `queue-capacity`) affect the behavior.

---

### **1. Default Behavior**

- **Automatic Thread Pool Creation**:
  - Spring Boot automatically creates a default thread pool when the `@Async` annotation is used.
  - This thread pool is managed by a `SimpleAsyncTaskExecutor` or a `TaskExecutor`.

- **Default Configuration**:
  - The default thread pool is basic and does not have advanced configurations like a fixed number of threads or a queue size.
  - It is suitable for simple use cases but may not perform well under high loads.

---

### **2. Customizing the Thread Pool**

You can customize the thread pool by configuring properties in the `application.properties` or `application.yml` file using the prefix `spring.task.execution`.

#### **Key Properties for Thread Pool Configuration**:

| Property                                     | Description                                                                 |
|---------------------------------------------|-----------------------------------------------------------------------------|
| `spring.task.execution.pool.core-size`      | The number of core threads in the pool (default: 8).                        |
| `spring.task.execution.pool.max-size`       | The maximum number of threads in the pool (default: `Integer.MAX_VALUE`).   |
| `spring.task.execution.pool.queue-capacity` | The capacity of the queue for tasks before new threads are created.         |
| `spring.task.execution.pool.allow-core-thread-timeout` | Whether core threads can time out (default: `true`).                      |
| `spring.task.execution.thread-name-prefix`  | The prefix for thread names (default: `task-`).                             |

---

#### **Example: Customizing Thread Pool in `application.properties`**
```properties
spring.task.execution.pool.core-size=5
spring.task.execution.pool.max-size=10
spring.task.execution.pool.queue-capacity=25
spring.task.execution.pool.allow-core-thread-timeout=true
spring.task.execution.thread-name-prefix=MyAsyncThread-
```

- **Explanation**:
  - `core-size`: Sets the minimum number of threads in the pool.
  - `max-size`: Limits the maximum number of threads.
  - `queue-capacity`: Specifies the size of the task queue.
  - `allow-core-thread-timeout`: Allows core threads to terminate if idle for too long.
  - `thread-name-prefix`: Makes it easier to identify threads in logs.

---

### **3. Advanced Customization with `@Bean`**

If the default configuration options are not sufficient, you can define a custom `TaskExecutor` bean in a configuration class.

#### **Example: Custom Thread Pool Configuration**
```java
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "customExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(10);
        executor.setQueueCapacity(25);
        executor.setThreadNamePrefix("CustomExecutor-");
        executor.initialize();
        return executor;
    }
}
```

- **Usage**:
  - Use the custom executor by specifying its name in the `@Async` annotation:
    ```java
    @Async("customExecutor")
    public void asyncMethod() {
        // Your async task
    }
    ```

---

### **4. How Thread Pool Parameters Affect Execution**

Let’s break down how the `core-size`, `max-size`, and `queue-capacity` parameters affect the behavior of the thread pool with a concrete example.

#### **Configuration Example:**
```properties
spring.task.execution.pool.core-size=8
spring.task.execution.pool.max-size=16
spring.task.execution.pool.queue-capacity=10
```

#### **Scenario:**
- You submit **50 tasks** to this thread pool.

---

#### **Step-by-Step Execution:**

1. **Initial Threads (Core Threads)**:
   - The thread pool starts with 8 core threads (`core-size=8`).
   - These 8 threads immediately start executing the first 8 tasks.

2. **Task Queue Filling**:
   - While the core threads are busy, the next 10 tasks are added to the task queue (`queue-capacity=10`).
   - At this point:
     - 8 tasks are being executed by the core threads.
     - 10 tasks are waiting in the queue.

3. **Creating Additional Threads**:
   - Once the queue is full, the thread pool starts creating additional threads (beyond the core size) to handle the remaining tasks.
   - It creates up to 8 more threads (to reach `max-size=16`).
   - These additional threads handle the next 8 tasks.

4. **Tasks Beyond Capacity**:
   - After the core threads (8) + additional threads (8) + queue (10) are all occupied, the thread pool has no more capacity to handle additional tasks.
   - The remaining 24 tasks are rejected with a `RejectedExecutionException` unless a custom rejection policy is defined.

---

#### **Diagram of Execution:**

1. **Core Threads**:
   ```
   Thread Pool: [Thread-1, Thread-2, ..., Thread-8]
   Tasks Executed: [Task-1, Task-2, ..., Task-8]
   ```

2. **Task Queue**:
   ```
   Task Queue: [Task-9, Task-10, ..., Task-18]
   ```

3. **Additional Threads**:
   ```
   Thread Pool: [Thread-9, Thread-10, ..., Thread-16]
   Tasks Executed: [Task-19, Task-20, ..., Task-26]
   ```

4. **Rejected Tasks**:
   ```
   Rejected Tasks: [Task-27, Task-28, ..., Task-50]
   ```

---

### **5. Why Customize the Default Thread Pool?**

#### **Benefits of Customization:**

1. **Performance Optimization**:
   - The default thread pool may not handle high concurrency efficiently, especially in production environments.
   - Customizing the thread pool allows you to optimize resource usage based on your application's needs.

2. **Thread Safety**:
   - Ensures that the thread pool size and behavior align with your application's workload.

3. **Logging and Debugging**:
   - Custom thread name prefixes make it easier to identify async threads in logs.

---

### **6. Handling Rejected Tasks**

If tasks are rejected because the thread pool is full, you can handle them gracefully by setting a custom rejection policy.

#### **Example: Custom Rejection Policy**
```java
executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
```

- **CallerRunsPolicy**:
  - The task is executed in the thread that submitted it (blocking the caller).
  
- **Other Policies**:
  - `AbortPolicy`: Throws a `RejectedExecutionException` (default behavior).
  - `DiscardPolicy`: Silently discards the rejected task.
  - `DiscardOldestPolicy`: Discards the oldest unhandled task in the queue.

---

### **7. Summary of Effects**

| Scenario                                  | What Happens                                                                 |
|------------------------------------------|-----------------------------------------------------------------------------|
| Tasks ≤ `core-size`                      | Handled by core threads immediately.                                        |
| Tasks > `core-size` but ≤ `core-size + queue-capacity` | Excess tasks are queued until core threads are free.                        |
| Tasks > `core-size + queue-capacity` but ≤ `max-size` | Additional threads are created to handle tasks beyond the queue.            |
| Tasks > `core-size + queue-capacity + max-size` | Tasks are rejected if no more threads or queue space is available.          |

---

### **8. Documentation Reference**

For more details, refer to the official Spring Boot documentation on thread pool configuration:  
[Spring Boot Task Execution Properties](https://docs.spring.io/spring-boot/appendix/application-properties/index.html#application-properties.core.spring.task.execution.pool.allow-core-thread-timeout)
 
