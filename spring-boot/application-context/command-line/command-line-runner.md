### **CommandLineRunner in Spring Boot**

`CommandLineRunner` is a functional interface in Spring Boot that allows developers to execute specific code after the Spring Boot application has started. It is particularly useful when you need to process command-line arguments or perform initialization tasks.

---

### **1. What is CommandLineRunner?**

- `CommandLineRunner` is a Spring Boot interface that provides a single method, `run(String... args)`.
- It is executed **after** the Spring application context is fully initialized.
- It is commonly used to:
  - Process command-line arguments.
  - Perform setup tasks such as database initialization or data loading.
  - Execute custom logic during application startup.

---

### **2. Key Features of CommandLineRunner**

- **Automatic Execution**: Beans implementing `CommandLineRunner` are executed automatically after the application starts.
- **Access to Command-Line Arguments**: The `run` method provides access to the arguments passed to the `main` method.
- **Multiple Runners**: You can define multiple `CommandLineRunner` beans, and they will all run in an undefined order unless explicitly ordered.

#### **Documentation**:
[CommandLineRunner Documentation](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/CommandLineRunner.html)

---

### **3. How to Use CommandLineRunner**

#### **a. Implementing CommandLineRunner Interface**

Create a class that implements the `CommandLineRunner` interface and override its `run` method.

##### **Example**:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
public class LogCommandLineRunner implements CommandLineRunner {

    private static final Logger log = LoggerFactory.getLogger(LogCommandLineRunner.class);

    @Override
    public void run(String... args) throws Exception {
        log.info("Application started with arguments: {}", Arrays.toString(args));
    }
}
```

#### **b. Adding CommandLineRunner as a Lambda**

You can also define a `CommandLineRunner` bean using a lambda expression in the `@SpringBootApplication` class.

##### **Example**:
```java
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class CommandLineApp {

    public static void main(String[] args) {
        SpringApplication.run(CommandLineApp.class, args);
    }

    @Bean
    public CommandLineRunner runner() {
        return args -> {
            System.out.println("Application started with arguments: " + Arrays.toString(args));
        };
    }
}
```

---

### **4. How to Pass Command-Line Arguments**

You can pass arguments to your Spring Boot application through the command line. These arguments will be available in the `run` method of the `CommandLineRunner`.

#### **Steps to Pass Arguments**:
1. **From Command Line**:
   ```bash
   java -jar your-application.jar arg1 arg2 arg3
   ```
   - Here, `arg1`, `arg2`, and `arg3` are the arguments.

2. **From IDE**:
   - In your IDE (e.g., IntelliJ IDEA or Eclipse), specify the arguments in the "Program Arguments" section of the run configuration.
 
---

### **5. Example Workflow with Command-Line Arguments**

#### **Code Example**:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@SpringBootApplication
public class CommandApplication {

    public static void main(String[] args) {
        SpringApplication.run(CommandApplication.class, args);
    }

    @Component
    public static class LogCommandLineRunner implements CommandLineRunner {

        private static final Logger log = LoggerFactory.getLogger(LogCommandLineRunner.class);

        @Override
        public void run(String... args) throws Exception {
            log.info("Run with args: {}", Arrays.toString(args));
        }
    }
}
```

#### **Passing Arguments**:
- Run the application with:
  ```bash
  java -jar command-app.jar --name=John --age=30
  ```

#### **Output**:
```
INFO 12345 --- [main] LogCommandLineRunner : Run with args: [--name=John, --age=30]
```

---

### **6. Multiple CommandLineRunners**

If you define multiple `CommandLineRunner` beans, all of them will execute, but the order is not guaranteed unless you specify it.

#### **Example**:
```java
@Component
public class FirstRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("First runner executed");
    }
}

@Component
public class SecondRunner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("Second runner executed");
    }
}
```

#### **Output**:
```
First runner executed
Second runner executed
```
(Note: Order may vary.)

#### **Specifying Order**:
Use the `@Order` annotation to control the execution order.
```java
@Component
@Order(1)
public class FirstRunner implements CommandLineRunner {
    // ...
}

@Component
@Order(2)
public class SecondRunner implements CommandLineRunner {
    // ...
}
```

---

### **7. When to Use CommandLineRunner**

- **Initialization Tasks**: Use it for tasks like database setup, file processing, or loading initial data.
- **Argument Handling**: Parse and process command-line arguments passed to the application.
- **Custom Startup Logic**: Execute specific code right after the application starts.

---

### **8. Differences Between CommandLineRunner and ApplicationRunner**

| **Feature**                 | **CommandLineRunner**                      | **ApplicationRunner**                    |
|-----------------------------|--------------------------------------------|------------------------------------------|
| **Method Signature**         | `run(String... args)`                     | `run(ApplicationArguments args)`         |
| **Arguments Format**         | Plain array of `String`                   | Provides parsed arguments via `ApplicationArguments` |
| **Use Case**                 | Simple argument handling                  | Advanced argument handling with options  |

