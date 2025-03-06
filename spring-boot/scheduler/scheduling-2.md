### **Scheduling in Spring Boot**

Scheduling in Spring Boot allows you to automate tasks by running them at fixed intervals, after a delay, or using a cron expression. This is particularly useful for background jobs such as sending emails, cleaning up expired data, or generating reports.

When you enable scheduling, Spring automatically manages the execution of scheduled tasks based on the configuration you provide.

---

### **How to Enable Scheduling in Spring Boot**

1. **Add `@EnableScheduling` Annotation**
   - Add the `@EnableScheduling` annotation to your main application class or a configuration class to enable scheduling support.

   Example:
   ```java
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.scheduling.annotation.EnableScheduling;

   @SpringBootApplication
   @EnableScheduling
   public class BelajarSpringAsyncApplication {
       public static void main(String[] args) {
           SpringApplication.run(BelajarSpringAsyncApplication.class, args);
       }
   }
   ```

   In the provided example, scheduling is enabled in the `BelajarSpringAsyncApplication` class.

---

### **Defining Scheduled Tasks**

To define a scheduled task, annotate a method with `@Scheduled`. Spring will automatically run this method based on the configuration you specify in the annotation.

#### Example:
```java
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class Job {

    @Scheduled(fixedRate = 5000) // Runs every 5 seconds
    public void runJob() {
        System.out.println("Running job: " + System.currentTimeMillis());
    }
}
```

In this example:
- The `runJob` method will execute every 5 seconds.

---

### **Key Parameters for `@Scheduled`**

The `@Scheduled` annotation provides several options to configure the execution of tasks:

| **Parameter**         | **Description**                                                                                     |
|------------------------|-----------------------------------------------------------------------------------------------------|
| `fixedRate`           | Executes the method at a fixed interval, measured from the **start time** of the previous execution. |
| `fixedDelay`          | Executes the method at a fixed interval, measured from the **completion time** of the previous execution. |
| `initialDelay`        | Specifies the delay before the first execution.                                                     |
| `cron`                | Allows you to define a cron expression for complex scheduling.                                      |
| `timeUnit`            | Specifies the unit of time for `fixedRate` and `fixedDelay` (e.g., `SECONDS`, `MINUTES`).           |

---

### **Examples of Scheduling Configurations**

#### 1. **Fixed Rate**
```java
@Scheduled(fixedRate = 10000) // Runs every 10 seconds
public void fixedRateJob() {
    System.out.println("Fixed rate job executed: " + System.currentTimeMillis());
}
```

#### 2. **Fixed Delay**
```java
@Scheduled(fixedDelay = 5000) // Runs 5 seconds after the previous execution is complete
public void fixedDelayJob() {
    System.out.println("Fixed delay job executed: " + System.currentTimeMillis());
}
```

#### 3. **Initial Delay**
```java
@Scheduled(initialDelay = 2000, fixedRate = 5000) // Starts after 2 seconds, then runs every 5 seconds
public void initialDelayJob() {
    System.out.println("Initial delay job executed: " + System.currentTimeMillis());
}
```

#### 4. **Cron Expression**
```java
@Scheduled(cron = "0 * * * * *") // Executes at the start of every minute
public void cronJob() {
    System.out.println("Cron job executed: " + System.currentTimeMillis());
}
```

- **Cron Syntax**:
  ```
  second minute hour day-of-month month day-of-week
  ```

  Example:
  - `"0 0 12 * * *"`: Executes every day at 12:00 PM.
  - `"0 0/5 * * * *"`: Executes every 5 minutes.

---

### **Optional Elements in `@Scheduled`**

| **Element**          | **Type**   | **Description**                                                                 |
|-----------------------|------------|---------------------------------------------------------------------------------|
| `cron`               | `String`   | A cron expression for scheduling.                                              |
| `fixedDelay`         | `long`     | Delay (in milliseconds) between the completion of the last task and the start of the next. |
| `fixedRate`          | `long`     | Interval (in milliseconds) between the start of consecutive executions.         |
| `initialDelay`       | `long`     | Delay (in milliseconds) before the first execution.                            |
| `timeUnit`           | `TimeUnit` | Specifies time units for `fixedRate` and `fixedDelay`.                         |
| `zone`               | `String`   | Time zone for the cron expression.                                             |

---

### **Example Code: Scheduled Task**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.concurrent.atomic.AtomicLong;

@Component
public class Job {
    private static final Logger log = LoggerFactory.getLogger(Job.class);
    private AtomicLong atomicLong = new AtomicLong(0);

    @Scheduled(timeUnit = java.util.concurrent.TimeUnit.SECONDS, initialDelay = 2, fixedDelay = 2)
    public void runJob() {
        Long value = atomicLong.incrementAndGet();
        log.info("Run job {} on thread {}", value, Thread.currentThread().getName());
    }

    public Long getValue() {
        return atomicLong.get();
    }
}
```

- **Explanation**:
  - The task starts **2 seconds after the application starts** (`initialDelay = 2`).
  - It runs every **2 seconds after the previous task finishes** (`fixedDelay = 2`).
  - The `atomicLong` variable keeps track of how many times the job has run.

---

### **Testing Scheduled Tasks**

To test scheduled tasks, you can use a simple test case like this:

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class JobTest {

    @Autowired
    private Job job;

    @Test
    void testJob() throws InterruptedException {
        Thread.sleep(5000); // Wait for 5 seconds to allow the scheduled task to run
        assertEquals(2L, job.getValue()); // Verify the job ran twice
    }
}
```

---

### **How Scheduling Works Automatically**

- When the application starts, Spring scans for all beans annotated with `@Scheduled`.
- It registers these methods with a task scheduler.
- The task scheduler executes the methods based on the configured schedule.

