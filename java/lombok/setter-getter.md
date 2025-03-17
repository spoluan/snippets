### **Setters and Getters in Lombok**

In Lombok, **`@Getter`** and **`@Setter`** annotations are used to automatically generate getter and setter methods for class fields. These annotations save developers from writing repetitive boilerplate code, making the code cleaner and more concise.

---

### **1. How `@Getter` and `@Setter` Work**

#### **Key Points**:
1. **Field-Level Usage**:  
   If `@Getter` or `@Setter` is placed on a specific field, Lombok generates only the getter or setter for that field.
   
2. **Class-Level Usage**:  
   If `@Getter` or `@Setter` is placed on a class, Lombok generates getter or setter methods for all non-static fields in the class.

3. **Customization**:  
   - You can control **access levels** (e.g., `public`, `private`) for generated methods.
   - You can exclude certain fields from having getter or setter methods.

---

### **2. Basic Example**

#### **Code**:
```java
import lombok.Getter;
import lombok.Setter;

public class Customer {
    @Getter @Setter
    private String id;

    @Getter @Setter
    private String name;
}
```

#### **Generated Code**:
```java
public class Customer {
    private String id;
    private String name;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### **3. Class-Level Example**

#### **Code**:
```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Customer {
    private String id;
    private String name;
}
```

#### **Generated Code**:
```java
public class Customer {
    private String id;
    private String name;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### **4. Customizing Access Levels**

You can specify access levels for the generated getter and setter methods using the **`AccessLevel`** enum. Available options include:
- `PUBLIC` (default)
- `PROTECTED`
- `PACKAGE` (default/package-private)
- `PRIVATE`
- `NONE` (no method is generated)

#### **Example**:
```java
import lombok.Getter;
import lombok.Setter;
import lombok.AccessLevel;

public class Customer {
    @Getter(AccessLevel.PUBLIC)  // Default
    private String id;

    @Setter(AccessLevel.PROTECTED)
    private String name;
}
```

#### **Generated Code**:
```java
public class Customer {
    private String id;
    private String name;

    public String getId() {
        return id;
    }

    protected void setName(String name) {
        this.name = name;
    }
}
```

---

### **5. Excluding Fields**

If you want to exclude certain fields from having a getter or setter, simply don't annotate them.

#### **Example**:
```java
import lombok.Getter;

public class Customer {
    @Getter
    private String id;

    private String name; // No getter or setter generated
}
```

---

### **6. Read-Only and Write-Only Fields**

- **Read-Only Field**: Use only `@Getter`.
- **Write-Only Field**: Use only `@Setter`.

#### **Example**:
```java
import lombok.Getter;
import lombok.Setter;

public class Customer {
    @Getter
    private String id; // Read-only

    @Setter
    private String name; // Write-only
}
```

#### **Generated Code**:
```java
public class Customer {
    private String id;
    private String name;

    public String getId() {
        return id;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### **7. Static Fields**

Lombok does **not** generate getter or setter methods for `static` fields, even if `@Getter` or `@Setter` is applied at the class level.

#### **Example**:
```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Customer {
    private static String companyName; // No getter or setter generated

    private String id;
    private String name;
}
```

---

### **8. Lombok with Final Fields**

- Lombok will not generate a setter for `final` fields because they cannot be reassigned.
- A getter will still be generated.

#### **Example**:
```java
import lombok.Getter;

public class Customer {
    @Getter
    private final String id = "12345"; // Read-only, no setter
}
```

#### **Generated Code**:
```java
public class Customer {
    private final String id = "12345";

    public String getId() {
        return id;
    }
}
```

---

### **9. Combining with Other Lombok Annotations**

Lombok's `@Getter` and `@Setter` can be combined with other annotations like `@Data`, `@Builder`, and `@ToString`.

#### **Using `@Data`**:
`@Data` automatically includes `@Getter` and `@Setter` for all fields, along with `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`.

```java
import lombok.Data;

@Data
public class Customer {
    private String id;
    private String name;
}
```

#### **Using `@Builder`**:
You can use `@Builder` for better object creation while still using `@Getter` and `@Setter`.

```java
import lombok.Builder;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
@Builder
public class Customer {
    private String id;
    private String name;
}
```

**Usage**:
```java
Customer customer = Customer.builder()
                            .id("123")
                            .name("John Doe")
                            .build();
```

---

### **10. Real-World Example**

#### **Code**:
```java
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Product {
    private String productId;
    private String productName;
    private double price;
    private int stock;
}
```

#### **Usage**:
```java
public class Main {
    public static void main(String[] args) {
        Product product = new Product();
        product.setProductId("P001");
        product.setProductName("Laptop");
        product.setPrice(1500.00);
        product.setStock(10);

        System.out.println("Product ID: " + product.getProductId());
        System.out.println("Product Name: " + product.getProductName());
        System.out.println("Price: " + product.getPrice());
        System.out.println("Stock: " + product.getStock());
    }
}
```

---

### **11. Summary**

- `@Getter` and `@Setter` are powerful Lombok annotations that simplify the creation of getter and setter methods.
- They can be used at the field or class level.
- Access levels and exclusions can be customized.
- They work seamlessly with other Lombok annotations like `@Data`, `@Builder`, and `@ToString`.
- Lombok does not generate setters for `final` fields or static fields.

