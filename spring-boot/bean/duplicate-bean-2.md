# **Handling Duplicate Beans in Spring**

In Spring, it’s common to encounter situations where multiple beans of the same type are defined in the application context. This can lead to ambiguity when the framework tries to inject or retrieve a bean of that type. To resolve such issues, Spring provides mechanisms like **bean naming**, **`@Primary` annotation**, and **qualifiers**.

---

## **1. Defining Multiple Beans of the Same Type**

In the example, two beans of the same type (`Foo`) are defined in the application context. Each bean is created using the `@Bean` annotation in a configuration class.

### **Code Example**
```java
@Configuration
public class AppConfig {

    @Primary
    @Bean
    public Foo foo1() {
        Foo foo = new Foo();
        return foo;
    }

    @Bean
    public Foo foo2() {
        Foo foo = new Foo();
        return foo;
    }
}
```

Here:
- **`foo1`** is marked as the primary bean using `@Primary`.
- **`foo2`** is another bean of the same type but is not marked as primary.

---

## **2. Accessing Duplicate Beans**

When multiple beans of the same type exist, you can specify which bean to retrieve by using the **bean name**. The `ApplicationContext` provides the `getBean(String name, Class<T> requiredType)` method for this purpose.

### **Code Example**
```java
Foo foo1 = applicationContext.getBean("foo1", Foo.class);
Foo foo2 = applicationContext.getBean("foo2", Foo.class);

Assertions.assertNotSame(foo1, foo2);
```

- **`getBean("foo1", Foo.class)`** retrieves the bean named `foo1`.
- **`getBean("foo2", Foo.class)`** retrieves the bean named `foo2`.
- The `assertNotSame(foo1, foo2)` assertion ensures that these are two distinct instances.

---

## **3. Using `@Primary` to Resolve Ambiguities**

The `@Primary` annotation tells Spring to consider the annotated bean as the default choice when multiple beans of the same type exist. If no specific bean name or qualifier is provided during injection, Spring will use the bean marked with `@Primary`.

### **Example**
```java
@Primary
@Bean
public Foo foo1() {
    return new Foo();
}

@Bean
public Foo foo2() {
    return new Foo();
}
```

In this case:
- If a `Foo` bean is injected without specifying a name or qualifier, Spring will inject `foo1` because it is marked as `@Primary`.

---

## **4. Naming Beans Explicitly**

Instead of relying on method names, you can explicitly assign names to beans using the `value` attribute of the `@Bean` annotation.

### **Code Example**
```java
@Configuration
public class AppConfig {

    @Primary
    @Bean(value = "fooFirst")
    public Foo foo1() {
        return new Foo();
    }

    @Bean(value = "fooSecond")
    public Foo foo2() {
        return new Foo();
    }
}
```

Here:
- The bean previously named `foo1` is now explicitly named **`fooFirst`**.
- The bean previously named `foo2` is now explicitly named **`fooSecond`**.

### **Accessing Named Beans**
```java
Foo foo1 = applicationContext.getBean("fooFirst", Foo.class);
Foo foo2 = applicationContext.getBean("fooSecond", Foo.class);

Assertions.assertNotSame(foo1, foo2);
```

This approach provides greater clarity and avoids potential conflicts when method names change.

---

## **5. Using `@Qualifier` for Dependency Injection**

When injecting beans of the same type, you can use the `@Qualifier` annotation to specify which bean to inject.

### **Example**
```java
@Component
public class FooService {

    private final Foo foo;

    public FooService(@Qualifier("fooSecond") Foo foo) {
        this.foo = foo;
    }
}
```

In this case:
- The `@Qualifier("fooSecond")` ensures that the `fooSecond` bean is injected into the `FooService` class.

---

## **6. Summary of Key Concepts**

| Feature                  | Description                                                                                     |
|--------------------------|-------------------------------------------------------------------------------------------------|
| **Multiple Beans**       | You can define multiple beans of the same type in the Spring context.                          |
| **Bean Naming**          | By default, the method name is the bean name, but you can explicitly name beans using `value`.  |
| **@Primary**             | Marks a bean as the default choice when multiple beans of the same type exist.                 |
| **ApplicationContext**   | Use `getBean(String name, Class<T> requiredType)` to retrieve a specific bean by name.          |
| **@Qualifier**           | Used to specify which bean to inject when multiple beans of the same type exist.               |

---

## **7. Practical Use Cases**

### **Scenario 1: Default Bean with `@Primary`**
Use `@Primary` when one bean is the most commonly used and should be the default.

### **Scenario 2: Explicit Bean Names**
Use explicit bean names when you need clarity or when the default method name is not descriptive enough.

### **Scenario 3: Dependency Injection with `@Qualifier`**
Use `@Qualifier` when injecting a specific bean into a dependent component.

