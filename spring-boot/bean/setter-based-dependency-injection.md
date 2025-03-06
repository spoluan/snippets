### **Setter-Based Dependency Injection (DI) in Spring**

Setter-Based Dependency Injection is one of the ways to inject dependencies into a Spring-managed bean. It involves using **setter methods** to inject required dependencies after the object is created. This method is particularly useful for **optional dependencies** or when the dependency needs to be changed dynamically.

---

### **Key Points of Setter-Based DI**
1. **Injection via Setter Methods**:
   - Dependencies are injected into the bean using public setter methods.
   - The setter method must be annotated with `@Autowired` to indicate that Spring should inject the dependency.

2. **Optional Dependencies**:
   - Setter-based DI is ideal for optional dependencies because the dependency can be null if not provided.

3. **Flexibility**:
   - Allows changing the dependency dynamically after the object has been created (though this is rarely needed in most cases).

4. **Combining with Constructor-Based DI**:
   - Setter-based DI can be combined with constructor-based DI, where mandatory dependencies are injected via the constructor, and optional dependencies are injected via setter methods.

---

### **Advantages of Setter-Based DI**
1. **Flexibility**:
   - Dependencies can be set or modified after the object is created.
   - Useful for optional or configurable dependencies.

2. **Readability**:
   - Makes it clear which dependencies are optional when compared to constructor-based DI.

3. **Ease of Testing**:
   - Dependencies can be easily replaced or modified during testing.

---

### **Disadvantages of Setter-Based DI**
1. **Immutability**:
   - Setter-based DI breaks immutability since dependencies can be modified after the object is created.

2. **Mandatory Dependencies**:
   - It does not enforce mandatory dependencies, which might lead to incomplete bean configurations if a required dependency is not set.

3. **Less Preferred in Modern Spring**:
   - Constructor-based DI is generally preferred for mandatory dependencies as it enforces better design principles.

---

### **Code Example: Setter-Based DI**

#### **1. Basic Setter-Based DI**
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CategoryService {
    private CategoryRepository categoryRepository;

    // Getter (optional, for external access)
    public CategoryRepository getCategoryRepository() {
        return categoryRepository;
    }

    // Setter method for dependency injection
    @Autowired
    public void setCategoryRepository(CategoryRepository categoryRepository) {
        this.categoryRepository = categoryRepository;
    }

    public void performOperation() {
        System.out.println("Using repository: " + categoryRepository);
    }
}
```

In this example:
- The `@Autowired` annotation on the setter method tells Spring to inject a `CategoryRepository` bean into the `CategoryService`.

---

#### **2. Optional Dependency Example**
If the dependency is optional, Spring can inject `null` if the bean is not available.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;

@Component
public class OptionalService {
    private SomeOptionalDependency optionalDependency;

    // Setter method for optional dependency
    @Autowired
    public void setOptionalDependency(@Nullable SomeOptionalDependency optionalDependency) {
        this.optionalDependency = optionalDependency;
    }

    public void execute() {
        if (optionalDependency != null) {
            optionalDependency.performTask();
        } else {
            System.out.println("Optional dependency is not available.");
        }
    }
}
```

Here, the `@Nullable` annotation allows the dependency to be null if no bean is available.

---

#### **3. Combining Setter-Based and Constructor-Based DI**
You can use constructor-based DI for mandatory dependencies and setter-based DI for optional ones.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CombinedService {
    private final MandatoryDependency mandatoryDependency;
    private OptionalDependency optionalDependency;

    // Constructor-based DI for mandatory dependency
    @Autowired
    public CombinedService(MandatoryDependency mandatoryDependency) {
        this.mandatoryDependency = mandatoryDependency;
    }

    // Setter-based DI for optional dependency
    @Autowired
    public void setOptionalDependency(OptionalDependency optionalDependency) {
        this.optionalDependency = optionalDependency;
    }

    public void execute() {
        mandatoryDependency.performTask();
        if (optionalDependency != null) {
            optionalDependency.performTask();
        }
    }
}
```

---

### **When to Use Setter-Based DI**
1. **Optional Dependencies**:
   - When a dependency is not always required for the bean to function.

2. **Dynamic Changes**:
   - When the dependency might need to be changed dynamically after the bean is created (though this is rare).

3. **Legacy Code**:
   - When working with legacy codebases where setter methods are already used extensively.

---

### **Comparison: Constructor-Based DI vs Setter-Based DI**

| **Feature**                  | **Constructor-Based DI**                                      | **Setter-Based DI**                                      |
|------------------------------|--------------------------------------------------------------|---------------------------------------------------------|
| **Usage**                    | Injects dependencies at the time of object creation.          | Injects dependencies after object creation via setters. |
| **Mandatory Dependencies**   | Enforces mandatory dependencies.                             | Cannot enforce mandatory dependencies.                  |
| **Optional Dependencies**    | Not suitable for optional dependencies.                      | Best suited for optional dependencies.                 |
| **Immutability**             | Promotes immutability by making dependencies `final`.         | Does not support immutability.                         |
| **Flexibility**              | Less flexible for dynamic changes.                           | More flexible for dynamic changes.                     |
| **Preferred Use Case**       | Mandatory dependencies.                                       | Optional or configurable dependencies.                 |

---

### **Best Practices**
1. **Use Constructor-Based DI for Mandatory Dependencies**:
   - Always prefer constructor-based DI for required dependencies to ensure the bean is fully initialized.

2. **Use Setter-Based DI for Optional Dependencies**:
   - Use setter-based DI only for dependencies that are optional or need to be changed dynamically.

3. **Avoid Setter-Based DI for Mandatory Dependencies**:
   - Setter-based DI does not guarantee that mandatory dependencies are provided, which can lead to runtime errors.

4. **Mark Dependencies as `@Nullable` if Optional**:
   - Clearly indicate optional dependencies using the `@Nullable` annotation for better code readability.

