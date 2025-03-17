### **`@NonNull` in Lombok**

The `@NonNull` annotation in Lombok is a convenient way to enforce that certain fields, parameters, or methods cannot accept or hold `null` values. It automatically generates null checks and throws a `NullPointerException` if a `null` value is provided where it is not allowed.

---

### **Key Features of `@NonNull`**

1. **Null Check Enforcement:**
   - Automatically adds null checks for fields, constructor parameters, and method parameters.

2. **Integration with Constructors:**
   - When used on fields, Lombok ensures that the generated constructors (`@RequiredArgsConstructor`, etc.) include null checks.

3. **Integration with Setters:**
   - If a setter is generated or manually written, Lombok adds a null check for the field.

4. **Throws `NullPointerException`:**
   - If a `null` value is passed to a `@NonNull` field or parameter, a `NullPointerException` is thrown with a descriptive error message.

5. **Improves Code Readability:**
   - Eliminates boilerplate null-checking code, making the code cleaner and easier to read.

---

### **How `@NonNull` Works**

#### **1. On Fields**
When applied to a field, Lombok ensures that:
   - The field is included in the generated `@RequiredArgsConstructor`, with a null check.
   - Any generated setter will include a null check.

#### **2. On Constructor Parameters**
When applied to a constructor parameter, Lombok:
   - Adds a null check in the constructor body.
   - Throws a `NullPointerException` if the parameter is `null`.

#### **3. On Method Parameters**
When applied to method parameters, Lombok:
   - Adds a null check at the beginning of the method.
   - Throws a `NullPointerException` if the parameter is `null`.

---

### **Basic Example**

#### **Code:**
```java
import lombok.Data;
import lombok.NonNull;

@Data
public class Member {
    @NonNull
    private String id;

    @NonNull
    private String name;
}
```

#### **Generated Code:**
```java
public class Member {
    private final String id;
    private final String name;

    public Member(@NonNull String id, @NonNull String name) {
        if (id == null) {
            throw new NullPointerException("id is marked non-null but is null");
        }
        if (name == null) {
            throw new NullPointerException("name is marked non-null but is null");
        }
        this.id = id;
        this.name = name;
    }

    public void setId(@NonNull String id) {
        if (id == null) {
            throw new NullPointerException("id is marked non-null but is null");
        }
        this.id = id;
    }

    public void setName(@NonNull String name) {
        if (name == null) {
            throw new NullPointerException("name is marked non-null but is null");
        }
        this.name = name;
    }
}
```

#### **Usage:**
```java
public class Main {
    public static void main(String[] args) {
        Member member = new Member("123", "John");

        // Throws NullPointerException
        // Member invalidMember = new Member(null, "John");
    }
}
```

---

### **Advanced Examples**

#### **1. Using `@NonNull` with `@RequiredArgsConstructor`**

When `@NonNull` is used on fields, and `@RequiredArgsConstructor` is added to the class, Lombok generates a constructor with null checks for all `@NonNull` fields.

##### **Code:**
```java
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
public class User {
    @NonNull
    private final String username;
    private final String email;
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

#### **2. Using `@NonNull` on Method Parameters**

##### **Code:**
```java
import lombok.NonNull;

public class Service {
    public void process(@NonNull String data) {
        System.out.println("Processing: " + data);
    }
}
```

##### **Generated Code:**
```java
public void process(@NonNull String data) {
    if (data == null) {
        throw new NullPointerException("data is marked non-null but is null");
    }
    System.out.println("Processing: " + data);
}
```

##### **Usage:**
```java
Service service = new Service();
service.process("Valid Data"); // Works fine
service.process(null);         // Throws NullPointerException
```

---

#### **3. Using `@NonNull` with Setters**

If a setter is generated or manually written for a `@NonNull` field, Lombok ensures a null check is added.

##### **Code:**
```java
import lombok.Data;
import lombok.NonNull;

@Data
public class Product {
    @NonNull
    private String name;
    private double price;
}
```

##### **Generated Setter:**
```java
public void setName(@NonNull String name) {
    if (name == null) {
        throw new NullPointerException("name is marked non-null but is null");
    }
    this.name = name;
}
```

---

#### **4. Using `@NonNull` with `@Builder`**

When used with `@Builder`, `@NonNull` ensures that null checks are added for the required fields during the build process.

##### **Code:**
```java
import lombok.Builder;
import lombok.NonNull;

@Builder
public class Order {
    @NonNull
    private String orderId;
    private String product;
}
```

##### **Generated Code:**
```java
public static class OrderBuilder {
    private String orderId;

    public OrderBuilder orderId(@NonNull String orderId) {
        if (orderId == null) {
            throw new NullPointerException("orderId is marked non-null but is null");
        }
        this.orderId = orderId;
        return this;
    }
}
```

##### **Usage:**
```java
Order order = Order.builder()
                   .orderId("ORD123")
                   .product("Laptop")
                   .build();

// Throws NullPointerException
// Order invalidOrder = Order.builder().product("Laptop").build();
```

---

### **Behavior of `@NonNull`**

| **Scenario**                               | **Behavior**                                                                                       |
|--------------------------------------------|---------------------------------------------------------------------------------------------------|
| **Field with `@NonNull`**                  | Null checks are added to constructors and setters.                                                |
| **Constructor Parameter with `@NonNull`**  | Null checks are added to the constructor body.                                                    |
| **Method Parameter with `@NonNull`**       | Null checks are added at the beginning of the method.                                             |
| **Field with `final` and `@NonNull`**      | Lombok ensures the field is initialized in the constructor or throws a `NullPointerException`.     |

---

### **Limitations of `@NonNull`**

1. **Runtime Null Checks Only:**
   - The null checks are performed at runtime and do not provide compile-time safety.

2. **Descriptive Error Messages:**
   - While the error message is descriptive, it might not always point to the exact issue in larger applications.

3. **No Support for Primitive Types:**
   - `@NonNull` cannot be applied to primitive fields or parameters (e.g., `int`, `double`).

---

### **When to Use `@NonNull`**

- When you want to enforce non-null constraints on fields, constructor parameters, or method parameters.
- To reduce boilerplate null-checking code.
- To improve code readability and maintainability.
- When working with frameworks or libraries that require strict null safety.

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **Null Checks**               | Automatically adds null checks for fields, constructor parameters, and method parameters.        |
| **Error Handling**            | Throws `NullPointerException` with a descriptive message if a null value is passed.             |
| **Integration with Lombok**   | Works seamlessly with `@Data`, `@Builder`, `@RequiredArgsConstructor`, and other Lombok features.|
| **Improves Code Readability** | Eliminates boilerplate null-checking code.                                                      |

