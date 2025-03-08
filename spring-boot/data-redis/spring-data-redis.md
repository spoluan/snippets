### **Spring Data Redis Overview**

**Spring Data Redis** is a module of the Spring Data project that provides easy integration with **Redis**, a popular in-memory data store. It simplifies the process of interacting with Redis by offering an abstraction layer and various utilities to work with Redis data structures.

---

### **Key Features of Spring Data Redis**
1. **Integration with Redis**: Allows seamless communication with Redis databases in Spring applications.
2. **Support for Redis Data Structures**:
   - Strings
   - Hashes
   - Lists
   - Sets
   - Sorted Sets
3. **Pub/Sub Messaging**: Supports Redis's publish/subscribe model for messaging.
4. **Caching**: Provides support for caching using Redis.
5. **Cluster Support**: Works with Redis clusters for high availability and scalability.
6. **Transactions**: Supports Redis transactions.
7. **Spring Boot Integration**: Simplifies configuration using `application.properties` or `application.yml`.

---

### **Steps to Use Spring Data Redis**

#### 1. **Add Dependency**
Add the following dependency in `pom.xml` for Maven projects:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

For Gradle:
```gradle
implementation 'org.springframework.boot:spring-boot-starter-data-redis'
```

---

#### 2. **Run Redis Server**
Ensure that the Redis server is running. You can download and run Redis locally or use a cloud-based Redis service.

---

#### 3. **Configuration**
Configure the connection to the Redis server in `application.properties` or `application.yml`.

**Example: `application.properties`**
```properties
spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.data.redis.database=0
spring.data.redis.timeout=5s
spring.data.redis.connect-timeout=5s
# Uncomment these if authentication is required
# spring.data.redis.username=your-username
# spring.data.redis.password=your-password
```

**Example: `application.yml`**
```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6379
      database: 0
      timeout: 5s
      connect-timeout: 5s
```

---

#### 4. **Basic CRUD Operations**

Create a service or repository to interact with Redis. Here's an example using `RedisTemplate`.

**Configuration Class:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;

@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        return template;
    }
}
```

**Service Example:**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

@Service
public class RedisService {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    // Save data
    public void save(String key, Object value) {
        redisTemplate.opsForValue().set(key, value);
    }

    // Retrieve data
    public Object get(String key) {
        return redisTemplate.opsForValue().get(key);
    }

    // Delete data
    public void delete(String key) {
        redisTemplate.delete(key);
    }
}
```

**Controller Example:**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/redis")
public class RedisController {

    @Autowired
    private RedisService redisService;

    @PostMapping("/save")
    public String save(@RequestParam String key, @RequestParam String value) {
        redisService.save(key, value);
        return "Data saved!";
    }

    @GetMapping("/get")
    public Object get(@RequestParam String key) {
        return redisService.get(key);
    }

    @DeleteMapping("/delete")
    public String delete(@RequestParam String key) {
        redisService.delete(key);
        return "Data deleted!";
    }
}
```

---

### **Working with Redis Data Structures**

#### 1. **Strings**
```java
redisTemplate.opsForValue().set("key", "value"); // Save
String value = redisTemplate.opsForValue().get("key"); // Retrieve
```

#### 2. **Hashes**
```java
redisTemplate.opsForHash().put("hashKey", "field", "value"); // Save
Object value = redisTemplate.opsForHash().get("hashKey", "field"); // Retrieve
```

#### 3. **Lists**
```java
redisTemplate.opsForList().rightPush("listKey", "value1"); // Add to List
redisTemplate.opsForList().rightPush("listKey", "value2");
List<Object> list = redisTemplate.opsForList().range("listKey", 0, -1); // Retrieve List
```

#### 4. **Sets**
```java
redisTemplate.opsForSet().add("setKey", "value1", "value2"); // Add to Set
Set<Object> set = redisTemplate.opsForSet().members("setKey"); // Retrieve Set
```

#### 5. **Sorted Sets**
```java
redisTemplate.opsForZSet().add("sortedSetKey", "value1", 1); // Add with score
redisTemplate.opsForZSet().add("sortedSetKey", "value2", 2);
Set<Object> sortedSet = redisTemplate.opsForZSet().range("sortedSetKey", 0, -1); // Retrieve
```

---

### **Using Redis as Cache**

Add the following dependency for caching:
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

Enable caching in your Spring Boot application:
```java
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableCaching
public class CacheConfig {
}
```

Use the `@Cacheable` annotation in your service:
```java
import org.springframework.cache.annotation.Cacheable;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Cacheable("users")
    public String getUserById(String userId) {
        // Simulate a slow database query
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "User " + userId;
    }
}
```

---

### **Pub/Sub Messaging**

**Publisher Example:**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

@Service
public class MessagePublisher {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    public void publish(String channel, String message) {
        redisTemplate.convertAndSend(channel, message);
    }
}
```

**Subscriber Example:**
```java
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.stereotype.Component;

@Component
public class RedisSubscriber implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        System.out.println("Received message: " + message.toString());
    }
}
```

**Configuration for Pub/Sub:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.listener.ChannelTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;

@Configuration
public class PubSubConfig {

    @Bean
    public RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory, RedisSubscriber subscriber) {
        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.addMessageListener(subscriber, new ChannelTopic("myTopic"));
        return container;
    }
}
```

---

### **Cluster Support**
To connect to a Redis cluster, use the `spring.data.redis.cluster.nodes` property:
```properties
spring.data.redis.cluster.nodes=127.0.0.1:7000,127.0.0.1:7001
```
