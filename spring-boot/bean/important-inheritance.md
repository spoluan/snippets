### **Inheritance in Spring Boot**

Inheritance in Spring Boot refers to the ability to work with beans using their **parent class** or **interface type** rather than their specific implementation. This is a powerful feature that allows developers to write more flexible and reusable code by leveraging polymorphism.

---

### **Key Concepts of Inheritance in Spring Boot**

1. **Accessing Beans by Parent Type**:
   - When a bean is registered in the Spring container, you can access it using either:
     - Its **specific class type** (e.g., `MerchantServiceImpl`).
     - Its **parent class** or **interface type** (e.g., `MerchantService`).

2. **Polymorphism**:
   - If a class implements an interface or extends a parent class, you can reference the bean using the parent type.
   - This enables writing code that depends on abstractions (interfaces) rather than concrete implementations.

3. **Handling Multiple Implementations**:
   - If multiple beans implement the same interface or extend the same parent class, you need to be careful to avoid **ambiguity errors**.
   - Spring requires you to specify which bean to use when multiple beans of the same type exist.

---

### **Example: Single Implementation**

#### **Step 1: Define an Interface**
```java
public interface MerchantService {
}
```

#### **Step 2: Implement the Interface**
```java
@Component
public class MerchantServiceImpl implements MerchantService {
}
```

#### **Step 3: Access the Bean**
You can access the bean using either the interface type (`MerchantService`) or the implementation type (`MerchantServiceImpl`).

```java
MerchantService merchantService = applicationContext.getBean(MerchantService.class);
MerchantServiceImpl merchantServiceImpl = applicationContext.getBean(MerchantServiceImpl.class);

Assertions.assertSame(merchantService, merchantServiceImpl);
```

- **Explanation**:
  - Both `merchantService` and `merchantServiceImpl` refer to the same bean instance in the Spring container.
  - Spring resolves the bean automatically based on the type.

---

### **Example: Multiple Implementations**

#### **Step 1: Define an Interface**
```java
public interface MerchantService {
}
```

#### **Step 2: Create Multiple Implementations**
```java
@Component
public class MerchantServiceImplA implements MerchantService {
}

@Component
public class MerchantServiceImplB implements MerchantService {
}
```

#### **Step 3: Handle Ambiguity**
When multiple beans implement the same interface, Spring cannot decide which one to inject automatically. You must resolve the ambiguity using one of the following approaches:

1. **Use Bean Name**:
   Specify the bean name explicitly when retrieving it.

   ```java
   MerchantService merchantServiceA = applicationContext.getBean("merchantServiceImplA", MerchantService.class);
   MerchantService merchantServiceB = applicationContext.getBean("merchantServiceImplB", MerchantService.class);
   ```

2. **Use `@Qualifier`**:
   Annotate the injection point with `@Qualifier` to specify which bean to use.

   ```java
   @Autowired
   @Qualifier("merchantServiceImplA")
   private MerchantService merchantService;
   ```

3. **Use `@Primary`**:
   Mark one of the beans as the primary bean to resolve conflicts.

   ```java
   @Component
   @Primary
   public class MerchantServiceImplA implements MerchantService {
   }
   ```

   With `@Primary`, Spring will use `MerchantServiceImplA` by default unless another bean is explicitly specified.

4. **Use `@Order`** (For Collections):
   If injecting a list of beans, use `@Order` to control the order of injection.

---

### **Testing Inheritance in Spring Boot**

#### **Test Class Example**
```java
public class InheritanceTest {

    private ConfigurableApplicationContext applicationContext;

    @BeforeEach
    void setUp() {
        applicationContext = new AnnotationConfigApplicationContext(InheritanceConfiguration.class);
        applicationContext.registerShutdownHook();
    }

    @Test
    void testInheritance() {
        MerchantService merchantService = applicationContext.getBean(MerchantService.class);
        MerchantServiceImpl merchantServiceImpl = applicationContext.getBean(MerchantServiceImpl.class);

        Assertions.assertSame(merchantService, merchantServiceImpl);
    }
}
```

#### **Explanation**:
- The test verifies that the same bean instance is returned whether accessed using the interface type (`MerchantService`) or the implementation type (`MerchantServiceImpl`).

---

### **Best Practices for Inheritance in Spring**

1. **Program to Interfaces**:
   - Always reference beans using their interfaces or parent classes rather than their concrete implementations.
   - This promotes flexibility and makes it easier to switch implementations without changing the dependent code.

2. **Avoid Ambiguity**:
   - Use `@Qualifier` or `@Primary` to resolve conflicts when multiple beans implement the same interface.

3. **Use `@Order` for Collections**:
   - When injecting a list of beans, use `@Order` to control the order in which they are injected.

4. **Be Explicit**:
   - When dealing with multiple implementations, always be explicit about which bean to use to avoid runtime errors.

---

### **Common Errors and How to Avoid Them**

1. **NoUniqueBeanDefinitionException**:
   - Occurs when multiple beans of the same type exist, and Spring cannot determine which one to inject.
   - **Solution**: Use `@Qualifier` or `@Primary`.

2. **UnsatisfiedDependencyException**:
   - Occurs when Spring cannot find a matching bean for the required type.
   - **Solution**: Ensure a bean of the required type exists in the Spring context.

3. **Circular Dependency**:
   - Occurs when two or more beans depend on each other directly or indirectly.
   - **Solution**: Use `@Lazy` or refactor the code to break the circular dependency.

 
