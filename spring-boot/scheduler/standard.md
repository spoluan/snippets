### **Scheduler in Java Spring Boot**

Spring Boot provides a simple and powerful way to schedule tasks using the **Spring Scheduler**. Scheduling tasks is a common requirement in applications, such as executing periodic jobs, running background processes, or automating repetitive tasks. Spring Boot's scheduling capabilities are built on top of the `TaskScheduler` interface and the `@Scheduled` annotation.

---

### **Key Features of Spring Scheduler**
1. **Annotation-Based Scheduling:**  
   Use `@Scheduled` to define tasks that should run at fixed intervals or specific times.

2. **Cron Expressions:**  
   Schedule tasks using flexible cron expressions for precise timing.

3. **Fixed Rate and Fixed Delay:**  
   Run tasks at a fixed rate or with a delay between executions.

4. **Thread Pool Support:**  
   Use a thread pool to execute multiple scheduled tasks concurrently.

---

### **How to Enable Scheduling in Spring Boot**

To use the scheduler in Spring Boot, you need to enable it in your application by adding the `@EnableScheduling` annotation.

#### **Step 1: Add `@EnableScheduling`**
Add the annotation to your main application class or any configuration class:

```java
@SpringBootApplication
@EnableScheduling
public class SchedulerApplication {
    public static void main(String[] args) {
        SpringApplication.run(SchedulerApplication.class, args);
    }
}
```

---

### **Types of Scheduling in Spring Boot**

#### 1. **Fixed Rate Scheduling**
Runs a task at a fixed interval, regardless of the previous task's completion time.

```java
@Component
public class FixedRateScheduler {

    @Scheduled(fixedRate = 5000) // Runs every 5 seconds
    public void runAtFixedRate() {
        System.out.println("Fixed Rate Task: " + System.currentTimeMillis());
    }
}
```

- **`fixedRate`:** The task will run every 5 seconds, even if the previous execution is still running.

---

#### 2. **Fixed Delay Scheduling**
Runs a task with a fixed delay after the previous task's completion.

```java
@Component
public class FixedDelayScheduler {

    @Scheduled(fixedDelay = 5000) // Runs 5 seconds after the last task finishes
    public void runWithFixedDelay() {
        System.out.println("Fixed Delay Task: " + System.currentTimeMillis());
    }
}
```

- **`fixedDelay`:** The task will wait for 5 seconds after the previous execution finishes before starting again.

---

#### 3. **Cron Expression Scheduling**
Runs a task based on a cron expression. This provides more flexibility and precision.

```java
@Component
public class CronScheduler {

    @Scheduled(cron = "0 0/1 * * * ?") // Runs every minute
    public void runWithCron() {
        System.out.println("Cron Task: " + System.currentTimeMillis());
    }
}
```

- **Cron Expression Breakdown (`0 0/1 * * * ?`):**
  - `0`: Second (0th second of the minute)
  - `0/1`: Minute (every 1 minute)
  - `*`: Hour (every hour)
  - `*`: Day of month (every day)
  - `*`: Month (every month)
  - `?`: Day of week (any day)

---

### **Thread Pool for Scheduling**

By default, Spring Boot uses a single-threaded executor for scheduling tasks. If multiple tasks need to run concurrently, you can configure a thread pool.

#### **Step 1: Configure a Thread Pool**
Define a custom `TaskScheduler` bean:

```java
@Configuration
public class SchedulerConfig {

    @Bean
    public TaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        scheduler.setPoolSize(5); // Set the pool size
        scheduler.setThreadNamePrefix("Scheduler-");
        return scheduler;
    }
}
```

#### **Step 2: Use the Thread Pool**
Once the thread pool is configured, Spring Boot will automatically use it for scheduling tasks.

---

### **Dynamic Scheduling**

Sometimes, you may need to schedule tasks dynamically at runtime instead of using static `@Scheduled` annotations.

#### **Example: Dynamic Task Scheduling**
```java
@Component
public class DynamicScheduler {

    private final TaskScheduler taskScheduler;

    public DynamicScheduler(TaskScheduler taskScheduler) {
        this.taskScheduler = taskScheduler;
    }

    public void scheduleTask(Runnable task, long delay) {
        taskScheduler.schedule(task, new Date(System.currentTimeMillis() + delay));
    }
}
```

#### **Usage:**
```java
@RestController
public class SchedulerController {

    private final DynamicScheduler dynamicScheduler;

    public SchedulerController(DynamicScheduler dynamicScheduler) {
        this.dynamicScheduler = dynamicScheduler;
    }

    @GetMapping("/schedule")
    public String scheduleTask() {
        dynamicScheduler.scheduleTask(() -> System.out.println("Dynamic Task Executed"), 5000);
        return "Task Scheduled!";
    }
}
```

---

### **Exception Handling in Scheduled Tasks**

If an exception occurs in a scheduled task, it may stop further executions. To handle exceptions:

```java
@Component
public class SafeScheduler {

    @Scheduled(fixedRate = 5000)
    public void runSafely() {
        try {
            System.out.println("Safe Task Execution");
            // Simulate an exception
            if (Math.random() > 0.5) {
                throw new RuntimeException("Simulated Exception");
            }
        } catch (Exception e) {
            System.err.println("Error occurred: " + e.getMessage());
        }
    }
}
```

---

### **Real-World Examples**

#### 1. **Database Cleanup Task**
Run a task every night at midnight to clean up old records from the database.

```java
@Component
public class DatabaseCleanupTask {

    @Scheduled(cron = "0 0 0 * * ?") // Every day at midnight
    public void cleanDatabase() {
        System.out.println("Cleaning up database...");
        // Logic to clean up old records
    }
}
```

#### 2. **Email Notification Task**
Send email notifications every hour.

```java
@Component
public class EmailNotificationTask {

    @Scheduled(fixedRate = 3600000) // Every hour
    public void sendEmails() {
        System.out.println("Sending email notifications...");
        // Logic to send emails
    }
}
```

#### 3. **API Data Fetch Task**
Fetch data from an external API every 10 minutes.

```java
@Component
public class ApiDataFetchTask {

    @Scheduled(fixedDelay = 600000) // 10 minutes after the last execution
    public void fetchData() {
        System.out.println("Fetching data from API...");
        // Logic to fetch data
    }
}
```

---

### **Testing Scheduled Tasks**

Scheduled tasks can be tested by manually triggering the methods or by using Spring's testing support.

#### **Example: Manual Triggering**
```java
@SpringBootTest
class SchedulerTests {

    @Autowired
    private FixedRateScheduler scheduler;

    @Test
    void testScheduler() {
        scheduler.runAtFixedRate(); // Manually trigger the task
    }
}
```
