### **Redis Streams Overview**

Redis Streams is a versatile data structure introduced in Redis 5.0 that acts as a log-based messaging system, enabling features like message queues, event sourcing, and real-time data streaming. Streams are persistent, append-only logs that store messages with unique IDs, and they support consumer groups for reliable message processing and distribution.

In **Spring Data Redis**, the `StreamOperations` class provides an abstraction to interact with Redis Streams, allowing developers to publish, consume, and manage stream data efficiently.

---

### **Core Features of Redis Streams**

1. **Append-Only Log**:
   - Messages are stored as an ordered sequence with unique IDs.

2. **Consumer Groups**:
   - Distribute messages among multiple consumers for parallel processing.

3. **Message Acknowledgment**:
   - Consumers can acknowledge messages to ensure reliable delivery.

4. **Blocking Reads**:
   - Consumers can block and wait for new messages.

5. **Persistence**:
   - Unlike Pub/Sub, streams persist messages until explicitly deleted.

6. **Range Queries**:
   - Retrieve messages based on specific ID ranges.

7. **Trimming**:
   - Manage memory by trimming older messages in the stream.

---

### **Redis Streams Operations in Spring Data Redis**

Spring Data Redis provides the `StreamOperations` interface to interact with Redis Streams. It supports operations like adding messages, reading messages, creating consumer groups, acknowledging messages, and trimming streams.

The `StreamOperations` interface is accessed via the `RedisTemplate` object using the `opsForStream()` method.

---

### **Key Methods in `StreamOperations`**

| **Method**                           | **Description**                                                                 |
|--------------------------------------|---------------------------------------------------------------------------------|
| `add(MapRecord)`                     | Adds a message to the stream.                                                  |
| `read(StreamOffset)`                 | Reads messages from a stream.                                                  |
| `read(Consumer, StreamOffset)`       | Reads messages as part of a consumer group.                                    |
| `acknowledge(String, String, IDs...)`| Acknowledges messages in a consumer group.                                     |
| `createGroup(String, String)`        | Creates a consumer group for a stream.                                         |
| `delete(String, IDs...)`             | Deletes specific messages from a stream.                                       |
| `trim(String, long)`                 | Trims the stream to retain only the last N messages.                           |

---

### **Example 1: Publish Messages to a Stream**

This example demonstrates how to publish multiple messages to a Redis stream using Spring Data Redis.

#### Code:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.connection.stream.MapRecord;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StreamOperations;
import org.springframework.stereotype.Service;

import java.util.Map;

@Service
public class StreamService {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    public void publishMessages() {
        // Get StreamOperations
        StreamOperations<String, String, String> streamOps = redisTemplate.opsForStream();

        // Create a record (message)
        MapRecord<String, String, String> record = MapRecord.create("stream-1", Map.of(
            "name", "Eko Kurniawan",
            "address", "Indonesia"
        ));

        // Publish the record multiple times
        for (int i = 0; i < 10; i++) {
            streamOps.add(record);
        }
    }
}
```

---

### **Example 2: Subscribe and Read Messages from a Stream**

This example demonstrates how to create a consumer group and read messages from a Redis stream as part of the group.

#### Code:
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.connection.stream.Consumer;
import org.springframework.data.redis.connection.stream.ReadOffset;
import org.springframework.data.redis.connection.stream.StreamOffset;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StreamOperations;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class StreamService {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    public void subscribeToStream() {
        // Get StreamOperations
        StreamOperations<String, String, String> streamOps = redisTemplate.opsForStream();

        // Create a consumer group
        try {
            streamOps.createGroup("stream-1", "sample-group");
        } catch (Exception e) {
            System.out.println("Group already exists");
        }

        // Read messages as part of the consumer group
        List<MapRecord<String, String, String>> records = streamOps.read(
            Consumer.from("sample-group", "consumer-1"),
            StreamOffset.create("stream-1", ReadOffset.lastConsumed())
        );

        // Print messages
        for (MapRecord<String, String, String> record : records) {
            System.out.println(record.getValue());
        }
    }
}
```

---

### **Example 3: Acknowledge Messages**

After processing a message, it is essential to acknowledge it to mark it as processed.

#### Code:
```java
public void acknowledgeMessage(String stream, String group, String messageId) {
    // Acknowledge the message
    redisTemplate.opsForStream().acknowledge(stream, group, messageId);
}
```

---

### **Example 4: Delete Messages from a Stream**

You can delete specific messages from a stream using their IDs.

#### Code:
```java
public void deleteMessage(String stream, String messageId) {
    // Delete a specific message from the stream
    redisTemplate.opsForStream().delete(stream, messageId);
}
```

---

### **Example 5: Trimming the Stream**

To manage memory usage, you can trim the stream to keep only the most recent messages.

#### Code:
```java
public void trimStream(String stream) {
    // Trim the stream to keep only the last 100 messages
    redisTemplate.opsForStream().trim(stream, 100);
}
```

---

### **Example 6: Blocking Reads**

You can block a consumer until new messages are available in the stream.

#### Code:
```java
import org.springframework.data.redis.connection.stream.StreamReadOptions;
import org.springframework.data.redis.connection.stream.StreamRecords;

public void blockingRead(String stream) {
    List<MapRecord<String, String, String>> records = redisTemplate.opsForStream().read(
        StreamReadOptions.empty().block(5000), // Block for 5 seconds
        StreamRecords.fromStream(stream)
    );

    for (MapRecord<String, String, String> record : records) {
        System.out.println(record.getValue());
    }
}
```

---

### **Example 7: Range Queries**

Retrieve messages within a specific range of IDs.

#### Code:
```java
import org.springframework.data.redis.connection.stream.Range;

public void readRange(String stream) {
    List<MapRecord<String, String, String>> records = redisTemplate.opsForStream().range(
        stream,
        Range.closed("0-0", "9999999999999-0") // Specify the range of IDs
    );

    for (MapRecord<String, String, String> record : records) {
        System.out.println(record.getValue());
    }
}
```

---

### **Use Cases for Redis Streams**

1. **Message Queues**:
   - Streams can act as a reliable message queue with persistence and acknowledgment.

2. **Event Sourcing**:
   - Store events in a stream for later replay or analysis.

3. **Real-Time Analytics**:
   - Process and analyze data streams in real-time.

4. **Distributed Systems**:
   - Use consumer groups to distribute messages among multiple consumers.

5. **Log Aggregation**:
   - Collect and store logs for debugging and monitoring.

6. **Data Pipelines**:
   - Use streams to build ETL pipelines for processing large data sets.

---

### **Advantages of Redis Streams**

1. **Persistence**:
   - Messages are stored until explicitly deleted, unlike Pub/Sub.

2. **Scalability**:
   - Consumer groups allow for distributed processing of messages.

3. **Reliability**:
   - Acknowledgment ensures that messages are processed reliably.

4. **Flexibility**:
   - Supports a wide range of operations like trimming, deleting, and reading messages.

---

### **Limitations of Redis Streams**

1. **Memory Usage**:
   - Streams can consume significant memory if not trimmed regularly.

2. **Complexity**:
   - Managing consumer groups and acknowledgments adds complexity.

3. **No Built-In Retry**:
   - You need to implement retry mechanisms for failed messages.

