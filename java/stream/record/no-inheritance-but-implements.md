### **Inheritance and Implementation in Java Records**

Java Records, introduced in **Java 14 (preview)** and finalized in **Java 16**, are immutable data classes that simplify the creation of objects for storing data. However, their behavior regarding inheritance and implementation differs from regular classes. Below is a detailed explanation of inheritance and interface implementation in Java Records.

---

### **1. Class Inheritance in Java Records**

#### **Key Rules**
1. **Records Cannot Extend Classes or Other Records**:
   - Records are implicitly subclasses of `java.lang.Record`.
   - They are **final**, meaning they cannot be extended by other classes or records.

2. **Similar to Enums**:
   - Just as enums automatically extend `java.lang.Enum`, records automatically extend `java.lang.Record`.

3. **Why Records Are Final**:
   - Records are designed to represent **immutable data**. Allowing inheritance would break this immutability principle.

#### **Example: Class Inheritance**

```java
public record Customer(String id, String name, String email) {}

// Trying to extend a record will result in a compilation error:
public class VIPCustomer extends Customer { // Error: Cannot inherit from a record
}
```

**Error Output**:
```
Cannot inherit from final 'Customer'
```

---

### **2. Interface Implementation in Java Records**

#### **Key Rules**
1. **Records Can Implement Interfaces**:
   - Records can implement one or more interfaces, just like regular classes.
   - This allows you to add behavior to records.

2. **Behavior Implementation**:
   - You can define methods required by the interface within the record.

3. **Why Interface Implementation Is Allowed**:
   - Implementing interfaces does not compromise the immutability of records, as interfaces define behavior rather than state.

#### **Example: Interface Implementation**

##### **Defining an Interface**

```java
public interface SayHello {
    String sayHello(String name);
}
```

##### **Implementing the Interface in a Record**

```java
public record Customer(String id, String name, String email, String phone) implements SayHello {
    @Override
    public String sayHello(String name) {
        return "Hello " + name + ", my name is " + this.name();
    }
}
```

##### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("1", "Sevendi", "Sevendi@localhost", "12345");
        System.out.println(customer.sayHello("Budi"));
    }
}
```

**Output**:
```
Hello Budi, my name is Sevendi
```

---

### **3. Records and Abstract Methods**

Records can implement interfaces that contain abstract methods, but they themselves cannot contain abstract methods because records are implicitly final.

#### **Example: Implementing Abstract Methods**

```java
public interface Greeting {
    String greet(String name);
}

public record Person(String id, String name) implements Greeting {
    @Override
    public String greet(String name) {
        return "Hi " + name + ", I'm " + this.name();
    }
}
```

---

### **4. Records and Multiple Interfaces**

Records can implement multiple interfaces, just like regular classes. This enables them to have multiple behaviors.

#### **Example: Implementing Multiple Interfaces**

##### **Defining Interfaces**

```java
public interface SayHello {
    String sayHello(String name);
}

public interface ContactInfo {
    String getEmail();
    String getPhone();
}
```

##### **Record Implementing Multiple Interfaces**

```java
public record Customer(String id, String name, String email, String phone) implements SayHello, ContactInfo {
    @Override
    public String sayHello(String name) {
        return "Hello " + name + ", my name is " + this.name();
    }

    @Override
    public String getEmail() {
        return email;
    }

    @Override
    public String getPhone() {
        return phone;
    }
}
```

##### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Customer customer = new Customer("1", "Sevendi", "Sevendi@localhost", "12345");
        System.out.println(customer.sayHello("Budi"));
        System.out.println("Email: " + customer.getEmail());
        System.out.println("Phone: " + customer.getPhone());
    }
}
```

**Output**:
```
Hello Budi, my name is Sevendi
Email: Sevendi@localhost
Phone: 12345
```

---

### **5. Records and Functional Interfaces**

Records can also implement functional interfaces, allowing them to be used in lambda expressions or method references.

#### **Example: Implementing a Functional Interface**

##### **Defining a Functional Interface**

```java
@FunctionalInterface
public interface Printer {
    String print();
}
```

##### **Record Implementing the Functional Interface**

```java
public record Document(String title, String content) implements Printer {
    @Override
    public String print() {
        return "Title: " + title + "\nContent: " + content;
    }
}
```

##### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Document doc = new Document("Java Records", "Immutable data classes in Java.");
        System.out.println(doc.print());
    }
}
```

**Output**:
```
Title: Java Records
Content: Immutable data classes in Java.
```

---

### **6. Records and Marker Interfaces**

Records can implement marker interfaces (interfaces with no methods) to indicate a specific capability or behavior.

#### **Example: Marker Interface**

##### **Defining a Marker Interface**

```java
public interface Serializable {}
```

##### **Record Implementing the Marker Interface**

```java
public record Product(String id, String name, double price) implements Serializable {}
```

---

### **7. Records and Interface Default Methods**

Records can use default methods provided by interfaces without overriding them, or they can override them if needed.

#### **Example: Default Methods**

##### **Defining an Interface with Default Methods**

```java
public interface Describable {
    default String describe() {
        return "This is a describable object.";
    }
}
```

##### **Record Using Default Methods**

```java
public record Item(String id, String name) implements Describable {}
```

##### **Usage**

```java
public class Main {
    public static void main(String[] args) {
        Item item = new Item("101", "Laptop");
        System.out.println(item.describe());
    }
}
```

**Output**:
```
This is a describable object.
```

---

### **8. Summary**

| Feature                  | Records Behavior                                                                 |
|--------------------------|----------------------------------------------------------------------------------|
| **Class Inheritance**    | Records cannot extend classes or other records; they are implicitly final.       |
| **Interface Implementation** | Records can implement one or more interfaces, providing behavior to the record. |
| **Abstract Methods**     | Records can implement abstract methods from interfaces.                         |
| **Multiple Interfaces**  | Records can implement multiple interfaces to combine behaviors.                  |
| **Functional Interfaces**| Records can implement functional interfaces for use in lambdas or method references. |
| **Marker Interfaces**    | Records can implement marker interfaces to indicate specific capabilities.       |
| **Default Methods**      | Records can use or override default methods from interfaces.                     |

