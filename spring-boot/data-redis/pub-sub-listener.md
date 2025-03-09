### **Pub/Sub Listener in Redis**

A **Pub/Sub Listener** in Redis enables applications to subscribe to specific channels and listen for messages published to those channels. In Spring Data Redis, this feature is implemented using a **`MessageListener`** and a **`RedisMessageListenerContainer`**.

---

### **Key Components of Pub/Sub Listener in Spring Data Redis**

1. **Publisher**:
   - Publishes messages to a Redis channel using `RedisTemplate.convertAndSend()`.

2. **MessageListener**:
   - A listener that implements the `MessageListener` interface to handle incoming messages.

3. **RedisMessageListenerContainer**:
   - A container that manages `MessageListener` instances and subscribes them to specific Redis channels.

4. **Channel**:
   - A named communication path where messages are published and received.

---

### **How Pub/Sub Listener Works**

1. **Publisher** sends messages to a channel.
2. **RedisMessageListenerContainer** subscribes to the channel.
3. When a message is published, the **MessageListener** processes it in real time.

---

### **Steps to Implement Pub/Sub Listener**

#### **1. Create a Publisher**

The publisher sends messages to a specific channel.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.UUID;
import java.util.concurrent.TimeUnit;

@Component
public class CustomerPublisher {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @Scheduled(fixedRate = 10L, timeUnit = TimeUnit.SECONDS) // Publish every 10 seconds
    public void publish() {
        redisTemplate.convertAndSend("customers", "Eko " + UUID.randomUUID().toString());
        System.out.println("Message published to channel: customers");
    }
}
```

---

#### **2. Create a Listener**

The listener processes messages received from the subscribed channel.

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.connection.Message;
import org.springframework.data.redis.connection.MessageListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class CustomerListener implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String channel = new String(pattern);
        String body = new String(message.getBody());
        log.info("Received message: {} from channel: {}", body, channel);
    }
}
```

---

#### **3. Configure the Listener Container**

The `RedisMessageListenerContainer` manages the listener and subscribes it to the desired channel.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.listener.ChannelTopic;
import org.springframework.data.redis.listener.RedisMessageListenerContainer;
import org.springframework.stereotype.Component;

@Component
public class ListenerContainerConfig {

    @Bean
    public RedisMessageListenerContainer messageListenerContainer(
            RedisConnectionFactory connectionFactory,
            CustomerListener customerListener) {

        RedisMessageListenerContainer container = new RedisMessageListenerContainer();
        container.setConnectionFactory(connectionFactory);
        container.addMessageListener(customerListener, new ChannelTopic("customers"));
        return container;
    }
}
```

---

### **End-to-End Flow**

1. **Publisher** publishes a message to the `customers` channel every 10 seconds.
2. The **RedisMessageListenerContainer** subscribes the **CustomerListener** to the `customers` channel.
3. When a message is published, the **CustomerListener** processes it and logs the message.

---

### **Advanced Examples**

#### **1. Multiple Listeners for Different Channels**

You can create multiple listeners for different channels.

```java
@Component
public class OrderListener implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String body = new String(message.getBody());
        System.out.println("Order received: " + body);
    }
}

@Component
public class ProductListener implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String body = new String(message.getBody());
        System.out.println("Product update: " + body);
    }
}

// Configuration
@Bean
public RedisMessageListenerContainer messageListenerContainer(
        RedisConnectionFactory connectionFactory,
        OrderListener orderListener,
        ProductListener productListener) {

    RedisMessageListenerContainer container = new RedisMessageListenerContainer();
    container.setConnectionFactory(connectionFactory);
    container.addMessageListener(orderListener, new ChannelTopic("orders"));
    container.addMessageListener(productListener, new ChannelTopic("products"));
    return container;
}
```

---

#### **2. Pattern-Based Subscription**

Subscribe to multiple channels using a pattern.

```java
@Component
public class PatternListener implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        String channel = new String(pattern);
        String body = new String(message.getBody());
        System.out.println("Pattern matched: " + channel + ", Message: " + body);
    }
}

// Configuration
@Bean
public RedisMessageListenerContainer patternMessageListenerContainer(
        RedisConnectionFactory connectionFactory,
        PatternListener patternListener) {

    RedisMessageListenerContainer container = new RedisMessageListenerContainer();
    container.setConnectionFactory(connectionFactory);
    container.addMessageListener(patternListener, new PatternTopic("customer-*"));
    return container;
}
```

---

#### **3. Dynamic Channel Subscription**

Dynamically subscribe to a channel at runtime.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.stereotype.Component;

@Component
public class DynamicSubscriber {

    @Autowired
    private RedisConnection redisConnection;

    public void subscribeToChannel(String channel) {
        redisConnection.subscribe((message, pattern) -> {
            System.out.println("Dynamic subscriber received: " + new String(message.getBody()));
        }, channel.getBytes());
    }
}
```

---

#### **4. Custom Error Handling**

Handle errors gracefully in the listener.

```java
@Component
public class ErrorHandlingListener implements MessageListener {

    @Override
    public void onMessage(Message message, byte[] pattern) {
        try {
            String body = new String(message.getBody());
            System.out.println("Processed message: " + body);
        } catch (Exception e) {
            System.err.println("Error processing message: " + e.getMessage());
        }
    }
}
```

---

### **Best Practices**

1. **Use Separate Channels**:
   - Use different channels for different message types to avoid conflicts.

2. **Graceful Shutdown**:
   - Ensure the `RedisMessageListenerContainer` is stopped gracefully during application shutdown.

3. **Error Handling**:
   - Implement error handling in listeners to avoid crashes due to unexpected message formats.

4. **Scalability**:
   - Use multiple listeners for high-throughput scenarios.

5. **Avoid Heavy Processing**:
   - Offload heavy processing to separate threads or queues to prevent blocking the listener.

---

### **Comparison with Redis Streams**

| Feature                 | Pub/Sub                     | Redis Streams               |
|-------------------------|-----------------------------|-----------------------------|
| **Persistence**         | No                         | Yes                         |
| **Message Acknowledgment** | No                         | Yes                         |
| **Consumer Groups**     | No                         | Yes                         |
| **Use Case**            | Real-time notifications    | Reliable message queues     |
| **Scalability**         | High                       | High                        |

---

### **Use Cases**

1. **Pub/Sub Listener**:
   - Real-time notifications (e.g., chat messages, stock price updates).
   - Broadcasting system-wide events.

2. **Redis Streams**:
   - Event sourcing.
   - Reliable job processing.

