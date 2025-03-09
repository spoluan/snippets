### **Geo Operations in Redis**

Redis **Geo Operations** allow you to store, retrieve, and manipulate geospatial data such as latitude and longitude coordinates. You can use these features to build location-based services like finding nearby stores, users, or points of interest.

In **Spring Data Redis**, the **`GeoOperations`** class provides an abstraction to work with Redis Geo commands.

---

### **Key Features of Geo Operations**
1. **Store Geospatial Data**:
   - Store locations as longitude, latitude, and an associated member (e.g., store name or user ID).

2. **Distance Calculation**:
   - Calculate the distance between two geospatial points.

3. **Radius Search**:
   - Find members within a specified radius from a given point.

4. **Geo Hashing**:
   - Retrieve the geohash of a location.

5. **Integration**:
   - The `GeoOperations` class in Spring Data Redis maps directly to Redis Geo commands.

---

### **Common Methods in GeoOperations**

| **Method**                                      | **Description**                                                                 |
|-------------------------------------------------|---------------------------------------------------------------------------------|
| `add(K key, Point point, M member)`             | Adds a geospatial point with a member to the key.                               |
| `add(K key, Map<M, Point> memberCoordinateMap)` | Adds multiple geospatial points to the key.                                     |
| `distance(K key, M member1, M member2, Metric metric)` | Calculates the distance between two members.                                    |
| `position(K key, M member)`                    | Retrieves the geospatial position (longitude and latitude) of a member.         |
| `hash(K key, M... members)`                    | Retrieves the geohash of one or more members.                                   |
| `radius(K key, Circle within)`                 | Finds members within a specified radius from a center point.                    |
| `radius(K key, Circle within, GeoRadiusCommandArgs args)` | Finds members within a radius with additional options (e.g., sorting).          |
| `search(K key, Circle within)`                 | An alias for `radius`, used to perform a radius search.                         |
| `remove(K key, M... members)`                  | Removes one or more members from the geospatial index.                          |

---

### **How to Use GeoOperations**

#### 1. **Adding Geospatial Data**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.geo.Point;
import org.springframework.data.redis.core.GeoOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

@Service
public class GeoService {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    public void addLocation(String key, String member, double longitude, double latitude) {
        GeoOperations<String, String> operations = redisTemplate.opsForGeo();
        operations.add(key, new Point(longitude, latitude), member); // Add a geospatial point
    }
}
```

#### 2. **Calculating Distance Between Two Points**

```java
import org.springframework.data.geo.Distance;
import org.springframework.data.geo.Metrics;

public Distance getDistance(String key, String member1, String member2) {
    GeoOperations<String, String> operations = redisTemplate.opsForGeo();
    return operations.distance(key, member1, member2, Metrics.KILOMETERS); // Calculate distance
}
```

#### 3. **Finding Locations Within a Radius**

```java
import org.springframework.data.geo.Circle;
import org.springframework.data.geo.Distance;
import org.springframework.data.geo.Point;
import org.springframework.data.geo.Metrics;
import org.springframework.data.redis.connection.RedisGeoCommands.GeoLocation;
import org.springframework.data.redis.core.GeoOperations;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;
import org.springframework.data.redis.core.GeoResults;

@Service
public class GeoService {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    public GeoResults<RedisGeoCommands.GeoLocation<String>> findNearby(String key, double longitude, double latitude, double radius) {
        GeoOperations<String, String> operations = redisTemplate.opsForGeo();
        Circle circle = new Circle(new Point(longitude, latitude), new Distance(radius, Metrics.KILOMETERS));
        return operations.radius(key, circle); // Find locations within the radius
    }
}
```

#### 4. **Retrieving Geohash**

```java
public List<String> getGeohash(String key, String... members) {
    GeoOperations<String, String> operations = redisTemplate.opsForGeo();
    return operations.hash(key, members); // Retrieve geohashes for members
}
```

#### 5. **Retrieving Geospatial Position**

```java
import java.util.List;

public List<Point> getPositions(String key, String... members) {
    GeoOperations<String, String> operations = redisTemplate.opsForGeo();
    return operations.position(key, members); // Retrieve positions of members
}
```

#### 6. **Removing Locations**

```java
public void removeLocations(String key, String... members) {
    GeoOperations<String, String> operations = redisTemplate.opsForGeo();
    operations.remove(key, members); // Remove members from the geospatial index
}
```

---

### **Test Case Example**

#### Code:
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.geo.Circle;
import org.springframework.data.geo.Distance;
import org.springframework.data.geo.Metrics;
import org.springframework.data.geo.Point;
import org.springframework.data.redis.core.GeoOperations;
import org.springframework.data.redis.core.RedisTemplate;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest
public class GeoOperationTest {

    @Autowired
    private RedisTemplate<String, String> redisTemplate;

    @Test
    void testGeoOperations() {
        GeoOperations<String, String> operations = redisTemplate.opsForGeo();

        // Add locations
        operations.add("sellers", new Point(106.822702, -6.177590), "Toko A");
        operations.add("sellers", new Point(106.820889, -6.174964), "Toko B");

        // Calculate distance
        Distance distance = operations.distance("sellers", "Toko A", "Toko B", Metrics.KILOMETERS);
        assertEquals(0.3543, distance.getValue(), 0.0001);

        // Search within radius
        GeoResults<RedisGeoCommands.GeoLocation<String>> results = operations.radius(
                "sellers",
                new Circle(new Point(106.821825, -6.175105), new Distance(5, Metrics.KILOMETERS))
        );
        assertEquals(2, results.getContent().size());
        assertEquals("Toko A", results.getContent().get(0).getContent().getName());
        assertEquals("Toko B", results.getContent().get(1).getContent().getName());
    }
}
```

#### Explanation:
1. **Add Locations**:
   - Adds two locations (`Toko A` and `Toko B`) with their coordinates.
2. **Calculate Distance**:
   - Calculates the distance between the two locations in kilometers.
3. **Search Within Radius**:
   - Finds all locations within a 5 km radius of a given point.
4. **Assertions**:
   - Verifies the results of the operations.

---

### **Use Cases for Geo Operations**
1. **Store Locator**:
   - Find the nearest stores to a customer.
2. **Ride-Hailing Services**:
   - Match riders with nearby drivers.
3. **Delivery Services**:
   - Identify delivery points within a specific area.
4. **Event Management**:
   - Locate events near a user's current location.
5. **Geofencing**:
   - Trigger actions when a user enters or exits a predefined area.

