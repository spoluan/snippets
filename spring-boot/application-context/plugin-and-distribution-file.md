### **Spring Boot Plugin and Distribution File**

---

### **1. Spring Boot Plugin**

The **Spring Boot Maven Plugin** is automatically included in Spring Boot projects created using tools like Spring Initializr. It simplifies the process of running and packaging Spring Boot applications.

#### **Key Features:**
- Automatically detects the `main` class of your application.
- Provides commands to run and package Spring Boot applications.
- Bundles the application and its dependencies into a single executable JAR file.

#### **Usage:**

1. **Running the Application:**
   - Use the following Maven command to run the application:
     ```bash
     mvn spring-boot:run
     ```
   - This command starts the Spring Boot application without the need to manually build or package it.
   - **Important Note:** Ensure that only one `main` class exists in the project. If there are multiple `main` classes, the plugin will throw an error.

2. **Maven Plugin Configuration:**
   - The Maven plugin is configured in the `pom.xml` file under the `<build>` section:
     ```xml
     <build>
         <plugins>
             <plugin>
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-maven-plugin</artifactId>
                 <configuration>
                     <excludes>
                         <exclude>
                             <groupId>org.projectlombok</groupId>
                             <artifactId>lombok</artifactId>
                         </exclude>
                     </excludes>
                 </configuration>
             </plugin>
         </plugins>
     </build>
     ```
   - This configuration ensures that the `spring-boot-maven-plugin` is used during the build process. It also excludes specific dependencies (e.g., `Lombok`) from the final JAR file.

---

### **2. Distribution File**

The **Spring Boot Maven Plugin** provides an easy way to create a distribution file for the application. This distribution file is typically a **fat JAR** (also known as an executable JAR), which includes the application code and all its dependencies.

#### **Steps to Create a Distribution File:**

1. **Packaging the Application:**
   - Use the following Maven command to build the application:
     ```bash
     mvn clean package
     ```
   - This command:
     - Cleans the project by removing previous build artifacts.
     - Packages the application into a single JAR file, including all dependencies.

2. **Output:**
   - After running the `mvn package` command, the output JAR file is created in the `target` directory. For example:
     ```
     target/belajar-spring-dasar-0.0.1-SNAPSHOT.jar
     ```

3. **Running the Packaged JAR:**
   - Once the JAR file is created, it can be executed using the `java -jar` command:
     ```bash
     java -jar target/belajar-spring-dasar-0.0.1-SNAPSHOT.jar
     ```
   - This command runs the Spring Boot application using the packaged JAR file.

#### **Important Notes:**
- Ensure that only one `main` class exists in the application. If there are multiple `main` classes, the plugin may fail to package the application and throw a complaint.
- The generated JAR file is self-contained and can be deployed to any environment with a Java Runtime Environment (JRE).

---

### **3. Key Benefits of Spring Boot Maven Plugin**

1. **Simplifies Development:**
   - The `spring-boot:run` command allows developers to quickly run the application without manual packaging or deployment.

2. **Efficient Packaging:**
   - The `mvn package` command creates a single executable JAR file, making deployment easier.

3. **Dependency Management:**
   - Automatically includes all required dependencies in the final JAR file, ensuring the application runs as expected.

4. **Cross-Platform Compatibility:**
   - The generated JAR file can run on any platform with a compatible JRE, making it portable.

---

### **4. Summary of Commands**

| **Command**                | **Description**                                                                 |
|----------------------------|---------------------------------------------------------------------------------|
| `mvn spring-boot:run`      | Runs the Spring Boot application directly from the source code.                 |
| `mvn clean package`        | Packages the application into a single executable JAR file.                    |
| `java -jar <file>.jar`     | Runs the packaged JAR file.                                                    |

---

### **5. Practical Example**

1. **Run the Application:**
   ```bash
   mvn spring-boot:run
   ```
   - Starts the application directly without creating a JAR file.

2. **Package the Application:**
   ```bash
   mvn clean package
   ```
   - Produces a JAR file in the `target` directory.

3. **Run the Packaged Application:**
   ```bash
   java -jar target/belajar-spring-dasar-0.0.1-SNAPSHOT.jar
   ```
   - Executes the packaged application.

