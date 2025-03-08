### **Actuator Endpoint: `/actuator/info`**

The `/actuator/info` endpoint in Spring Boot Actuator is used to display custom application information. It can be configured to include metadata about the application, such as version, author, or any other custom details. This endpoint is particularly useful for providing non-sensitive information about the application to external systems or developers.

---

### **Features of the `/actuator/info` Endpoint**

1. **Customizable Information**:
   - You can add any information you want, such as application name, version, author, build details, etc., using the `info` prefix in `application.properties` or `application.yml`.

2. **Environment Information**:
   - The endpoint can be configured to include environment details, such as OS and Java runtime information.

3. **Easy Integration**:
   - Works seamlessly with monitoring tools to provide application metadata.

4. **Security**:
   - The endpoint does not expose sensitive information by default. However, you can configure it to include additional details if required.

---

### **How to Enable and Configure the `/actuator/info` Endpoint**

#### **1. Add Actuator Dependency**

Add the following dependency to your project:

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

#### **2. Enable the Endpoint**

By default, the `/actuator/info` endpoint is enabled but not exposed. To expose it, update your `application.properties` or `application.yml`:

**For `application.properties`:**
```properties
management.endpoints.web.exposure.include=info
management.endpoint.info.enabled=true
```

**For `application.yml`:**
```yaml
management:
  endpoints:
    web:
      exposure:
        include: info
  endpoint:
    info:
      enabled: true
```

---

#### **3. Add Custom Information**

You can add custom information to the `/actuator/info` endpoint using the `info` prefix in your `application.properties` or `application.yml`.

**For `application.properties`:**
```properties
info.app=My Spring Boot Application
info.version=1.0.0
info.author=John Doe
info.website=https://example.com
```

**For `application.yml`:**
```yaml
info:
  app: My Spring Boot Application
  version: 1.0.0
  author: John Doe
  website: https://example.com
```

---

#### **4. Enable Environment Information**

To include additional environment details (e.g., OS and Java runtime), update the configuration:

**For `application.properties`:**
```properties
management.info.env.enabled=true
management.info.os.enabled=true
management.info.java.enabled=true
```

**For `application.yml`:**
```yaml
management:
  info:
    env:
      enabled: true
    os:
      enabled: true
    java:
      enabled: true
```

---

### **Accessing the `/actuator/info` Endpoint**

Once configured, you can access the `/actuator/info` endpoint at:
```
http://localhost:8080/actuator/info
```

---

### **Sample Configurations and Responses**

#### **1. Custom Application Information**

**Configuration (`application.properties`):**
```properties
info.app=My Spring Boot Application
info.version=1.0.0
info.author=John Doe
info.website=https://example.com
```

**Response:**
```json
{
  "app": "My Spring Boot Application",
  "version": "1.0.0",
  "author": "John Doe",
  "website": "https://example.com"
}
```

---

#### **2. Include Environment Information**

**Configuration (`application.properties`):**
```properties
info.app=My Spring Boot Application
info.version=1.0.0
info.author=John Doe
management.info.env.enabled=true
management.info.os.enabled=true
management.info.java.enabled=true
```

**Response:**
```json
{
  "app": "My Spring Boot Application",
  "version": "1.0.0",
  "author": "John Doe",
  "java": {
    "version": "17",
    "vendor": {
      "name": "Oracle Corporation"
    },
    "runtime": {
      "name": "OpenJDK Runtime Environment",
      "version": "17.0.2+8"
    },
    "jvm": {
      "name": "OpenJDK 64-Bit Server VM",
      "vendor": "Oracle Corporation",
      "version": "17.0.2+8"
    }
  },
  "os": {
    "name": "Mac OS X",
    "version": "12.3",
    "arch": "x86_64"
  }
}
```

---

#### **3. Add Build Information**

You can include build information in the `/actuator/info` endpoint by adding a `build-info.properties` file.

**Steps:**
1. Add the `spring-boot-maven-plugin` to your `pom.xml` (if using Maven):
   ```xml
   <plugin>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-maven-plugin</artifactId>
       <configuration>
           <addResources>true</addResources>
       </configuration>
   </plugin>
   ```
2. Add the following to your `application.properties`:
   ```properties
   info.build.artifact=${project.artifactId}
   info.build.version=${project.version}
   info.build.name=${project.name}
   ```

**Response:**
```json
{
  "build": {
    "artifact": "my-app",
    "version": "1.0.0",
    "name": "My Spring Boot Application"
  }
}
```

---

#### **4. Combine Custom and Build Information**

**Configuration (`application.yml`):**
```yaml
info:
  app: My Spring Boot Application
  version: 1.0.0
  author: John Doe
  website: https://example.com
management:
  info:
    env:
      enabled: true
    os:
      enabled: true
    java:
      enabled: true
```

**Response:**
```json
{
  "app": "My Spring Boot Application",
  "version": "1.0.0",
  "author": "John Doe",
  "website": "https://example.com",
  "java": {
    "version": "17",
    "vendor": {
      "name": "Oracle Corporation"
    },
    "runtime": {
      "name": "OpenJDK Runtime Environment",
      "version": "17.0.2+8"
    },
    "jvm": {
      "name": "OpenJDK 64-Bit Server VM",
      "vendor": "Oracle Corporation",
      "version": "17.0.2+8"
    }
  },
  "os": {
    "name": "Mac OS X",
    "version": "12.3",
    "arch": "x86_64"
  }
}
```

---

### **Use Cases for the `/actuator/info` Endpoint**

1. **Application Metadata**:
   - Provide basic information about the application, such as version, author, or description, for documentation or monitoring purposes.

2. **Build Information**:
   - Include build details (e.g., artifact ID, build version) for debugging and tracking deployments.

3. **Environment Details**:
   - Expose runtime environment details like OS and Java version to help with troubleshooting.

4. **Integration with Monitoring Tools**:
   - Use the `/actuator/info` endpoint to integrate with monitoring systems like Prometheus or Grafana.

---

### **Best Practices**

1. **Avoid Sensitive Information**:
   - Do not expose sensitive information (e.g., passwords, API keys) through the `/actuator/info` endpoint.

2. **Secure the Endpoint**:
   - Use Spring Security to restrict access to the `/actuator/info` endpoint if it contains sensitive data.

3. **Use for Non-Sensitive Metadata**:
   - Use this endpoint only for non-sensitive metadata that can be safely shared.

4. **Combine with Build Automation**:
   - Automate the inclusion of build details using tools like Maven or Gradle.

