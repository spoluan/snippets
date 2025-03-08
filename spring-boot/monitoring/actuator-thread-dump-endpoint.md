### **Thread Dump Actuator Endpoint**

The `/actuator/threaddump` endpoint in Spring Boot Actuator provides a snapshot of all the threads currently running in the JVM. This is a valuable tool for diagnosing thread-related issues such as deadlocks, high CPU usage, or blocked threads.

---

### **What is a Thread Dump?**

1. **Definition**:
   - A thread dump is a snapshot of all the threads running in the JVM at a specific point in time.
   - It includes details about each thread’s state, stack trace, and other metadata.

2. **Purpose**:
   - To monitor and debug thread-related issues in a running application.
   - To identify blocked or waiting threads, thread contention, or deadlocks.

3. **Access**:
   - The endpoint is accessed at `/actuator/threaddump`.

---

### **How to Enable and Configure `/threaddump`**

#### **1. Add Actuator Dependency**

Include the Spring Boot Actuator dependency in your project.

**Maven:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**Gradle:**
```gradle
implementation 'org.springframework.boot:spring-boot-starter-actuator'
```

---

#### **2. Enable and Expose the `/threaddump` Endpoint**

By default, the `/threaddump` endpoint is enabled but not exposed. You need to configure your `application.properties` or `application.yml` to expose it.

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=threaddump
management.endpoint.threaddump.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: threaddump
  endpoint:
    threaddump:
      enabled: true
```

---

### **Accessing the `/threaddump` Endpoint**

Once configured, you can access the `/threaddump` endpoint at:
```
http://localhost:8080/actuator/threaddump
```

---

### **Features of the `/threaddump` Endpoint**

1. **Thread Information**:
   - Provides details about each thread, including:
     - Thread name
     - Thread ID
     - Thread state (`RUNNABLE`, `WAITING`, `BLOCKED`, etc.)
     - Whether the thread is a daemon
     - Whether the thread is suspended
     - Stack trace of the thread

2. **Deadlock Detection**:
   - Helps identify threads involved in deadlocks or waiting on locks.

3. **Performance Monitoring**:
   - Analyze threads consuming excessive CPU or waiting for long durations.

---

### **Sample Response**

When you access the `/threaddump` endpoint, it returns a JSON response with details about all the threads currently running in the JVM.

**Example Response:**
```json
{
  "threads": [
    {
      "threadName": "Reference Handler",
      "threadId": 9,
      "blockedTime": -1,
      "blockedCount": 4,
      "waitedTime": -1,
      "waitedCount": 0,
      "lockOwnerId": -1,
      "daemon": true,
      "inNative": false,
      "suspended": false,
      "threadState": "RUNNABLE",
      "priority": 10,
      "stackTrace": [
        {
          "moduleName": "java.base",
          "moduleVersion": "21",
          "methodName": "waitForReferencePendingList",
          "fileName": "Reference.java",
          "lineNumber": -2,
          "className": "java.lang.ref.Reference",
          "nativeMethod": true
        }
      ]
    },
    {
      "threadName": "main",
      "threadId": 1,
      "blockedTime": -1,
      "blockedCount": 0,
      "waitedTime": -1,
      "waitedCount": 0,
      "lockOwnerId": -1,
      "daemon": false,
      "inNative": false,
      "suspended": false,
      "threadState": "WAITING",
      "priority": 5,
      "stackTrace": [
        {
          "moduleName": "java.base",
          "moduleVersion": "21",
          "methodName": "park",
          "fileName": "Unsafe.java",
          "lineNumber": -1,
          "className": "jdk.internal.misc.Unsafe",
          "nativeMethod": true
        },
        {
          "moduleName": "java.base",
          "moduleVersion": "21",
          "methodName": "await",
          "fileName": "ConditionObject.java",
          "lineNumber": 162,
          "className": "java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject",
          "nativeMethod": false
        }
      ]
    }
  ]
}
```

---

### **Key Fields in the Response**

1. **Thread Metadata**:
   - `threadName`: The name of the thread.
   - `threadId`: Unique ID of the thread.
   - `daemon`: Whether the thread is a daemon thread.
   - `threadState`: The current state of the thread (`RUNNABLE`, `WAITING`, `BLOCKED`, etc.).
   - `priority`: The priority of the thread.

2. **Lock Information**:
   - `blockedTime`: Time spent blocked by other threads.
   - `blockedCount`: Number of times the thread was blocked.
   - `lockOwnerId`: ID of the thread holding the lock (if applicable).

3. **Stack Trace**:
   - `stackTrace`: The stack trace of the thread, showing the methods currently being executed.

---

### **When to Use the `/threaddump` Endpoint**

1. **Deadlock Detection**:
   - Identify threads involved in deadlocks and analyze their stack traces to resolve the issue.

2. **High CPU Usage**:
   - Inspect threads in the `RUNNABLE` state that may be consuming excessive CPU.

3. **Thread Contention**:
   - Identify threads waiting for locks or resources, causing performance bottlenecks.

4. **Debugging Application Freezes**:
   - Capture a thread dump when the application becomes unresponsive to identify threads causing the issue.

---

### **Best Practices**

1. **Secure the Endpoint**:
   - Restrict access to the `/threaddump` endpoint using Spring Security to prevent unauthorized access.

2. **Use in Non-Production Environments**:
   - Avoid exposing the `/threaddump` endpoint in production unless necessary for debugging.

3. **Capture Dumps During Issues**:
   - Trigger the `/threaddump` endpoint when the application exhibits performance issues or deadlocks to capture useful diagnostic data.

4. **Analyze Offline**:
   - Save the thread dump data and analyze it offline using tools like VisualVM or thread dump analyzers.

---

### **Advantages of Using `/threaddump`**

1. **Quick Insights**:
   - Provides a real-time snapshot of all threads in the JVM.

2. **Integrated with Actuator**:
   - Seamlessly integrates with other Actuator endpoints for comprehensive monitoring.

3. **JSON Format**:
   - The JSON response is easy to parse and analyze programmatically.

---

### **Limitations**

1. **Performance Impact**:
   - Capturing a thread dump may slightly impact the application’s performance.

2. **Not Continuous**:
   - The thread dump represents a single point in time and does not provide continuous monitoring.

3. **Sensitive Data**:
   - Thread dumps may expose sensitive information such as method names and stack traces. Ensure proper access controls are in place.

