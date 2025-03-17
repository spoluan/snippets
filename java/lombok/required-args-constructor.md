### **`@RequiredArgsConstructor` in Lombok**

The `@RequiredArgsConstructor` annotation in Lombok generates a constructor that includes **only the required fields**:
- Fields marked as `final`.
- Fields annotated with `@NonNull` (even if they are not `final`).

This is useful when you want to create a constructor that enforces initialization of mandatory fields while leaving optional fields unset.

---

### **How It Works**

1. **Mandatory Fields Only:**  
   The generated constructor will include parameters for:
   - `final` fields.
   - Fields annotated with `@NonNull`.

2. **Optional Fields Excluded:**  
   Fields that are neither `final` nor annotated with `@NonNull` are excluded from the constructor.

3. **Null Checks for `@NonNull`:**  
   If a field is annotated with `@NonNull`, Lombok will add a null check in the generated constructor to ensure that a `null` value cannot be passed.

4. **Access Level:**  
   You can control the visibility of the generated constructor using the `access` parameter (e.g., `PRIVATE`, `PROTECTED`, `PUBLIC`).

---

### **Key Points**

- **Default Behavior:** Only `final` and `@NonNull` fields are included in the constructor.
- **Null Safety:** Null checks are added automatically for `@NonNull` fields.
- **Access Control:** You can specify the access level of the constructor using `@RequiredArgsConstructor(access = AccessLevel.PRIVATE)`.

---

### **Examples**

#### **1. Basic Example with `final` Fields**

##### **Code:**
```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class User {
    private final String id;
    private String name; // Optional field
}
```

##### **Generated Code:**
```java
public class User {
    private final String id;
    private String name;

    // Constructor with only the required field
    public User(final String id) {
        this.id = id;
    }
}
```

##### **Usage:**
```java
User user = new User("123"); // Constructor requires "id"
```

---

#### **2. Example with `@NonNull` Fields**

##### **Code:**
```java
import lombok.Getter;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class Product {
    @NonNull
    private String id;
    private String name; // Optional field
}
```

##### **Generated Code:**
```java
public class Product {
    private final String id;
    private String name;

    // Constructor with null check for @NonNull field
    public Product(final String id) {
        if (id == null) {
            throw new NullPointerException("id is marked non-null but is null");
        }
        this.id = id;
    }
}
```

##### **Usage:**
```java
Product product = new Product("P001"); // Valid
Product invalidProduct = new Product(null); // Throws NullPointerException
```

---

#### **3. Example with Both `final` and `@NonNull` Fields**

##### **Code:**
```java
import lombok.Getter;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor
public class Order {
    private final String orderId;
    @NonNull
    private String customerName;
    private String product; // Optional field
}
```

##### **Generated Code:**
```java
public class Order {
    private final String orderId;
    private final String customerName;
    private String product;

    // Constructor with both final and @NonNull fields
    public Order(final String orderId, final String customerName) {
        if (customerName == null) {
            throw new NullPointerException("customerName is marked non-null but is null");
        }
        this.orderId = orderId;
        this.customerName = customerName;
    }
}
```

##### **Usage:**
```java
Order order = new Order("O001", "John Doe"); // Valid
Order invalidOrder = new Order("O002", null); // Throws NullPointerException
```

---

#### **4. Example with Access Level Control**

##### **Code:**
```java
import lombok.Getter;
import lombok.RequiredArgsConstructor;
import lombok.AccessLevel;

@Getter
@RequiredArgsConstructor(access = AccessLevel.PRIVATE)
public class Employee {
    private final String id;
    private String name; // Optional field

    // Static factory method
    public static Employee create(String id) {
        return new Employee(id);
    }
}
```

##### **Generated Code:**
```java
public class Employee {
    private final String id;
    private String name;

    // Private constructor
    private Employee(final String id) {
        this.id = id;
    }

    // Static factory method
    public static Employee create(String id) {
        return new Employee(id);
    }
}
```

##### **Usage:**
```java
Employee emp = Employee.create("E001"); // Valid via static method
Employee invalidEmp = new Employee("E001"); // Not allowed, constructor is private
```

---

#### **5. Real-World Example: Merchant Class**

##### **Code:**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.EqualsAndHashCode;
import lombok.ToString;
import lombok.RequiredArgsConstructor;

@Getter
@Setter
@EqualsAndHashCode
@ToString
@RequiredArgsConstructor
public class Merchant {
    private final String id; // Required field
    private String name;     // Optional field
}
```

##### **Generated Code:**
```java
public class Merchant {
    private final String id;
    private String name;

    // Constructor with only the required field
    public Merchant(final String id) {
        this.id = id;
    }
}
```

##### **Usage:**
```java
Merchant merchant = new Merchant("M001"); // Constructor requires "id"
merchant.setName("Store A");
```

---

#### **6. Example with `@NonNull` and Static Factory Method**

##### **Code:**
```java
import lombok.Getter;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

@Getter
@RequiredArgsConstructor(staticName = "of")
public class Customer {
    @NonNull
    private String id;
    private String name; // Optional field
}
```

##### **Generated Code:**
```java
public class Customer {
    private final String id;
    private String name;

    // Private constructor with null check
    private Customer(final String id) {
        if (id == null) {
            throw new NullPointerException("id is marked non-null but is null");
        }
        this.id = id;
    }

    // Static factory method
    public static Customer of(final String id) {
        return new Customer(id);
    }
}
```

##### **Usage:**
```java
Customer customer = Customer.of("C001"); // Calls the static method
Customer invalidCustomer = Customer.of(null); // Throws NullPointerException
```

---

### **Summary of `@RequiredArgsConstructor`**

| **Feature**                 | **Description**                                                                                   |
|-----------------------------|---------------------------------------------------------------------------------------------------|
| **Mandatory Fields**         | Includes only `final` and `@NonNull` fields in the constructor.                                  |
| **Optional Fields Excluded** | Fields that are not `final` or `@NonNull` are excluded from the constructor.                     |
| **Null Safety**              | Adds null checks for `@NonNull` fields in the constructor.                                       |
| **Access Control**           | Constructor visibility can be controlled using the `access` parameter (e.g., `PRIVATE`, `PUBLIC`). |
| **Static Method**            | Can generate a static factory method using `staticName`.                                         |

---

### **When to Use `@RequiredArgsConstructor`**

- When you need to initialize only the mandatory fields (`final` or `@NonNull`).
- When you want to avoid creating constructors manually for required fields.
- When you want to enforce null safety for required fields with `@NonNull`.
