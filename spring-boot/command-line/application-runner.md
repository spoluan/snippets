### **ApplicationRunner in Spring Boot**

`ApplicationRunner` is an interface in Spring Boot, similar to `CommandLineRunner`, but it provides better handling of command-line arguments by wrapping them into an `ApplicationArguments` object. This makes it more convenient to parse and work with arguments.

---

### **1. What is ApplicationRunner?**

- `ApplicationRunner` is a Spring Boot interface that provides a single method, `run(ApplicationArguments args)`.
- It is executed **after** the Spring application context is fully initialized, just like `CommandLineRunner`.
- The key difference is that `ApplicationRunner` uses `ApplicationArguments`, which provides additional methods to parse and handle arguments.

---

### **2. Key Features of ApplicationRunner**

- **Argument Parsing**: The `ApplicationArguments` object wraps the command-line arguments and provides methods to:
  - Retrieve all arguments.
  - Access specific options or key-value pairs.
- **Automatic Execution**: Beans implementing `ApplicationRunner` are executed automatically after the application starts.
- **Multiple Runners**: You can define multiple `ApplicationRunner` beans, and they will all run in an undefined order unless explicitly ordered.

#### **Documentation**:
- [ApplicationRunner Documentation](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/ApplicationRunner.html)
- [ApplicationArguments Documentation](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/ApplicationArguments.html)

---

### **3. How to Use ApplicationRunner**

#### **a. Implementing ApplicationRunner Interface**

Create a class that implements the `ApplicationRunner` interface and override its `run` method.

##### **Example**:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
public class SimpleRunner implements ApplicationRunner {

    private static final Logger log = LoggerFactory.getLogger(SimpleRunner.class);

    @Override
    public void run(ApplicationArguments args) throws Exception {
        List<String> profiles = args.getOptionValues("profiles");
        log.info("Profiles: {}", profiles);
    }
}
```

#### **b. Adding ApplicationRunner as a Lambda**

You can also define an `ApplicationRunner` bean using a lambda expression in the `@SpringBootApplication` class.

##### **Example**:
```java
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class RunnerApplication {

    public static void main(String[] args) {
        SpringApplication.run(RunnerApplication.class, args);
    }

    @Bean
    public ApplicationRunner runner() {
        return args -> {
            if (args.containsOption("profiles")) {
                System.out.println("Profiles: " + args.getOptionValues("profiles"));
            }
        };
    }
}
```

---

### **4. How to Pass Command-Line Arguments**

You can pass arguments to your Spring Boot application through the command line or via your IDE.

#### **Steps to Pass Arguments**:

1. **From Command Line**:
   ```bash
   java -jar your-application.jar --profiles=dev,test
   ```
   - Here, `--profiles=dev,test` is the argument.

2. **From IDE**:
   - In your IDE (e.g., IntelliJ IDEA or Eclipse), specify the arguments in the "Program Arguments" section of the run configuration.

   Example:
   ```
   --profiles=dev,test
   ```
 
---

### **5. Example Workflow with ApplicationRunner**

#### **Code Example**:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Component;

import java.util.List;

@SpringBootApplication
public class RunnerApplication {

    public static void main(String[] args) {
        SpringApplication.run(RunnerApplication.class, args);
    }

    @Component
    public static class SimpleRunner implements ApplicationRunner {

        private static final Logger log = LoggerFactory.getLogger(SimpleRunner.class);

        @Override
        public void run(ApplicationArguments args) throws Exception {
            List<String> profiles = args.getOptionValues("profiles");
            log.info("Profiles: {}", profiles);
        }
    }
}
```

#### **Passing Arguments**:
- Run the application with:
  ```bash
  java -jar runner-app.jar --profiles=dev,test
  ```

#### **Output**:
```
INFO 12345 --- [main] SimpleRunner : Profiles: [dev, test]
```

---

### **6. ApplicationArguments Methods**

The `ApplicationArguments` object provides several methods for working with arguments:

| **Method**                    | **Description**                                                                 |
|--------------------------------|---------------------------------------------------------------------------------|
| `getSourceArgs()`              | Returns the raw array of arguments passed to the application.                   |
| `containsOption(String name)`  | Checks if a specific option (e.g., `--profiles`) is present.                    |
| `getOptionValues(String name)` | Retrieves the values for a specific option, e.g., `--profiles=dev,test`.        |
| `getNonOptionArgs()`           | Retrieves arguments without options (e.g., `arg1 arg2` instead of `--key=value`). |

---

### **7. Differences Between ApplicationRunner and CommandLineRunner**

| **Feature**                 | **CommandLineRunner**                      | **ApplicationRunner**                    |
|-----------------------------|--------------------------------------------|------------------------------------------|
| **Method Signature**         | `run(String... args)`                     | `run(ApplicationArguments args)`         |
| **Arguments Format**         | Plain array of `String`                   | Provides parsed arguments via `ApplicationArguments` |
| **Use Case**                 | Simple argument handling                  | Advanced argument handling with options  |

---

### **8. When to Use ApplicationRunner**

- **Advanced Argument Parsing**: Use it when you need to parse and handle command-line arguments with options or key-value pairs.
- **Initialization Tasks**: Perform setup tasks like database initialization or data loading.
- **Custom Startup Logic**: Execute specific code right after the application starts.

