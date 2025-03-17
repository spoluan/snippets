### **`@Value` in Lombok**

The `@Value` annotation in Lombok is a shortcut for creating **immutable classes** in Java. Immutable classes are classes whose state cannot be changed after they are created. Lombok simplifies the process of creating such classes by automatically generating the required boilerplate code.

---

### **Key Features of `@Value`**

1. **Final Class:**
   - The class is automatically marked as `final`, preventing it from being subclassed.

2. **Final Fields:**
   - All fields in the class are automatically marked as `private` and `final`.

3. **No Setters:**
   - No setter methods are generated, ensuring that the fields cannot be modified after the object is created.

4. **All-Args Constructor:**
   - A constructor is generated with parameters for all fields, allowing the fields to be initialized at the time of object creation.

5. **Getter Methods:**
   - Getter methods are generated for all fields, providing read-only access.

6. **Overrides `equals()`, `hashCode()`, and `toString()`:**
   - Lombok automatically generates implementations for these methods based on the fields of the class.

7. **Thread-Safe:**
   - Since the fields are final and the object cannot be modified, the class is inherently thread-safe.

---

### **How `@Value` Works**

#### **1. Basic Example**

##### **Code:**
```java
import lombok.Value;

@Value
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

    public Register(final String username, final String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return this.username;
    }

    public String getPassword() {
        return this.password;
    }

    @Override
    public boolean equals(final Object o) {
        if (o == this) return true;
        if (!(o instanceof Register)) return false;
        final Register other = (Register) o;
        return this.username.equals(other.username) && this.password.equals(other.password);
    }

    @Override
    public int hashCode() {
        return username.hashCode() * 31 + password.hashCode();
    }

    @Override
    public String toString() {
        return "Register(username=" + this.username + ", password=" + this.password + ")";
    }
}
```

##### **Usage:**
```java
public class Main {
    public static void main(String[] args) {
        Register register = new Register("user123", "password");

        System.out.println(register.getUsername()); // Output: user123
        System.out.println(register.getPassword()); // Output: password
    }
}
```

---

### **Detailed Breakdown of `@Value` Behavior**

#### **1. Class Declaration**
- The class is automatically marked as `final` so that it cannot be subclassed.
- This ensures immutability since subclasses could potentially modify the behavior.

#### **2. Fields**
- All fields are marked as `private` and `final`.
- This ensures that the fields cannot be modified after the object is created.

#### **3. Constructor**
- A constructor is generated for all fields, allowing the fields to be initialized during object creation.

#### **4. Getters**
- Getter methods are generated for all fields, providing read-only access.

#### **5. `equals()` and `hashCode()`**
- The `equals()` method compares all fields to determine equality.
- The `hashCode()` method generates a hash code based on the fields.

#### **6. `toString()`**
- The `toString()` method generates a string representation of the object, including all fields.

---

### **Advanced Features**

#### **1. Customizing Field Accessibility**
You can use the `@NonNull` annotation to enforce null checks for specific fields.

##### **Code:**
```java
import lombok.Value;
import lombok.NonNull;

@Value
public class User {
    @NonNull
    String username;
    String email;
}
```

##### **Generated Constructor:**
```java
public User(@NonNull String username, String email) {
    if (username == null) {
        throw new NullPointerException("username is marked non-null but is null");
    }
    this.username = username;
    this.email = email;
}
```

---

#### **2. Using Collections**
If your class contains collections, they are not automatically made immutable. You need to ensure immutability manually.

##### **Code:**
```java
import lombok.Value;
import java.util.List;

@Value
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
        Order order = new Order("ORD123", Collections.unmodifiableList(Arrays.asList("Item1", "Item2")));
        System.out.println(order);
    }
}
```

---

#### **3. Customizing `toString()`, `equals()`, or `hashCode()`**
If needed, you can override any of these methods manually.

##### **Code:**
```java
import lombok.Value;

@Value
public class Product {
    String name;
    double price;

    @Override
    public String toString() {
        return "Product: " + name + " (Price: $" + price + ")";
    }
}
```

---

### **When to Use `@Value`**

- When creating **immutable classes**.
- For **data transfer objects (DTOs)**, where the object's state should not change.
- For **thread-safe classes**, where immutability ensures no concurrent modification issues.
- When you want to reduce boilerplate code for `equals()`, `hashCode()`, and `toString()`.

---

### **Advantages of `@Value`**

1. **Simplicity:**
   - Eliminates boilerplate code for immutable classes.
2. **Thread-Safety:**
   - Immutable objects are inherently thread-safe.
3. **Readability:**
   - The code is cleaner and easier to understand.
4. **Consistency:**
   - Automatically generates consistent implementations of `equals()`, `hashCode()`, and `toString()`.

---

### **Limitations of `@Value`**

1. **Collections Are Not Immutable:**
   - Collections need to be explicitly made immutable.
2. **No Support for Default Values:**
   - You cannot define default values for fields directly with `@Value`.
3. **All Fields Are Final:**
   - You cannot have mutable fields in a class annotated with `@Value`.

---

### **Comparison Between `@Value` and `@Data`**

| Feature                     | `@Value`                                  | `@Data`                                   |
|-----------------------------|-------------------------------------------|-------------------------------------------|
| **Mutability**              | Immutable                                | Mutable                                   |
| **Final Fields**            | All fields are `final`                   | Fields are not `final` by default         |
| **Setters**                 | No setters generated                     | Setters are generated                     |
| **Thread-Safe**             | Yes                                      | No                                        |
| **Use Case**                | Immutable objects, DTOs                  | POJOs, mutable objects                    |

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **Immutable Class**           | Automatically creates immutable classes by marking the class and fields as `final`.             |
| **No Setters**                | Ensures fields cannot be modified after object creation.                                         |
| **All-Args Constructor**      | Automatically generates a constructor for all fields.                                           |
| **Getter Methods**            | Generates getter methods for all fields.                                                        |
| **Overrides `equals()`**      | Compares objects based on their fields.                                                         |
| **Overrides `hashCode()`**    | Generates a hash code based on the fields.                                                      |
| **Overrides `toString()`**    | Generates a string representation of the object.                                                |

