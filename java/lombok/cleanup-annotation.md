### **`@Cleanup` in Lombok**

The `@Cleanup` annotation in Lombok is a feature designed to simplify the management of resources that need to be closed after use, such as file readers, database connections, or network sockets. It eliminates the need for manually writing `try-finally` blocks to ensure resources are properly closed, reducing boilerplate code and improving code readability.

---

### **Key Features of `@Cleanup`**

1. **Automatic Resource Management:**
   - Automatically calls the `close()` method on resources that implement the `AutoCloseable` or `Closeable` interface after they are no longer needed.

2. **Simplifies Code:**
   - Removes the need for explicit `try-finally` blocks to close resources.

3. **Thread-Safe:**
   - Ensures proper resource cleanup even in case of exceptions, preventing resource leaks.

4. **Customizable Cleanup Method:**
   - By default, `@Cleanup` calls the `close()` method, but it can be configured to call a different cleanup method if required.

---

### **How `@Cleanup` Works**

#### **1. Basic Example**

##### **Code Without `@Cleanup`:**
```java
import java.io.FileReader;
import java.io.IOException;
import java.util.Scanner;

public class FileHelper {
    public static String loadFile(String file) throws IOException {
        FileReader fileReader = null;
        Scanner scanner = null;

        try {
            fileReader = new FileReader(file);
            scanner = new Scanner(fileReader);

            StringBuilder builder = new StringBuilder();
            while (scanner.hasNextLine()) {
                builder.append(scanner.nextLine()).append("\n");
            }
            return builder.toString();
        } finally {
            if (scanner != null) {
                scanner.close();
            }
            if (fileReader != null) {
                fileReader.close();
            }
        }
    }
}
```

##### **Code With `@Cleanup`:**
```java
import lombok.Cleanup;

import java.io.FileReader;
import java.util.Scanner;

public class FileHelper {
    public static String loadFile(String file) throws Exception {
        @Cleanup FileReader fileReader = new FileReader(file);
        @Cleanup Scanner scanner = new Scanner(fileReader);

        StringBuilder builder = new StringBuilder();
        while (scanner.hasNextLine()) {
            builder.append(scanner.nextLine()).append("\n");
        }
        return builder.toString();
    }
}
```

---

### **Generated Code**

When using `@Cleanup`, Lombok transforms the annotated code into equivalent `try-finally` blocks. For example:

##### **Input Code:**
```java
@Cleanup FileReader fileReader = new FileReader(file);
```

##### **Generated Code:**
```java
FileReader fileReader = new FileReader(file);
try {
    // Your code here
} finally {
    if (fileReader != null) {
        fileReader.close();
    }
}
```

---

### **Advanced Features**

#### **1. Custom Cleanup Method**
You can specify a custom cleanup method if the resource does not use `close()`.

##### **Code:**
```java
import lombok.Cleanup;

public class CustomCleanupExample {
    static class Resource {
        void release() {
            System.out.println("Resource released!");
        }
    }

    public static void main(String[] args) {
        @Cleanup("release") Resource resource = new Resource();
        System.out.println("Using resource...");
    }
}
```

##### **Output:**
```
Using resource...
Resource released!
```

---

### **Behavior of `@Cleanup`**

| **Scenario**                         | **Behavior**                                                                                   |
|--------------------------------------|-----------------------------------------------------------------------------------------------|
| **Resource Implements `AutoCloseable` or `Closeable`** | Automatically calls the `close()` method after the resource is used.                         |
| **Custom Cleanup Method**            | Calls the specified cleanup method instead of `close()`.                                      |
| **Multiple Resources**               | Ensures all resources are closed in reverse order of their declaration.                       |
| **Exception Handling**               | Properly closes resources even if an exception occurs during resource usage.                  |

---

### **Advantages of `@Cleanup`**

1. **Reduces Boilerplate Code:**
   - Eliminates the need for repetitive `try-finally` blocks.

2. **Improves Readability:**
   - Makes the code cleaner and more concise.

3. **Prevents Resource Leaks:**
   - Ensures resources are properly closed, avoiding potential memory or resource leaks.

4. **Customizable:**
   - Allows specifying custom cleanup methods for non-standard resources.

---

### **Limitations of `@Cleanup`**

1. **Limited to Closable Resources:**
   - Works only with resources that implement `AutoCloseable` or `Closeable` or have a custom cleanup method.

2. **Not Suitable for Complex Scenarios:**
   - For highly complex resource management logic, manual `try-finally` blocks might still be preferred for better control.

3. **Requires Lombok Dependency:**
   - The project must include Lombok, which may not always be feasible in certain environments.

---

### **When to Use `@Cleanup`**

- When working with resources like file readers, database connections, or network sockets that need to be closed after use.
- To simplify code and avoid manual `try-finally` blocks.
- In scenarios where resource management is straightforward and does not require custom logic.

---

### **Comparison: Manual vs. `@Cleanup`**

| **Aspect**               | **Manual `try-finally`**                                   | **`@Cleanup`**                                  |
|--------------------------|-----------------------------------------------------------|------------------------------------------------|
| **Code Complexity**       | Requires explicit `try-finally` blocks                   | Simplifies code with annotations               |
| **Readability**           | Can become verbose for multiple resources                | Improves readability by reducing boilerplate   |
| **Error-Prone**           | Easy to forget to close resources                        | Automatically ensures resources are closed     |
| **Customization**         | Allows full control over resource management             | Customizable with specific cleanup methods     |

---

### **Summary**

| **Feature**                | **Description**                                                                                   |
|----------------------------|---------------------------------------------------------------------------------------------------|
| **Automatic Cleanup**       | Automatically calls `close()` or a custom cleanup method for resources.                         |
| **Simplifies Code**         | Reduces boilerplate code by replacing `try-finally` blocks with annotations.                     |
| **Custom Cleanup Support**  | Allows specifying a custom cleanup method for non-standard resources.                           |
| **Thread-Safe**             | Ensures proper resource cleanup even in case of exceptions.                                      |
| **Integration**             | Works seamlessly with resources implementing `AutoCloseable` or `Closeable`.                    |

