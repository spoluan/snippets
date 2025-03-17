### **Synchronized in Lombok**

The `@Synchronized` annotation in Lombok simplifies thread-safe programming in Java. It is used to synchronize methods, ensuring that only one thread can access a critical section at a time, avoiding race conditions. This annotation provides a cleaner and more concise alternative to the traditional `synchronized` keyword or `Lock` objects.

---

### **Key Features of `@Synchronized`**

1. **Simplifies Synchronization**:
   - Automatically synchronizes a method or block of code without manually writing the `synchronized` keyword.

2. **Custom Lock Objects**:
   - Allows the use of custom lock objects for more granular control over synchronization.

3. **Thread Safety**:
   - Ensures that the annotated method is thread-safe.

4. **Avoids `this` Locking Issue**:
   - Unlike the traditional `synchronized` keyword, `@Synchronized` avoids locking on `this` (the current object), which can lead to deadlocks or unintended behavior. Instead, it uses a private lock object.

5. **Customizable Lock Name**:
   - You can specify a custom lock object by passing its name as a value to the annotation.

---

### **How `@Synchronized` Works**

When you annotate a method with `@Synchronized`, Lombok generates a private lock object (`$lock`) in the class, which is used to synchronize the method. If a custom lock object is specified, Lombok uses that instead.

---

### **Examples of `@Synchronized`**

#### **1. Basic Example**

##### **Code**:
```java
import lombok.Synchronized;

public class Counter {
    private long counter = 0;

    @Synchronized
    public void increment() {
        counter++;
    }

    @Synchronized
    public long getCounter() {
        return counter;
    }
}
```

##### **Generated Code**:
```java
public class Counter {
    private long counter = 0;
    private final Object $lock = new Object();

    public void increment() {
        synchronized ($lock) {
            counter++;
        }
    }

    public long getCounter() {
        synchronized ($lock) {
            return counter;
        }
    }
}
```

---

#### **2. Using a Custom Lock Object**

You can specify a custom lock object to synchronize multiple methods on the same lock.

##### **Code**:
```java
import lombok.Synchronized;

public class Counter {
    private final Object counterLock = new Object();
    private long counter = 0;

    @Synchronized("counterLock")
    public void increment() {
        counter++;
    }

    @Synchronized("counterLock")
    public long getCounter() {
        return counter;
    }
}
```

##### **Generated Code**:
```java
public class Counter {
    private final Object counterLock = new Object();
    private long counter = 0;

    public void increment() {
        synchronized (counterLock) {
            counter++;
        }
    }

    public long getCounter() {
        synchronized (counterLock) {
            return counter;
        }
    }
}
```

---

#### **3. Example with Multi-threaded Access**

This example demonstrates how `@Synchronized` ensures thread safety when multiple threads access a shared resource.

##### **Code**:
```java
import lombok.Synchronized;

public class Counter {
    private long counter = 0;

    @Synchronized
    public void increment() {
        counter++;
    }

    @Synchronized
    public long getCounter() {
        return counter;
    }
}

class CounterTest {
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        };

        Thread thread1 = new Thread(task);
        Thread thread2 = new Thread(task);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Final Counter: " + counter.getCounter());
    }
}
```

##### **Output**:
```
Final Counter: 2000
```

---

#### **4. Synchronizing Multiple Methods**

You can share a custom lock object between multiple methods to ensure that only one method can execute at a time.

##### **Code**:
```java
import lombok.Synchronized;

public class SharedResource {
    private final Object lock = new Object();

    @Synchronized("lock")
    public void methodA() {
        System.out.println("Method A");
    }

    @Synchronized("lock")
    public void methodB() {
        System.out.println("Method B");
    }
}
```

---

#### **5. Example with Complex Logic**

This example demonstrates using `@Synchronized` for more complex logic, such as managing a shared data structure.

##### **Code**:
```java
import lombok.Synchronized;
import java.util.ArrayList;
import java.util.List;

public class SharedList {
    private final List<String> list = new ArrayList<>();

    @Synchronized
    public void addItem(String item) {
        list.add(item);
    }

    @Synchronized
    public List<String> getItems() {
        return new ArrayList<>(list);
    }
}
```

---

### **Advantages of `@Synchronized`**

1. **Cleaner Code**:
   - Removes the need for manually writing `synchronized` blocks, making the code more readable.

2. **Avoids Common Pitfalls**:
   - Prevents locking on `this`, which can lead to unintended behavior or deadlocks.

3. **Custom Locking**:
   - Provides the ability to use custom lock objects for better control over synchronization.

4. **Thread Safety**:
   - Ensures that methods are thread-safe with minimal effort.

---

### **Limitations of `@Synchronized`**

1. **Limited to Method Synchronization**:
   - `@Synchronized` can only be applied to methods. For block-level synchronization, you still need to use the `synchronized` keyword.

2. **Generated Lock Object**:
   - Lombok generates a `$lock` object for synchronization. If you need fine-grained control over locking, you may need to define custom lock objects.

3. **Performance Overhead**:
   - Synchronization introduces a performance overhead, so it should only be used when necessary.

4. **Not Suitable for Static Methods**:
   - For static methods, Lombok generates a separate `$LOCK` object to avoid using `Class` as the lock, which may not always align with specific requirements.

---

### **Best Practices**

1. **Use Custom Lock Objects**:
   - Define custom lock objects for better control and to avoid unintended locking behavior.

2. **Minimize Synchronization**:
   - Synchronize only the critical sections of code to reduce performance overhead.

3. **Combine with Other Lombok Features**:
   - Use `@Synchronized` alongside other Lombok annotations like `@Getter` and `@Setter` for cleaner and more maintainable code.

4. **Avoid Over-synchronization**:
   - Synchronizing unnecessary methods can lead to performance bottlenecks.

---

### **Summary**

| **Feature**             | **Description**                                                                                   |
|--------------------------|---------------------------------------------------------------------------------------------------|
| **Annotation**           | `@Synchronized`                                                                                 |
| **Purpose**              | Simplifies synchronization for thread-safe methods.                                              |
| **Default Lock Object**  | Generates a private lock object (`$lock`) to avoid locking on `this`.                            |
| **Custom Lock**          | Allows specifying a custom lock object for synchronization.                                       |
| **Thread Safety**        | Ensures that only one thread can access the synchronized method at a time.                        |
| **Use Cases**            | Ideal for shared resources, counters, or any critical section in multi-threaded environments.    |

