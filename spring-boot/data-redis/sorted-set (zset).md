### **ZSet Operations in Redis (Sorted Set)**

Redis **Sorted Set (ZSet)** is an advanced data structure that combines the features of a **Set** (unique elements) and a **Sorted List** (elements sorted by a score). Each member in a ZSet is associated with a floating-point score, which determines the order of the elements.

In **Spring Data Redis**, the **`ZSetOperations`** class provides a comprehensive API to interact with Redis Sorted Sets.

---

### **Key Features of ZSetOperations**
1. **Unique Elements**:
   - Like a regular set, all members in a ZSet are unique.

2. **Sorted by Score**:
   - Members are ordered based on their associated scores, in ascending order by default.

3. **Efficient Range Queries**:
   - Supports retrieving elements by rank, score range, or lexicographical order.

4. **Score Manipulation**:
   - Allows incrementing or decrementing the score of an existing member.

5. **Integration**:
   - The `ZSetOperations` class in Spring Data Redis maps directly to Redis ZSet commands.

---

### **Common Methods in ZSetOperations**

| **Method**                                  | **Description**                                                                 |
|---------------------------------------------|---------------------------------------------------------------------------------|
| `add(K key, V value, double score)`         | Adds a member to the ZSet with the specified score.                             |
| `score(K key, Object value)`                | Retrieves the score of a specific member.                                       |
| `rank(K key, Object value)`                 | Gets the rank (index) of a member in ascending order.                           |
| `reverseRank(K key, Object value)`          | Gets the rank (index) of a member in descending order.                          |
| `range(K key, long start, long end)`        | Retrieves members within the specified rank range (ascending order).            |
| `reverseRange(K key, long start, long end)` | Retrieves members within the specified rank range (descending order).           |
| `rangeByScore(K key, double min, double max)`| Retrieves members with scores within the specified range.                       |
| `remove(K key, Object... values)`           | Removes one or more members from the ZSet.                                      |
| `incrementScore(K key, V value, double delta)`| Increments the score of a member by the specified delta.                        |
| `popMax(K key)`                             | Removes and returns the member with the highest score.                          |
| `popMin(K key)`                             | Removes and returns the member with the lowest score.                           |
| `unionAndStore(K key, Collection<K> otherKeys, K destKey)` | Performs union of multiple ZSets and stores the result in another ZSet.         |
| `intersectAndStore(K key, Collection<K> otherKeys, K destKey)` | Performs intersection of multiple ZSets and stores the result in another ZSet.  |

---

### **How to Use ZSetOperations**

#### 1. **Adding and Retrieving Members**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ZSetOperations;
import org.springframework.stereotype.Service;

import java.util.Set;

@Service
public class RedisZSetService {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    public void addToSortedSet(String key, String value, double score) {
        ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
        operations.add(key, value, score); // Add a member with a score
    }

    public Set<String> getRange(String key, long start, long end) {
        ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
        return operations.range(key, start, end); // Retrieve members by rank range
    }
}
```

#### 2. **Retrieve Members by Score Range**

```java
public Set<String> getByScoreRange(String key, double minScore, double maxScore) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    return operations.rangeByScore(key, minScore, maxScore); // Retrieve members by score range
}
```

#### 3. **Increment Scores**

```java
public void incrementScore(String key, String value, double delta) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    operations.incrementScore(key, value, delta); // Increment the score of a member
}
```

#### 4. **Remove Members**

```java
public void removeFromSortedSet(String key, String... values) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    operations.remove(key, values); // Remove specific members
}
```

---

### **Advanced Examples**

#### 1. **Get Rank of a Member**

```java
public Long getRank(String key, String value) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    return operations.rank(key, value); // Retrieve the rank of a member
}
```

#### 2. **Pop Max and Min Elements**

```java
public String popMax(String key) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    return operations.popMax(key).getValue(); // Remove and return the member with the highest score
}

public String popMin(String key) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    return operations.popMin(key).getValue(); // Remove and return the member with the lowest score
}
```

#### 3. **Union and Intersection of ZSets**

```java
public void unionAndStore(String key, Collection<String> otherKeys, String destKey) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    operations.unionAndStore(key, otherKeys, destKey); // Perform union and store the result
}

public void intersectAndStore(String key, Collection<String> otherKeys, String destKey) {
    ZSetOperations<String, String> operations = redisTemplate.opsForZSet();
    operations.intersectAndStore(key, otherKeys, destKey); // Perform intersection and store the result
}
```

---

### **Test Case Example**

#### Code:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.ZSetOperations;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class ZSetOperationTest {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    @Test
    void testZSetOperations() {
        ZSetOperations<String, String> operations = redisTemplate.opsForZSet();

        // Add elements to the ZSet
        operations.add("score", "Sevendi", 100);
        operations.add("score", "Eldrige", 85);
        operations.add("score", "Rifki", 95);

        // Pop and assert the max element
        assertEquals("Sevendi", operations.popMax("score").getValue());

        // Pop and assert the next max element
        assertEquals("Rifki", operations.popMax("score").getValue());

        // Pop and assert the last remaining element
        assertEquals("Eldrige", operations.popMax("score").getValue());
    }
}
```

#### Explanation:
1. **Add Elements**:
   - Adds members with scores to the ZSet.
2. **Pop Max**:
   - Removes and retrieves the member with the highest score.
3. **Assertions**:
   - Verify the order of the popped members based on their scores.

---

### **Use Cases for ZSetOperations**
1. **Leaderboards**:
   - Store and retrieve player scores in games.
2. **Task Scheduling**:
   - Use scores as timestamps to schedule tasks.
3. **Ranking Systems**:
   - Create ranking systems for products, users, or articles.
4. **Priority Queues**:
   - Implement priority queues by using scores as priorities.
5. **Time-Based Data**:
   - Store time-based data (e.g., logs or events) with timestamps as scores.

---

### **Advantages of ZSetOperations**
1. **Sorted Data**:
   - Automatically maintains the order of elements based on scores.
2. **Efficient Retrieval**:
   - Supports efficient range queries by rank or score.
3. **Flexible Operations**:
   - Allows union, intersection, and score manipulation.
4. **Atomicity**:
   - All operations are atomic, ensuring thread safety.
