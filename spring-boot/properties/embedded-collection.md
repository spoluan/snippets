### **Introduction to Embedded Collections in Spring Boot Configuration Properties**

In Spring Boot, **embedded collections** refer to the ability to map collections (e.g., `List`, `Set`, or `Map`) from configuration files (like `application.yml` or `application.properties`) into Java objects using the `@ConfigurationProperties` annotation. This feature is particularly useful for grouping related configuration properties in a structured and hierarchical way.

Spring Boot supports binding hierarchical configurations (e.g., nested objects and collections) to Java classes, making it easier to manage and access complex configurations in your application.

---

### **Why Use Embedded Collections in Spring Boot?**

- **Structured Configuration:** Organize related settings in a more readable and maintainable structure.
- **Type-Safe Binding:** Automatically convert configuration properties into strongly typed Java objects.
- **Reusability:** Group configurations into reusable components.
- **Ease of Access:** Access nested or complex configurations directly in your application without manual parsing.

---

### **Key Annotations for Configuration Properties**

1. **`@ConfigurationProperties`:**  
   Binds the properties from the configuration file to a Java class.

2. **`@EnableConfigurationProperties`:**  
   Enables support for `@ConfigurationProperties` in the application.

3. **`@Component`:**  
   Marks the configuration class as a Spring-managed bean (alternative to registering the class manually).

4. **`@ConstructorBinding` (Optional):**  
   Used when you prefer immutable configuration classes with constructor-based binding.

---

### **Examples of Embedded Collections**

#### **1. Using `List` in Configuration**

##### **application.yml**
```yaml
app:
  servers:
    - host: server1.example.com
      port: 8080
    - host: server2.example.com
      port: 9090
```

##### **application.properties**
```properties
app.servers[0].host=server1.example.com
app.servers[0].port=8080
app.servers[1].host=server2.example.com
app.servers[1].port=9090
```

##### **Java Configuration Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private List<Server> servers;

    public List<Server> getServers() {
        return servers;
    }

    public void setServers(List<Server> servers) {
        this.servers = servers;
    }

    public static class Server {
        private String host;
        private int port;

        public String getHost() {
            return host;
        }

        public void setHost(String host) {
            this.host = host;
        }

        public int getPort() {
            return port;
        }

        public void setPort(int port) {
            this.port = port;
        }
    }
}
```

##### **Accessing the Configuration**
```java
import org.springframework.stereotype.Service;

@Service
public class ServerService {

    private final AppConfig appConfig;

    public ServerService(AppConfig appConfig) {
        this.appConfig = appConfig;
    }

    public void printServers() {
        appConfig.getServers().forEach(server -> 
            System.out.println("Host: " + server.getHost() + ", Port: " + server.getPort())
        );
    }
}
```

---

#### **2. Using `Map` in Configuration**

##### **application.yml**
```yaml
app:
  database:
    connections:
      primary:
        url: jdbc:postgresql://localhost:5432/primary_db
        username: admin
        password: secret
      secondary:
        url: jdbc:postgresql://localhost:5432/secondary_db
        username: user
        password: password
```

##### **application.properties**
```properties
app.database.connections.primary.url=jdbc:postgresql://localhost:5432/primary_db
app.database.connections.primary.username=admin
app.database.connections.primary.password=secret
app.database.connections.secondary.url=jdbc:postgresql://localhost:5432/secondary_db
app.database.connections.secondary.username=user
app.database.connections.secondary.password=password
```

##### **Java Configuration Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Map;

@Component
@ConfigurationProperties(prefix = "app.database")
public class DatabaseConfig {

    private Map<String, Connection> connections;

    public Map<String, Connection> getConnections() {
        return connections;
    }

    public void setConnections(Map<String, Connection> connections) {
        this.connections = connections;
    }

    public static class Connection {
        private String url;
        private String username;
        private String password;

        public String getUrl() {
            return url;
        }

        public void setUrl(String url) {
            this.url = url;
        }

        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getPassword() {
            return password;
        }

        public void setPassword(String password) {
            this.password = password;
        }
    }
}
```

##### **Accessing the Configuration**
```java
import org.springframework.stereotype.Service;

@Service
public class DatabaseService {

    private final DatabaseConfig databaseConfig;

    public DatabaseService(DatabaseConfig databaseConfig) {
        this.databaseConfig = databaseConfig;
    }

    public void printDatabaseConnections() {
        databaseConfig.getConnections().forEach((key, connection) -> 
            System.out.println("Connection: " + key + ", URL: " + connection.getUrl())
        );
    }
}
```

---

#### **3. Using Nested Objects and Collections**

##### **application.yml**
```yaml
app:
  users:
    - name: Alice
      roles:
        - ADMIN
        - USER
    - name: Bob
      roles:
        - USER
```

##### **application.properties**
```properties
app.users[0].name=Alice
app.users[0].roles[0]=ADMIN
app.users[0].roles[1]=USER
app.users[1].name=Bob
app.users[1].roles[0]=USER
```

##### **Java Configuration Class**
```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.List;

@Component
@ConfigurationProperties(prefix = "app")
public class UserConfig {

    private List<User> users;

    public List<User> getUsers() {
        return users;
    }

    public void setUsers(List<User> users) {
        this.users = users;
    }

    public static class User {
        private String name;
        private List<String> roles;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public List<String> getRoles() {
            return roles;
        }

        public void setRoles(List<String> roles) {
            this.roles = roles;
        }
    }
}
```

##### **Accessing the Configuration**
```java
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final UserConfig userConfig;

    public UserService(UserConfig userConfig) {
        this.userConfig = userConfig;
    }

    public void printUsers() {
        userConfig.getUsers().forEach(user -> {
            System.out.println("Name: " + user.getName() + ", Roles: " + user.getRoles());
        });
    }
}
```

---

### **Registering Configuration Properties Class**

In Spring Boot 2.2 and later, `@ConfigurationProperties` classes are automatically registered as beans if annotated with `@Component`. If you prefer not to use `@Component`, you can manually register the class using `@EnableConfigurationProperties`.

##### **Manual Registration**
```java
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableConfigurationProperties({AppConfig.class, DatabaseConfig.class, UserConfig.class})
public class ConfigPropertiesRegistration {
}
```

---

### **Best Practices for Embedded Collections**

1. **Use `@ConfigurationProperties` for Hierarchical Configurations:**  
   This makes it easier to manage and access structured data.

2. **Validate Configuration Properties:**  
   Use annotations like `@Valid` and `@NotNull` for validation.

   Example:
   ```java
   import jakarta.validation.constraints.NotNull;

   public static class Server {
       @NotNull
       private String host;

       @NotNull
       private Integer port;

       // Getters and Setters
   }
   ```

3. **Use Immutable Classes with Constructor Binding:**  
   For better immutability, use `@ConstructorBinding` with a constructor-based approach.
