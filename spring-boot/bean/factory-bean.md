### **FactoryBean in Spring**

A `FactoryBean` in Spring is a special type of bean that allows you to create and configure complex objects, particularly when you cannot directly annotate a class or control its instantiation (e.g., third-party classes). It provides an abstraction for creating objects and is useful in scenarios where direct instantiation is not feasible.

---

### **Why Use FactoryBean?**
1. **Third-Party Classes**:
   - When a class is not under your control (e.g., a third-party library), you cannot annotate it with Spring's annotations like `@Component` or `@Service`.
   - Example: A library-provided client class like `PaymentGatewayClient`.

2. **Complex Initialization Logic**:
   - When creating an object requires custom logic, such as setting multiple properties or dependencies.

3. **Encapsulation**:
   - Encapsulates the creation logic in a separate class, keeping your application code clean.

4. **Lazy Initialization**:
   - The bean is created only when it is accessed, which can improve performance.

---

### **Key Features of FactoryBean**
1. **Custom Object Creation**:
   - It allows you to define how an object is created and configured.

2. **Integration with Spring Container**:
   - The `FactoryBean` interface integrates seamlessly with the Spring container.

3. **Type-Specific Beans**:
   - You can specify the type of object the factory will produce.

4. **Singleton or Prototype Scope**:
   - You can configure whether the object created is a singleton or a prototype.

---

### **How FactoryBean Works**
A class implements the `org.springframework.beans.factory.FactoryBean<T>` interface to act as a factory for creating objects of type `T`.

#### **Key Methods in FactoryBean**
1. **`T getObject()`**:
   - Creates and returns the object managed by the `FactoryBean`.

2. **`Class<?> getObjectType()`**:
   - Returns the type of object the factory produces.

3. **`boolean isSingleton()`**:
   - Specifies whether the object produced is a singleton or prototype.

---

### **Example: FactoryBean for Payment Gateway Client**

#### **Step 1: Third-Party Class**
This is a third-party class that you cannot modify or annotate.
```java
@Data
public class PaymentGatewayClient {
    private String endpoint;
    private String publicKey;
    private String privateKey;
}
```

---

#### **Step 2: FactoryBean Implementation**
Here, we create a `FactoryBean` to instantiate and configure the `PaymentGatewayClient`.
```java
@Component(value = "paymentGatewayClient")
public class PaymentGatewayClientFactoryBean implements FactoryBean<PaymentGatewayClient> {

    @Override
    public PaymentGatewayClient getObject() throws Exception {
        PaymentGatewayClient client = new PaymentGatewayClient();
        client.setEndpoint("https://payment/");
        client.setPrivateKey("PRIVATE");
        client.setPublicKey("PUBLIC");
        return client;
    }

    @Override
    public Class<?> getObjectType() {
        return PaymentGatewayClient.class;
    }

    @Override
    public boolean isSingleton() {
        return true; // Default is singleton
    }
}
```

- **`getObject()`**: Creates and configures the `PaymentGatewayClient` instance.
- **`getObjectType()`**: Specifies the type of object produced (`PaymentGatewayClient`).
- **`isSingleton()`**: Indicates whether the bean is a singleton.

---

#### **Step 3: Configuration**
You can explicitly import the `FactoryBean` into your Spring configuration.
```java
@Configuration
@Import(value = {
    PaymentGatewayClientFactoryBean.class
})
public class FactoryConfiguration {
}
```

---

#### **Step 4: Accessing the Bean**
You can retrieve the `PaymentGatewayClient` bean from the Spring context.
```java
PaymentGatewayClient client = applicationContext.getBean(PaymentGatewayClient.class);

Assertions.assertEquals("https://payment/", client.getEndpoint());
Assertions.assertEquals("PRIVATE", client.getPrivateKey());
Assertions.assertEquals("PUBLIC", client.getPublicKey());
```

---

### **Advantages of FactoryBean**
1. **Custom Initialization**:
   - Encapsulates complex object creation logic.

2. **Integration with Spring**:
   - Works seamlessly with Spring's dependency injection and lifecycle management.

3. **Reusable Logic**:
   - The same factory can be reused to create multiple instances with consistent configuration.

4. **Lazy Initialization**:
   - Objects are created only when they are accessed.

---

### **Common Use Cases**
1. **Third-Party Libraries**:
   - Creating beans for classes that cannot be modified or annotated.

2. **Complex Object Instantiation**:
   - When an object requires multiple steps or dependencies to initialize.

3. **Proxy Objects**:
   - Creating proxy objects for AOP or other purposes.

4. **Dynamic Configuration**:
   - Configuring beans dynamically based on runtime conditions.

---

### **Comparison: FactoryBean vs @Bean**
| Feature                  | `FactoryBean`                             | `@Bean` Method                |
|--------------------------|-------------------------------------------|--------------------------------|
| **Purpose**              | Encapsulates object creation logic        | Directly creates and configures a bean |
| **Third-Party Support**  | Ideal for third-party classes             | Requires direct control over the class |
| **Reuse**                | Reusable across multiple configurations   | Limited to the configuration class |
| **Lazy Initialization**  | Supported                                | Supported                     |
| **Proxy Creation**       | Can create proxies                       | Limited                      |

