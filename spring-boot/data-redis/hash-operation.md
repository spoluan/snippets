### **Hash Operations in Redis**

Redis **Hash** is a data structure that stores key-value pairs, similar to a dictionary or map in programming. Hashes are ideal for representing objects with multiple fields, such as user profiles or configuration data. Each hash is identified by a **key**, and within the hash, there are multiple **fields** (hash keys) and their corresponding **values**.

In **Spring Data Redis**, the **`HashOperations`** class provides an abstraction to interact with Redis Hashes.

---

### **Key Features of Hash Operations**
1. **Field-Value Storage**:
   - Each hash is a mapping of fields (hash keys) to values.

2. **Efficient Storage**:
   - Suitable for storing objects with multiple attributes, as it avoids creating multiple top-level keys.

3. **Partial Updates**:
   - Modify specific fields without affecting the entire hash.

4. **Integration**:
   - The `HashOperations` class in Spring Data Redis maps directly to Redis hash commands.

---

### **Common Methods in HashOperations**

| **Method**                                   | **Description**                                                                 |
|----------------------------------------------|---------------------------------------------------------------------------------|
| `put(K key, HK hashKey, HV value)`           | Adds or updates a field-value pair in the hash.                                 |
| `putAll(K key, Map<? extends HK, ? extends HV> map)` | Adds multiple field-value pairs to the hash.                                    |
| `get(K key, Object hashKey)`                 | Retrieves the value associated with a specific field.                           |
| `entries(K key)`                             | Retrieves all field-value pairs in the hash as a map.                           |
| `keys(K key)`                                | Retrieves all the field names (hash keys) in the hash.                          |
| `values(K key)`                              | Retrieves all the values in the hash.                                           |
| `hasKey(K key, Object hashKey)`              | Checks if a specific field exists in the hash.                                  |
| `delete(K key, Object... hashKeys)`          | Deletes one or more fields from the hash.                                       |
| `size(K key)`                                | Returns the number of fields in the hash.                                       |
| `increment(K key, HK hashKey, long delta)`   | Increments the value of a field by a specified delta.                           |
| `multiGet(K key, Collection<HK> hashKeys)`   | Retrieves the values of multiple fields in the hash.                            |

---

### **How to Use HashOperations**

#### 1. **Adding and Retrieving Field-Value Pairs**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.HashOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.Map;

@Service
public class RedisHashService {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    public void addToHash(String key, String hashKey, String value) {
        HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
        operations.put(key, hashKey, value); // Add a field-value pair
    }

    public Object getFromHash(String key, String hashKey) {
        HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
        return operations.get(key, hashKey); // Retrieve the value of a specific field
    }

    public Map<Object, Object> getAllFromHash(String key) {
        HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
        return operations.entries(key); // Retrieve all field-value pairs
    }
}
```

#### 2. **Adding Multiple Field-Value Pairs**

```java
public void addMultipleToHash(String key, Map<String, String> map) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    operations.putAll(key, map); // Add multiple field-value pairs
}
```

#### 3. **Checking for Field Existence**

```java
public boolean hasField(String key, String hashKey) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    return operations.hasKey(key, hashKey); // Check if a specific field exists
}
```

#### 4. **Deleting Fields**

```java
public void deleteFromHash(String key, String... hashKeys) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    operations.delete(key, (Object[]) hashKeys); // Delete one or more fields
}
```

---

### **Advanced Examples**

#### 1. **Incrementing Field Values**

```java
public void incrementField(String key, String hashKey, long delta) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    operations.increment(key, hashKey, delta); // Increment the value of a field
}
```

#### 2. **Retrieving All Keys and Values**

```java
public Set<Object> getAllKeys(String key) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    return operations.keys(key); // Retrieve all field names
}

public List<Object> getAllValues(String key) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    return operations.values(key); // Retrieve all field values
}
```

#### 3. **Retrieving Multiple Field Values**

```java
public List<Object> getMultipleFields(String key, List<String> hashKeys) {
    HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();
    return operations.multiGet(key, hashKeys); // Retrieve values of multiple fields
}
```

---

### **Test Case Example**

#### Code:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.HashOperations;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class HashOperationTest {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    @Test
    void testHashOperations() {
        HashOperations<String, Object, Object> operations = redisTemplate.opsForHash();

        // Add fields to the hash
        operations.put("user:1", "id", "1");
        operations.put("user:1", "name", "Sevendi");
        operations.put("user:1", "email", "Sevendi@example.com");

        // Retrieve and assert field values
        assertEquals("1", operations.get("user:1", "id"));
        assertEquals("Sevendi", operations.get("user:1", "name"));
        assertEquals("Sevendi@example.com", operations.get("user:1", "email"));

        // Clean up
        redisTemplate.delete("user:1");
    }
}
```

#### Explanation:
1. **Add Fields**:
   - Adds multiple fields to the hash.
2. **Retrieve Fields**:
   - Retrieves and asserts the values of specific fields.
3. **Cleanup**:
   - Deletes the hash after testing.

---

### **Use Cases for HashOperations**
1. **User Profiles**:
   - Store user details like ID, name, email, and preferences.
2. **Configuration Data**:
   - Maintain application settings or feature flags.
3. **Session Management**:
   - Store session data for users.
4. **Caching**:
   - Cache objects with multiple attributes.
5. **Inventory Management**:
   - Store product details like name, price, and stock count.

---

### **Advantages of HashOperations**
1. **Efficient Storage**:
   - Reduces the number of top-level keys by grouping related fields under a single key.
2. **Field-Level Operations**:
   - Supports operations on individual fields without affecting the entire hash.
3. **Atomicity**:
   - All operations are atomic, ensuring thread safety.
4. **Scalability**:
   - Suitable for storing and managing large amounts of structured data.

