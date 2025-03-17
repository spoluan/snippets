### **`@With` in Lombok**

The `@With` annotation in Lombok is used to generate **"with" methods** for immutable classes. These methods allow the creation of a new object with one or more fields modified, while keeping the original object unchanged. This is particularly useful when working with immutable objects, as it provides a convenient way to update specific fields without directly modifying the object.

---

### **Key Features of `@With`**

1. **Immutable Updates:**
   - Allows the creation of a new instance of a class with one or more fields updated, without modifying the original object.

2. **Method Naming:**
   - Generates methods named `withXxx()` (e.g., `withUsername()`) for each field, where `Xxx` is the name of the field.

3. **Field-Level or Class-Level Annotation:**
   - If applied at the **class level**, `@With` generates "with" methods for all fields.
   - If applied at the **field level**, "with" methods are generated only for the annotated fields.

4. **Integration with `@Value`:**
   - Often used together with `@Value` to ensure immutability of the class.

5. **Thread-Safe:**
   - Since the original object is not modified, the generated "with" methods are inherently thread-safe.

---

### **How `@With` Works**

#### **1. Basic Example**

##### **Code:**
```java
import lombok.Value;
import lombok.With;

@Value
@With
public class Register {
    String username;
    String password;
}
```

##### **Generated Code:**
```java
public final class Register {
    private final String username;
    private final String password;

    public Register(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPassword() {
        return this.password;
    }

    public Register withUsername(String username) {
        return this.username.equals(username) ? this : new Register(username, this.password);
    }

    public Register withPassword(String password) {
        return this.password.equals(password) ? this : new Register(this.username, password);
    }
}
```

##### **Usage:**
```java
public class Main {
    public static void main(String[] args) {
        Register register1 = new Register("user123", "password123");

        // Create a new object with a modified username
        Register register2 = register1.withUsername("newUser");

        System.out.println(register1.getUsername()); // Output: user123
        System.out.println(register2.getUsername()); // Output: newUser
        System.out.println(register1.getPassword()); // Output: password123
        System.out.println(register2.getPassword()); // Output: password123
    }
}
```

---

#### **2. Field-Level `@With`**

If `@With` is applied to individual fields, "with" methods are generated only for those fields.

##### **Code:**
```java
import lombok.Value;
import lombok.With;

@Value
public class User {
    @With
    String username;
    String email;
}
```

##### **Generated Code:**
```java
public final class User {
    private final String username;
    private final String email;

    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }

    public String getUsername() {
        return this.username;
    }

    public String getEmail() {
        return this.email;
    }

    public User withUsername(String username) {
        return this.username.equals(username) ? this : new User(username, this.email);
    }
}
```

---

#### **3. Testing `@With`**

##### **Code:**
```java
import lombok.Value;
import lombok.With;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

@Value
@With
public class Register {
    String username;
    String password;
}

public class RegisterTest {

    @Test
    void testWith() {
        Register register1 = new Register("Sevendi", "Sevendi");
        Register register2 = register1.withUsername("Eldrige");

        Assertions.assertEquals(register1.getPassword(), register2.getPassword());
        Assertions.assertNotEquals(register1.getUsername(), register2.getUsername());
    }
}
```

##### **Output:**
- The test verifies that:
  - The `password` field remains unchanged.
  - The `username` field is updated in the new object.

---

### **Behavior of `@With`**

| **Scenario**                             | **Behavior**                                                                                       |
|------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Class-Level Annotation**               | Generates "with" methods for all fields in the class.                                             |
| **Field-Level Annotation**               | Generates "with" methods only for the annotated fields.                                           |
| **Immutable Object**                     | Returns a new object with the updated field(s) while keeping the original object unchanged.       |
| **Thread-Safe**                          | Ensures thread safety as the original object is not modified.                                     |

---

### **Advanced Example**

#### **Using `@With` with `@Value` and Collections**

When using `@With` with collections, ensure that the collections are immutable to maintain the immutability of the class.

##### **Code:**
```java
import lombok.Value;
import lombok.With;

import java.util.List;

@Value
@With
public class Order {
    String orderId;
    List<String> items;
}
```

##### **Usage:**
```java
import java.util.Arrays;
import java.util.Collections;

public class Main {
    public static void main(String[] args) {
        Order order1 = new Order("ORD123", Collections.unmodifiableList(Arrays.asList("Item1", "Item2")));
        Order order2 = order1.withOrderId("ORD124");

        System.out.println(order1.getOrderId()); // Output: ORD123
        System.out.println(order2.getOrderId()); // Output: ORD124
        System.out.println(order1.getItems());   // Output: [Item1, Item2]
        System.out.println(order2.getItems());   // Output: [Item1, Item2]
    }
}
```

---

### **Advantages of `@With`**

1. **Simplifies Immutable Object Updates:**
   - Eliminates the need to manually write "with" methods for creating updated, immutable instances.

2. **Thread-Safe:**
   - As the original object is not modified, the generated methods are inherently thread-safe.

3. **Improves Code Readability:**
   - Provides a clear and concise way to update immutable objects.

4. **Reduces Boilerplate Code:**
   - Automatically generates "with" methods, saving time and effort.

---

### **Limitations of `@With`**

1. **Collections Are Not Automatically Immutable:**
   - If the class contains collections, you must manually ensure they are immutable.

2. **Only for Immutable Patterns:**
   - `@With` is not suitable for mutable classes, as it generates new instances instead of modifying existing ones.

3. **No Partial Updates:**
   - Each "with" method updates only one field. For multiple field updates, multiple "with" calls are required.

---

### **When to Use `@With`**

- When working with **immutable objects**.
- To easily create modified copies of an object without altering the original instance.
- In scenarios where **thread safety** is a priority.
- When using **functional programming patterns** that rely on immutability.

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **Immutable Updates**         | Creates a new object with updated fields while keeping the original object unchanged.            |
| **Method Naming**             | Generates methods like `withXxx()` for each field.                                               |
| **Field-Level or Class-Level**| Can be applied to individual fields or the entire class.                                         |
| **Integration with `@Value`** | Works best with `@Value` to ensure immutability.                                                 |
| **Thread-Safe**               | Ensures thread safety by not modifying the original object.                                      |

