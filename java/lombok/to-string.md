### **`@ToString` in Lombok**

The `@ToString` annotation in Lombok is used to automatically generate a `toString()` method for a class. The generated `toString()` method provides a string representation of the object, including the values of its fields.

---

### **Features of `@ToString`**

1. **Automatic Method Generation:**
   - Generates a `toString()` method that includes all fields in the class by default.
   - The output format is:  
     `ClassName(field1=value1, field2=value2, ...)`

2. **Customizable Behavior:**
   - You can exclude specific fields using the `exclude` parameter.
   - You can include fields from the superclass using the `callSuper` parameter.
   - You can control whether field names are included using the `onlyExplicitlyIncluded` parameter.

3. **Integration with Other Lombok Annotations:**
   - Works well with annotations like `@Getter`, `@Setter`, `@EqualsAndHashCode`, etc.

---

### **Key Parameters**

| **Parameter**              | **Description**                                                                                  |
|----------------------------|--------------------------------------------------------------------------------------------------|
| `exclude`                  | Excludes specific fields from the `toString()` method.                                           |
| `callSuper`                | Includes the `toString()` output of the superclass.                                              |
| `onlyExplicitlyIncluded`   | Includes only those fields explicitly annotated with `@ToString.Include`.                        |
| `doNotUseGetters`          | If `true`, directly accesses fields instead of using getter methods in the `toString()` method. |

---

### **Examples**

#### **1. Basic Usage**

##### **Code:**
```java
import lombok.ToString;

@ToString
public class User {
    private String id;
    private String name;
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "User(id=" + this.id + ", name=" + this.name + ")";
}
```

##### **Usage:**
```java
User user = new User();
user.setId("123");
user.setName("John Doe");
System.out.println(user); // Output: User(id=123, name=John Doe)
```

---

#### **2. Excluding Specific Fields**

##### **Code:**
```java
import lombok.ToString;

@ToString(exclude = "password")
public class Login {
    private String username;
    private String password;
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "Login(username=" + this.username + ")";
}
```

##### **Usage:**
```java
Login login = new Login();
login.setUsername("admin");
login.setPassword("secret");
System.out.println(login); // Output: Login(username=admin)
```

---

#### **3. Including Superclass Fields**

##### **Code:**
```java
import lombok.ToString;

@ToString(callSuper = true)
public class Employee extends Person {
    private String department;
}

@ToString
class Person {
    private String name;
    private int age;
}
```

##### **Generated `toString()` Method:**
```java
// In Employee class
public String toString() {
    return super.toString() + "(department=" + this.department + ")";
}

// In Person class
public String toString() {
    return "Person(name=" + this.name + ", age=" + this.age + ")";
}
```

##### **Usage:**
```java
Employee employee = new Employee();
employee.setName("Alice");
employee.setAge(30);
employee.setDepartment("HR");
System.out.println(employee);
// Output: Person(name=Alice, age=30)(department=HR)
```

---

#### **4. Including Only Specific Fields**

##### **Code:**
```java
import lombok.ToString;

@ToString(onlyExplicitlyIncluded = true)
public class Product {
    @ToString.Include
    private String id;

    @ToString.Include
    private String name;

    private double price; // Not included in toString()
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "Product(id=" + this.id + ", name=" + this.name + ")";
}
```

##### **Usage:**
```java
Product product = new Product();
product.setId("P001");
product.setName("Laptop");
product.setPrice(1000.0);
System.out.println(product); // Output: Product(id=P001, name=Laptop)
```

---

#### **5. Avoiding Getter Methods**

By default, Lombok uses getter methods (if available) in the `toString()` method. You can disable this behavior using `doNotUseGetters`.

##### **Code:**
```java
import lombok.ToString;

@ToString(doNotUseGetters = true)
public class Account {
    private String accountNumber;
    private double balance;

    public String getAccountNumber() {
        return "Hidden"; // Custom getter logic
    }
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "Account(accountNumber=" + this.accountNumber + ", balance=" + this.balance + ")";
}
```

##### **Usage:**
```java
Account account = new Account();
account.setAccountNumber("123456");
account.setBalance(5000.0);
System.out.println(account); // Output: Account(accountNumber=123456, balance=5000.0)
```

---

#### **6. Real-World Example: Login Class**

##### **Code:**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.ToString;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.AccessLevel;

@Getter
@NoArgsConstructor(staticName = "createEmpty")
@AllArgsConstructor(staticName = "create")
@ToString
public class Login {
    @Setter(value = AccessLevel.PROTECTED)
    private String username;

    @Setter(value = AccessLevel.PROTECTED)
    private String password;
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "Login(username=" + this.getUsername() + ", password=" + this.getPassword() + ")";
}
```

##### **Usage:**
```java
Login login = Login.create("admin", "password123");
System.out.println(login); // Output: Login(username=admin, password=password123)
```

---

### **Advanced Customization**

#### **1. Custom Field Names**

You can customize the field names in the `toString()` output using `@ToString.Include`.

##### **Code:**
```java
import lombok.ToString;

@ToString
public class Customer {
    @ToString.Include(name = "Customer ID")
    private String id;

    @ToString.Include(name = "Customer Name")
    private String name;
}
```

##### **Generated `toString()` Method:**
```java
public String toString() {
    return "Customer(Customer ID=" + this.id + ", Customer Name=" + this.name + ")";
}
```

---

#### **2. Ignoring Null Fields**

You can customize the `toString()` method to skip null fields by adding logic manually.

##### **Code:**
```java
import lombok.ToString;

@ToString
public class Order {
    private String orderId;
    private String customerName;
    private String product;
}
```

##### **Custom `toString()` Output:**
```java
Order(orderId=O001, customerName=John Doe, product=null)
```

If you want to ignore null fields, you need to write a manual `toString()` method.

---

### **Summary of `@ToString`**

| **Feature**                 | **Description**                                                                                  |
|-----------------------------|--------------------------------------------------------------------------------------------------|
| **Default Behavior**         | Includes all fields in the `toString()` method.                                                 |
| **Exclude Fields**           | Use `exclude` to skip specific fields.                                                          |
| **Include Superclass Fields**| Use `callSuper` to include fields from the superclass.                                          |
| **Explicit Inclusion**       | Use `onlyExplicitlyIncluded` with `@ToString.Include` to include specific fields.               |
| **Avoid Getters**            | Use `doNotUseGetters = true` to directly access fields instead of using getters.                |

