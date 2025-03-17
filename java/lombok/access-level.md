### **Access Level in Lombok**

Lombok provides the ability to control the **visibility** (access level) of the generated getter and setter methods using the **`AccessLevel`** enumeration. By default, Lombok generates **public** getter and setter methods. However, you can customize this behavior by specifying the desired access level.

---

### **1. Default Behavior**

- When using `@Getter` or `@Setter` without specifying an access level, the generated methods are **public** by default.
- Example:

```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class User {
    private String username;
    private String password;
}
```

**Generated Code**:
```java
public class User {
    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

---

### **2. Customizing Access Levels**

You can specify the visibility of the generated getter or setter methods using the `value` parameter of `@Getter` or `@Setter`. The possible values are:

| **Access Level** | **Description**                     |
|-------------------|-------------------------------------|
| `PUBLIC`          | Method is public (default).        |
| `PROTECTED`       | Method is protected.               |
| `PACKAGE`         | Method is package-private.         |
| `PRIVATE`         | Method is private.                 |
| `NONE`            | No method is generated.            |

---

### **3. Examples of Access Levels**

#### **a. Protected Setter**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Getter
public class Login {
    @Setter(value = AccessLevel.PROTECTED)
    private String username;

    @Setter(value = AccessLevel.PROTECTED)
    private String password;
}
```

**Generated Code**:
```java
public class Login {
    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }

    protected void setUsername(String username) {
        this.username = username;
    }

    protected void setPassword(String password) {
        this.password = password;
    }
}
```

---

#### **b. Private Getter**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Setter
public class Account {
    @Getter(value = AccessLevel.PRIVATE)
    private String accountId;

    private double balance;
}
```

**Generated Code**:
```java
public class Account {
    private String accountId;
    private double balance;

    private String getAccountId() {
        return accountId;
    }

    public void setAccountId(String accountId) {
        this.accountId = accountId;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }
}
```

---

#### **c. Package-Private Setter**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Getter
public class Product {
    private String name;

    @Setter(value = AccessLevel.PACKAGE)
    private double price;
}
```

**Generated Code**:
```java
public class Product {
    private String name;
    private double price;

    public String getName() {
        return name;
    }

    double getPrice() {
        return price;
    }

    void setPrice(double price) {
        this.price = price;
    }
}
```

---

#### **d. No Getter or Setter (`NONE`)**
If you want to prevent Lombok from generating a getter or setter for a specific field, use `AccessLevel.NONE`.

```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Getter
@Setter
public class Employee {
    private String name;

    @Getter(AccessLevel.NONE)
    @Setter(AccessLevel.NONE)
    private String ssn; // No getter or setter generated
}
```

**Generated Code**:
```java
public class Employee {
    private String name;
    private String ssn;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### **4. Class-Level Access Level**

When `@Getter` or `@Setter` is applied at the class level, the specified access level applies to all fields in the class.

#### **Example**:
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Getter(AccessLevel.PROTECTED)
@Setter(AccessLevel.PROTECTED)
public class Customer {
    private String id;
    private String name;
}
```

**Generated Code**:
```java
public class Customer {
    private String id;
    private String name;

    protected String getId() {
        return id;
    }

    protected void setId(String id) {
        this.id = id;
    }

    protected String getName() {
        return name;
    }

    protected void setName(String name) {
        this.name = name;
    }
}
```

---

### **5. Mixed Access Levels**

You can mix access levels for different fields in the same class by applying `@Getter` or `@Setter` with specific access levels on individual fields.

#### **Example**:
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

@Getter
@Setter
public class Order {
    private String orderId;

    @Setter(AccessLevel.PROTECTED)
    private String customerName;

    @Getter(AccessLevel.PRIVATE)
    private double totalAmount;
}
```

**Generated Code**:
```java
public class Order {
    private String orderId;
    private String customerName;
    private double totalAmount;

    public String getOrderId() {
        return orderId;
    }

    public void setOrderId(String orderId) {
        this.orderId = orderId;
    }

    protected void setCustomerName(String customerName) {
        this.customerName = customerName;
    }

    private double getTotalAmount() {
        return totalAmount;
    }
}
```

---

### **6. Real-World Use Case**

#### **Code**:
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

public class BankAccount {
    @Getter
    private String accountNumber;

    @Setter(AccessLevel.PROTECTED)
    private double balance;

    @Getter(AccessLevel.PRIVATE)
    private String pin;

    public BankAccount(String accountNumber, double balance, String pin) {
        this.accountNumber = accountNumber;
        this.balance = balance;
        this.pin = pin;
    }
}
```

**Generated Code**:
```java
public class BankAccount {
    private String accountNumber;
    private double balance;
    private String pin;

    public String getAccountNumber() {
        return accountNumber;
    }

    protected void setBalance(double balance) {
        this.balance = balance;
    }

    private String getPin() {
        return pin;
    }
}
```

---

### **7. Summary**

- **Default Access Level**: `PUBLIC`.
- **Custom Access Levels**: Can be set using `AccessLevel` (e.g., `PROTECTED`, `PRIVATE`, etc.).
- **Class-Level vs Field-Level**: You can apply `@Getter` or `@Setter` at the class level (applies to all fields) or at the field level (applies to specific fields).
- **Prevent Method Generation**: Use `AccessLevel.NONE` to exclude a field from having a getter or setter.
