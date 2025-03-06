# **Configuring and Reading Properties File** 
 
---

## **1. What is a Properties File?**

A **properties file** is a simple text file used to store key-value pairs. It is commonly used for configuration in Java applications. The file usually has a `.properties` extension, and an example might look like this:

```properties
app.name=MyApplication
app.version=1.0.0
database.url=jdbc:mysql://localhost:3306/mydb
database.username=root
database.password=secret
```

---

## **2. How to Load a Properties File**

In Java, you can use the `ClassPathResource` class (from the Spring Framework) to load a properties file located in the classpath. The code snippet provided demonstrates this process.

### **Code Explanation**

```java
var resource = new ClassPathResource(path: "/application.properties");
Assertions.assertNotNull(resource);

System.out.println(resource.getPath());
System.out.println(resource.getFilename());
System.out.println(resource.getFile().getAbsolutePath());
```

### **Steps in the Code**:

1. **Load the Resource**:
   - The `ClassPathResource` class is used to load the `application.properties` file from the classpath.
   - The file path is specified as `"/application.properties"`. Ensure the file is located in the `src/main/resources` directory or another directory included in the classpath.

2. **Assert Not Null**:
   - The `Assertions.assertNotNull(resource)` ensures that the resource is successfully loaded. If the file is missing or the path is incorrect, the test will fail.

3. **Print Resource Details**:
   - `resource.getPath()`: Returns the path of the resource relative to the classpath.
   - `resource.getFilename()`: Returns the name of the file (e.g., `application.properties`).
   - `resource.getFile().getAbsolutePath()`: Returns the absolute file path in the file system.

---

## **3. Directory Structure**

Ensure your project structure is set up correctly so the `application.properties` file can be found in the classpath.

Example directory structure:

```
src/
├── main/
│   ├── java/
│   └── resources/
│       └── application.properties
```

---

## **4. Example Properties File**

Here is an example `application.properties` file:

```properties
server.port=8080
server.host=localhost
app.name=SampleApp
app.environment=development
```

---

## **5. How to Use the Loaded Properties**

Once the file is loaded, you can use the `Properties` class to read key-value pairs from it:

### **Complete Example**

```java
import org.springframework.core.io.ClassPathResource;

import java.io.IOException;
import java.util.Properties;

public class PropertiesLoader {

    public static void main(String[] args) throws IOException {
        // Load the properties file using ClassPathResource
        var resource = new ClassPathResource("/application.properties");

        // Ensure the resource is not null
        Assertions.assertNotNull(resource);

        // Load properties into a Properties object
        Properties properties = new Properties();
        properties.load(resource.getInputStream());

        // Access properties
        String appName = properties.getProperty("app.name");
        String appEnvironment = properties.getProperty("app.environment");

        // Print properties
        System.out.println("Application Name: " + appName);
        System.out.println("Environment: " + appEnvironment);
    }
}
```

### **Output**

If the `application.properties` contains:

```properties
app.name=SampleApp
app.environment=development
```

The output will be:

```
Application Name: SampleApp
Environment: development
```

---

## **6. Common Issues and Troubleshooting**

1. **File Not Found**:
   - Ensure the `application.properties` file is in the `src/main/resources` directory or any directory included in the classpath.

2. **Incorrect Path**:
   - Use the correct path when initializing `ClassPathResource`. For example, `"/application.properties"` assumes the file is at the root of the classpath.

3. **Spring Dependency Missing**:
   - Ensure you have the Spring Core dependency in your `pom.xml` (for Maven) or `build.gradle` (for Gradle).

   **Maven**:
   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-core</artifactId>
       <version>5.x.x</version>
   </dependency>
   ```

   **Gradle**:
   ```gradle
   implementation 'org.springframework:spring-core:5.x.x'
   ```

4. **Encoding Issues**:
   - If the file contains non-ASCII characters, ensure it is saved with the correct encoding (e.g., UTF-8).

---

## **7. Best Practices**

- **Externalize Configuration**:
  - Keep properties files outside the codebase for easier updates in production environments.
  
- **Use Profiles**:
  - For different environments (e.g., `dev`, `prod`), use profile-specific properties files like `application-dev.properties` or `application-prod.properties`.

- **Secure Sensitive Data**:
  - Avoid storing sensitive information, such as passwords, in plain text. Use environment variables or a secure vault.
