### **`@Data` in Lombok**

The `@Data` annotation in Lombok is a convenient shortcut that bundles several commonly used annotations into one. It is typically used for classes that represent data models, entities, or DTOs (Data Transfer Objects). By using `@Data`, you can automatically generate boilerplate code for getters, setters, `toString()`, `equals()`, `hashCode()`, and a required-args constructor.

---

### **What Does `@Data` Do?**

When you annotate a class with `@Data`, Lombok automatically generates the following:

1. **`@Getter` and `@Setter`:** Generates getter and setter methods for all fields.
2. **`@ToString`:** Generates a `toString()` method that includes all fields.
3. **`@EqualsAndHashCode`:** Generates `equals()` and `hashCode()` methods.
4. **`@RequiredArgsConstructor`:** Generates a constructor for all `final` fields and fields annotated with `@NonNull`.

This makes `@Data` a highly efficient way to eliminate boilerplate code for simple data classes.

---

### **Key Features of `@Data`**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **All-in-One Annotation**     | Combines `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`. |
| **Final Fields Constructor**  | Automatically includes `final` fields in the constructor.                                        |
| **Customizable**              | Can be combined with other Lombok annotations to override specific behaviors.                    |
| **Excludes Static Fields**    | Automatically excludes static fields from generated methods.                                     |

---

### **Examples**

#### **1. Basic Usage**

##### **Code:**
```java
import lombok.Data;

@Data
public class Product {
    private final String id;
    private String name;
    private Long price;
}
```

##### **Generated Code:**
- **Getters and Setters:**
  ```java
  public String getId() { return id; }
  public String getName() { return name; }
  public void setName(String name) { this.name = name; }
  public Long getPrice() { return price; }
  public void setPrice(Long price) { this.price = price; }
  ```

- **`toString()` Method:**
  ```java
  public String toString() {
      return "Product(id=" + id + ", name=" + name + ", price=" + price + ")";
  }
  ```

- **`equals()` and `hashCode()` Methods:**
  ```java
  public boolean equals(Object o) { ... }
  public int hashCode() { ... }
  ```

- **Constructor:**
  ```java
  public Product(String id) {
      this.id = id;
  }
  ```

##### **Usage:**
```java
Product product = new Product("P001");
product.setName("Laptop");
product.setPrice(1500L);

System.out.println(product); // Output: Product(id=P001, name=Laptop, price=1500)
```

---

#### **2. Customizing `@Data` with Additional Annotations**

You can combine `@Data` with other Lombok annotations to customize its behavior.

##### **Code:**
```java
import lombok.Data;
import lombok.ToString;
import lombok.AllArgsConstructor;

@Data
@ToString(exclude = "price")
@AllArgsConstructor
public class Product {
    private final String id;
    private String name;
    private Long price;
}
```

##### **Explanation:**
- `@ToString(exclude = "price")`: Excludes the `price` field from the `toString()` method.
- `@AllArgsConstructor`: Adds a constructor for all fields.

##### **Usage:**
```java
Product product = new Product("P001", "Laptop", 1500L);

System.out.println(product); // Output: Product(id=P001, name=Laptop)
```

---

#### **3. Using `@Data` with `@NonNull`**

You can use `@NonNull` to ensure that specific fields cannot be null.

##### **Code:**
```java
import lombok.Data;
import lombok.NonNull;

@Data
public class Customer {
    private final String id;
    @NonNull
    private String name;
}
```

##### **Generated Constructor:**
```java
public Customer(String id, @NonNull String name) {
    if (name == null) {
        throw new NullPointerException("name is marked non-null but is null");
    }
    this.id = id;
    this.name = name;
}
```

##### **Usage:**
```java
Customer customer = new Customer("C001", "John Doe"); // Works
Customer customer2 = new Customer("C002", null); // Throws NullPointerException
```

---

#### **4. Combining with `@Builder`**

You can use `@Builder` with `@Data` to create builder-style object creation.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Order {
    private final String orderId;
    private String product;
    private int quantity;
}
```

##### **Usage:**
```java
Order order = Order.builder()
                   .orderId("O001")
                   .product("Laptop")
                   .quantity(2)
                   .build();

System.out.println(order); // Output: Order(orderId=O001, product=Laptop, quantity=2)
```

---

#### **5. Excluding Fields from `equals()` and `hashCode()`**

You can exclude specific fields from being included in `equals()` and `hashCode()`.

##### **Code:**
```java
import lombok.Data;
import lombok.EqualsAndHashCode;

@Data
@EqualsAndHashCode(exclude = "price")
public class Product {
    private final String id;
    private String name;
    private Long price;
}
```

##### **Usage:**
```java
Product p1 = new Product("P001", "Laptop", 1500L);
Product p2 = new Product("P001", "Laptop", 2000L);

System.out.println(p1.equals(p2)); // Output: true (price is excluded)
```

---

#### **6. Immutable Objects**

By combining `@Data` with `final` fields, you can create immutable objects.

##### **Code:**
```java
import lombok.Data;

@Data
public class ImmutableProduct {
    private final String id;
    private final String name;
    private final Long price;
}
```

##### **Usage:**
```java
ImmutableProduct product = new ImmutableProduct("P001", "Laptop", 1500L);

// product.setName("Tablet"); // Compilation error: No setter for final field
```

---

#### **7. Overriding Generated Methods**

You can override any of the generated methods if needed.

##### **Code:**
```java
import lombok.Data;

@Data
public class Product {
    private final String id;
    private String name;
    private Long price;

    @Override
    public String toString() {
        return "Product: " + name + " (ID: " + id + ")";
    }
}
```

##### **Usage:**
```java
Product product = new Product("P001");
product.setName("Laptop");

System.out.println(product); // Output: Product: Laptop (ID: P001)
```

---

### **When to Use `@Data`**

- Use `@Data` for simple data classes or entities where all fields need getters, setters, `toString()`, `equals()`, and `hashCode()`.
- Avoid using `@Data` for complex classes where you need fine-grained control over specific methods (e.g., `equals()` or `hashCode()`).

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **All-in-One Annotation**     | Combines `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`. |
| **Customizable**              | Can be combined with other annotations like `@ToString`, `@Builder`, and `@AllArgsConstructor`.  |
| **Immutable Objects**         | Use `final` fields with `@Data` to create immutable objects.                                     |
| **Excludes Static Fields**    | Automatically excludes static fields from generated methods.                                     |
