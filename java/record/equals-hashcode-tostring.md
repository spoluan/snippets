### **Equals, HashCode, and ToString in Java Records**

Java Records, introduced in **Java 14 (preview)** and finalized in **Java 16**, automatically generate implementations for the `equals()`, `hashCode()`, and `toString()` methods. This is one of the key features of records, making them concise and ideal for representing immutable data.

---

### **1. Equals in Java Records**

The `equals()` method in Java Records is automatically generated to compare all the fields declared in the record's header. Two records are considered **equal** if:
- They are of the same type.
- All their fields have the same values.

#### **Generated Equals Behavior**
- Compares each field of the record using the `equals()` method of the field's type.
- Ensures structural equality rather than reference equality.

#### **Example: Equals**

```java
public record Customer(String id, String name, String email) {}

public class Main {
    public static void main(String[] args) {
        Customer customer1 = new Customer("1", "Sevendi", "Sevendi@localhost");
        Customer customer2 = new Customer("1", "Sevendi", "Sevendi@localhost");
        Customer customer3 = new Customer("2", "Budi", "budi@localhost");

        System.out.println(customer1.equals(customer2)); // true
        System.out.println(customer1.equals(customer3)); // false
    }
}
```

**Output**:
```
true
false
```

---

### **2. HashCode in Java Records**

The `hashCode()` method is also automatically generated in records. It computes a hash code based on all the fields in the record's header. This ensures that two records that are **equal** (as per `equals()`) will have the same hash code.

#### **Generated HashCode Behavior**
- Combines the hash codes of all fields in the record.
- Uses the `Objects.hash()` method internally.

#### **Example: HashCode**

```java
public record Product(String id, String name, double price) {}

public class Main {
    public static void main(String[] args) {
        Product product1 = new Product("101", "Laptop", 1500.00);
        Product product2 = new Product("101", "Laptop", 1500.00);

        System.out.println(product1.hashCode() == product2.hashCode()); // true
    }
}
```

**Output**:
```
true
```

---

### **3. ToString in Java Records**

The `toString()` method in records is automatically generated to provide a string representation of the record. It includes:
- The record's class name.
- The names and values of all fields in the record.

#### **Generated ToString Behavior**
- The output format is: `ClassName[field1=value1, field2=value2, ...]`.

#### **Example: ToString**

```java
public record Employee(String name, int age) {}

public class Main {
    public static void main(String[] args) {
        Employee employee = new Employee("Alice", 30);
        System.out.println(employee.toString());
    }
}
```

**Output**:
```
Employee[name=Alice, age=30]
```

---

### **4. Overriding Equals, HashCode, and ToString**

Although these methods are automatically generated, you can override them to provide custom behavior if needed.

#### **Overriding Equals**

You can override `equals()` to customize the comparison logic.

```java
public record Book(String title, String author) {
    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Book other)) return false;
        return title.equalsIgnoreCase(other.title) && author.equalsIgnoreCase(other.author);
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Book book1 = new Book("Java Programming", "John Doe");
        Book book2 = new Book("java programming", "john doe");
        System.out.println(book1.equals(book2)); // true
    }
}
```

---

#### **Overriding HashCode**

You can override `hashCode()` to provide a custom hash code calculation.

```java
public record User(String username, String email) {
    @Override
    public int hashCode() {
        return username.hashCode() * 31 + email.hashCode();
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        User user1 = new User("john_doe", "john.doe@example.com");
        User user2 = new User("john_doe", "john.doe@example.com");
        System.out.println(user1.hashCode() == user2.hashCode()); // true
    }
}
```

---

#### **Overriding ToString**

You can override `toString()` to customize the string representation of the record.

```java
public record Order(String id, String product, int quantity) {
    @Override
    public String toString() {
        return "Order ID: " + id + ", Product: " + product + ", Quantity: " + quantity;
    }
}
```

**Usage**:

```java
public class Main {
    public static void main(String[] args) {
        Order order = new Order("123", "Laptop", 2);
        System.out.println(order.toString());
    }
}
```

**Output**:
```
Order ID: 123, Product: Laptop, Quantity: 2
```

---

### **5. Testing Equals, HashCode, and ToString**

You can write unit tests to verify the behavior of these methods.

#### **JUnit Test Example**

```java
import static org.junit.jupiter.api.Assertions.*;

public class RecordTest {
    @Test
    void testEqualsAndHashCode() {
        var customer1 = new Customer("1", "Sevendi", "Sevendi@localhost");
        var customer2 = new Customer("1", "Sevendi", "Sevendi@localhost");

        // Test equals
        assertTrue(customer1.equals(customer2));

        // Test hashCode
        assertEquals(customer1.hashCode(), customer2.hashCode());
    }

    @Test
    void testToString() {
        var customer = new Customer("1", "Sevendi", "Sevendi@localhost");
        assertEquals("Customer[id=1, name=Sevendi, email=Sevendi@localhost]", customer.toString());
    }
}
```

---

### **6. Key Points About Equals, HashCode, and ToString in Records**

1. **Automatic Generation**:
   - Java Records automatically generate these methods based on all the fields in the record's header.

2. **Structural Equality**:
   - `equals()` checks the equality of all fields, ensuring structural equality.

3. **Consistent Hashing**:
   - `hashCode()` is consistent with `equals()`, ensuring that two equal records have the same hash code.

4. **Readable Representation**:
   - `toString()` provides a human-readable representation of the record.

5. **Custom Behavior**:
   - You can override these methods if you need custom behavior.

---

### **7. Summary**

| Method      | Automatically Generated Behavior                                                                                      | Can Be Overridden |
|-------------|----------------------------------------------------------------------------------------------------------------------|-------------------|
| **equals()** | Compares all fields for equality.                                                                                    | Yes               |
| **hashCode()** | Generates a hash code based on all fields.                                                                          | Yes               |
| **toString()** | Provides a string representation of the record in the format `ClassName[field1=value1, field2=value2, ...]`.        | Yes               |

