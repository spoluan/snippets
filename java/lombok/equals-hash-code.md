### **`@EqualsAndHashCode` in Lombok**

The `@EqualsAndHashCode` annotation in Lombok is used to automatically generate the `equals()` and `hashCode()` methods for a class. These methods are crucial in Java for comparing objects and using them effectively in collections like `HashMap`, `HashSet`, etc.

---

### **Features of `@EqualsAndHashCode`**

1. **Automatic Method Generation:**
   - Generates `equals()` and `hashCode()` based on all non-static fields of the class by default.

2. **Customizable Behavior:**
   - You can exclude specific fields from the generated methods.
   - You can include fields from the superclass in the generated methods.
   - You can explicitly include only selected fields using annotations.

3. **Integration with Other Lombok Annotations:**
   - Works seamlessly with `@Getter`, `@Setter`, `@ToString`, and other Lombok annotations.

---

### **Why `equals()` and `hashCode()` Are Important**

1. **`equals()`:** Determines whether two objects are logically equivalent.
   - Example: Comparing two `Customer` objects to see if they have the same `id`.

2. **`hashCode()`:** Generates a hash code for an object.
   - Example: Used in hash-based collections like `HashMap` and `HashSet` to store and retrieve objects efficiently.

---

### **Key Parameters**

| **Parameter**               | **Description**                                                                                     |
|-----------------------------|-----------------------------------------------------------------------------------------------------|
| `exclude`                   | Excludes specific fields from the `equals()` and `hashCode()` methods.                              |
| `callSuper`                 | Includes the `equals()` and `hashCode()` logic of the superclass.                                   |
| `onlyExplicitlyIncluded`    | Includes only those fields explicitly annotated with `@EqualsAndHashCode.Include`.                  |
| `doNotUseGetters`           | If `true`, directly accesses fields instead of using getter methods in the generated methods.        |

---

### **Examples**

#### **1. Basic Usage**

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class Customer {
    private String id;
    private String name;
}
```

##### **Generated `equals()` and `hashCode()` Methods:**
```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;

    Customer customer = (Customer) o;

    if (id != null ? !id.equals(customer.id) : customer.id != null) return false;
    return name != null ? name.equals(customer.name) : customer.name == null;
}

@Override
public int hashCode() {
    int result = id != null ? id.hashCode() : 0;
    result = 31 * result + (name != null ? name.hashCode() : 0);
    return result;
}
```

##### **Usage:**
```java
Customer c1 = new Customer();
c1.setId("C001");
c1.setName("John Doe");

Customer c2 = new Customer();
c2.setId("C001");
c2.setName("John Doe");

System.out.println(c1.equals(c2)); // Output: true
```

---

#### **2. Excluding Specific Fields**

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode(exclude = "name")
public class Customer {
    private String id;
    private String name;
}
```

##### **Generated Methods:**
- The `name` field is excluded from `equals()` and `hashCode()`.

##### **Usage:**
```java
Customer c1 = new Customer();
c1.setId("C001");
c1.setName("John Doe");

Customer c2 = new Customer();
c2.setId("C001");
c2.setName("Jane Doe");

System.out.println(c1.equals(c2)); // Output: true (because `name` is excluded)
```

---

#### **3. Including Superclass Fields**

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode(callSuper = true)
public class Employee extends Person {
    private String department;
}

class Person {
    private String name;
    private int age;
}
```

##### **Generated Methods:**
- Includes `equals()` and `hashCode()` logic from the `Person` superclass.

##### **Usage:**
```java
Employee e1 = new Employee();
e1.setName("Alice");
e1.setAge(30);
e1.setDepartment("HR");

Employee e2 = new Employee();
e2.setName("Alice");
e2.setAge(30);
e2.setDepartment("HR");

System.out.println(e1.equals(e2)); // Output: true
```

---

#### **4. Including Only Specific Fields**

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Product {
    @EqualsAndHashCode.Include
    private String id;

    @EqualsAndHashCode.Include
    private String name;

    private double price; // Not included in equals() and hashCode()
}
```

##### **Generated Methods:**
- Only the `id` and `name` fields are included in `equals()` and `hashCode()`.

##### **Usage:**
```java
Product p1 = new Product();
p1.setId("P001");
p1.setName("Laptop");
p1.setPrice(1000.0);

Product p2 = new Product();
p2.setId("P001");
p2.setName("Laptop");
p2.setPrice(2000.0);

System.out.println(p1.equals(p2)); // Output: true (price is ignored)
```

---

#### **5. Avoiding Getter Methods**

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode(doNotUseGetters = true)
public class Account {
    private String accountNumber;
    private double balance;

    public String getAccountNumber() {
        return "Hidden"; // Custom getter logic
    }
}
```

##### **Generated Methods:**
- Directly accesses fields instead of using getters.

##### **Usage:**
```java
Account a1 = new Account();
a1.setAccountNumber("123456");
a1.setBalance(5000.0);

Account a2 = new Account();
a2.setAccountNumber("123456");
a2.setBalance(5000.0);

System.out.println(a1.equals(a2)); // Output: true
```

---

#### **6. Real-World Example: Customer Class**

##### **Code:**
```java
import lombok.Getter;
import lombok.Setter;
import lombok.EqualsAndHashCode;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;

@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class Customer {
    private String id;
    private String name;
}
```

##### **Generated Methods:**
- Automatically generates `equals()` and `hashCode()` based on all fields (`id` and `name`).

##### **Usage:**
```java
Customer customer1 = new Customer("C001", "John Doe");
Customer customer2 = new Customer("C001", "John Doe");

System.out.println(customer1.equals(customer2)); // Output: true
```

---

### **Advanced Customization**

#### **1. Custom Field Names**

You can customize the behavior of `@EqualsAndHashCode` for specific fields using `@EqualsAndHashCode.Include` and `@EqualsAndHashCode.Exclude`.

##### **Code:**
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class Order {
    @EqualsAndHashCode.Include
    private String orderId;

    @EqualsAndHashCode.Exclude
    private String product;
}
```

##### **Generated Methods:**
- Includes only `orderId` in `equals()` and `hashCode()`.

---

### **Summary of `@EqualsAndHashCode`**

| **Feature**                 | **Description**                                                                                     |
|-----------------------------|-----------------------------------------------------------------------------------------------------|
| **Default Behavior**         | Includes all non-static fields in `equals()` and `hashCode()`.                                      |
| **Exclude Fields**           | Use `exclude` to skip specific fields.                                                             |
| **Include Superclass Fields**| Use `callSuper` to include fields from the superclass.                                             |
| **Explicit Inclusion**       | Use `onlyExplicitlyIncluded` with `@EqualsAndHashCode.Include` to include specific fields.          |
| **Avoid Getters**            | Use `doNotUseGetters = true` to directly access fields instead of using getters.                   |
