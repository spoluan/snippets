### **Handling Multiple Constructors in Spring Dependency Injection**

Spring supports **only one constructor** for Dependency Injection (DI) by default. However, if a class has **multiple constructors**, you need to explicitly specify which constructor Spring should use for injection. This can be achieved using the `@Autowired` annotation.

---

### **Key Points**
1. **Default Behavior**:
   - If a class has only **one constructor**, Spring automatically uses it for dependency injection.
   - If there are **multiple constructors**, Spring cannot decide which one to use unless explicitly instructed.

2. **Specifying the Constructor**:
   - Use the `@Autowired` annotation on the constructor you want Spring to use for dependency injection.

3. **Avoid Ambiguity**:
   - Always mark the desired constructor with `@Autowired` to avoid runtime errors or ambiguity.

---

### **Example: Multiple Constructors**

#### **1. Without `@Autowired`**
If a class has multiple constructors and none of them is annotated with `@Autowired`, Spring will throw an **error** during runtime because it cannot decide which constructor to use.

```java
import org.springframework.stereotype.Component;

@Component
public class MyService {
    private final DependencyA dependencyA;
    private final DependencyB dependencyB;

    // Constructor 1
    public MyService(DependencyA dependencyA) {
        this.dependencyA = dependencyA;
        this.dependencyB = null; // Not injected
    }

    // Constructor 2
    public MyService(DependencyA dependencyA, DependencyB dependencyB) {
        this.dependencyA = dependencyA;
        this.dependencyB = dependencyB;
    }
}
```

- **Problem**: Spring doesn’t know whether to use **Constructor 1** or **Constructor 2**.

---

#### **2. Resolving with `@Autowired`**
To resolve this ambiguity, annotate the desired constructor with `@Autowired`.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class MyService {
    private final DependencyA dependencyA;
    private final DependencyB dependencyB;

    // Constructor 1
    public MyService(DependencyA dependencyA) {
        this.dependencyA = dependencyA;
        this.dependencyB = null; // Not injected
    }

    // Constructor 2 (Marked with @Autowired)
    @Autowired
    public MyService(DependencyA dependencyA, DependencyB dependencyB) {
        this.dependencyA = dependencyA;
        this.dependencyB = dependencyB;
    }
}
```

In this case:
- Spring will use **Constructor 2** to inject both `DependencyA` and `DependencyB`.

---

### **Best Practices**
1. **Use a Single Constructor**:
   - Whenever possible, design your classes with a single constructor to avoid ambiguity and simplify the code.

2. **Explicitly Use `@Autowired`**:
   - When multiple constructors are necessary, always annotate the one you want Spring to use with `@Autowired`.

3. **Avoid Overloading Constructors**:
   - Overloading constructors with similar parameters can lead to confusion and potential runtime errors.

4. **Optional Dependencies**:
   - For optional dependencies, use setter-based injection or `@Nullable` in the constructor.

---

### **Advanced Example: Optional Dependencies**

If you want to inject a dependency only when it is available, you can mark it as `@Nullable` in the constructor.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;

@Component
public class MyService {
    private final DependencyA dependencyA;
    private final DependencyB dependencyB;

    @Autowired
    public MyService(DependencyA dependencyA, @Nullable DependencyB dependencyB) {
        this.dependencyA = dependencyA;
        this.dependencyB = dependencyB; // Can be null if not available
    }
}
```

