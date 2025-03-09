### **StreamListener in Redis (Spring Data Redis)**

`StreamListener` is a feature in **Spring Data Redis** that allows you to automatically listen and process messages from Redis Streams using an **event-driven approach**. This eliminates the need for manually polling or reading messages from the stream, making the process more efficient and reactive.

---

### **Key Concepts of StreamListener**

1. **Event Listener Concept**:
   - Similar to how Spring handles events, a `StreamListener` listens to new messages in a Redis Stream and processes them automatically.

2. **Consumer Groups**:
   - `StreamListener` works seamlessly with Redis consumer groups, allowing multiple listeners to process messages in parallel.

3. **Automatic Acknowledgment**:
   - Messages can be auto-acknowledged after being processed, ensuring reliability.

4. **Listener Container**:
   - A **StreamMessageListenerContainer** is required to manage and run `StreamListener` instances.

5. **Ease of Integration**:
   - `StreamListener` integrates well with Spring Boot, making it easy to create and register listeners as beans.

---

### **Steps to Implement a StreamListener**

1. **Create a Data Model**:
   - Define the structure of the messages to be consumed.

2. **Implement the StreamListener**:
   - Create a class that implements the `StreamListener` interface.

3. **Configure a Listener Container**:
   - Use a `StreamMessageListenerContainer` to manage and run the `StreamListener`.

4. **Register the Listener**:
   - Register the listener with the container to start consuming messages.

5. **Publish Messages**:
   - Use a publisher to send messages to the Redis Stream.

---

### **Code Examples**

#### **1. Define a Data Model**

The data model represents the structure of the messages in the stream.

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order {
    private String id;
    private Long amount;
}
```

---

#### **2. Implement a StreamListener**

Create a listener class that implements the `StreamListener` interface. This class will handle incoming messages from the stream.

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.data.redis.connection.stream.ObjectRecord;
import org.springframework.data.redis.stream.StreamListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class OrderListener implements StreamListener<String, ObjectRecord<String, Order>> {

    @Override
    public void onMessage(ObjectRecord<String, Order> message) {
        log.info("Received order: {}", message.getValue());
    }
}
```

---

#### **3. Configure a Listener Container**

The `StreamMessageListenerContainer` is responsible for managing and running the `StreamListener`.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.connection.stream.ObjectRecord;
import org.springframework.data.redis.stream.StreamMessageListenerContainer;
import org.springframework.data.redis.stream.StreamMessageListenerContainer.StreamMessageListenerContainerOptions;
import org.springframework.stereotype.Component;

import java.time.Duration;

@Component
public class ListenerContainerConfig {

    @Bean(destroyMethod = "stop", initMethod = "start")
    public StreamMessageListenerContainer<String, ObjectRecord<String, Order>> orderContainer(
            RedisConnectionFactory connectionFactory) {

        var options = StreamMessageListenerContainerOptions
                .builder()
                .pollTimeout(Duration.ofSeconds(5)) // Poll every 5 seconds
                .targetType(Order.class) // Map the stream messages to the Order class
                .build();

        return StreamMessageListenerContainer.create(connectionFactory, options);
    }
}
```

---

#### **4. Register the Listener**

Register the `StreamListener` to the container by creating a **subscription**.

```java
import org.springframework.context.annotation.Bean;
import org.springframework.data.redis.connection.stream.Consumer;
import org.springframework.data.redis.connection.stream.ReadOffset;
import org.springframework.data.redis.connection.stream.StreamOffset;
import org.springframework.data.redis.stream.StreamMessageListenerContainer;
import org.springframework.data.redis.stream.Subscription;
import org.springframework.stereotype.Component;

@Component
public class SubscriptionConfig {

    @Bean
    public Subscription orderSubscription(
            StreamMessageListenerContainer<String, ObjectRecord<String, Order>> orderContainer,
            OrderListener orderListener) {

        // Create a consumer group if it doesn't exist
        try {
            redisTemplate.opsForStream().createGroup("orders", "order-group");
        } catch (Exception e) {
            // Consumer group already exists
        }

        // Define the offset and consumer
        var offset = StreamOffset.create("orders", ReadOffset.lastConsumed());
        var consumer = Consumer.from("order-group", "consumer-1");

        // Build the read request
        var readRequest = StreamMessageListenerContainer.StreamReadRequest.builder(offset)
                .consumer(consumer)
                .autoAcknowledge(true) // Auto-acknowledge messages
                .cancelOnError(throwable -> false) // Continue on errors
                .errorHandler(throwable -> log.warn("Error occurred: {}", throwable.getMessage()))
                .build();

        // Register the listener
        return orderContainer.register(readRequest, orderListener);
    }
}
```

---

#### **5. Publish Messages**

Periodically publish messages to the Redis Stream for processing by the listener.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.connection.stream.ObjectRecord;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.UUID;
import java.util.concurrent.TimeUnit;

@Component
public class OrderPublisher {

    @Autowired
    private StringRedisTemplate redisTemplate;

    @Scheduled(fixedRate = 10, timeUnit = TimeUnit.SECONDS) // Publish every 10 seconds
    public void publishOrder() {
        Order order = new Order(UUID.randomUUID().toString(), 1000L);
        ObjectRecord<String, Order> record = ObjectRecord.create("orders", order);
        redisTemplate.opsForStream().add(record);
    }
}
```

---

### **Key Features of StreamListener**

1. **Automatic Message Processing**:
   - Automatically listens to new messages in the stream and processes them.

2. **Integration with Consumer Groups**:
   - Supports Redis consumer groups for distributed message processing.

3. **Error Handling**:
   - Provides options to handle errors gracefully and continue processing.

4. **Auto Acknowledgment**:
   - Messages can be automatically acknowledged after successful processing.

5. **Polling Timeout**:
   - Configurable polling intervals to control how frequently the listener checks for new messages.

---

### **Advanced Features and Customization**

#### **1. Custom Error Handling**
You can define custom error-handling logic in the `StreamReadRequest`.

```java
.errorHandler(throwable -> log.error("Error processing message: {}", throwable.getMessage()))
```

#### **2. Manual Acknowledgment**
Instead of auto-acknowledgment, you can manually acknowledge messages.

```java
.autoAcknowledge(false)
// Acknowledge manually in the listener
redisTemplate.opsForStream().acknowledge("orders", "order-group", message.getId());
```

#### **3. Multiple Listeners**
You can register multiple listeners for different streams or consumer groups.

---

### **Use Cases for StreamListener**

1. **Order Processing**:
   - Listen to incoming orders and process them in real time.

2. **Log Aggregation**:
   - Collect logs from multiple sources and process them for monitoring.

3. **Real-Time Analytics**:
   - Process and analyze data streams in real time.

4. **Event-Driven Architectures**:
   - Trigger actions based on events published to the stream.

5. **Notification Systems**:
   - Send notifications based on messages in the stream.

---

### **Advantages of StreamListener**

- **Reactive and Event-Driven**: Automatically processes messages without manual polling.
- **Scalable**: Works well with Redis consumer groups for distributed processing.
- **Reliable**: Supports acknowledgment to ensure no messages are lost.
- **Customizable**: Offers flexible error handling, polling intervals, and acknowledgment modes.

