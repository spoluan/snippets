# **Using `@DependsOn` in Spring**

The `@DependsOn` annotation in Spring is used to declare that a bean depends on one or more other beans. This ensures that the dependent beans are initialized before the annotated bean.

---

## **1. Code Example**

### **Definition**
```java
@Bean
@DependsOn(value = {"bar"})
public Foo foo() {
    log.info("Create new Foo");
    return new Foo();
}

@Bean
public Bar bar() {
    log.info("Create new Bar");
    return new Bar();
}
```

### **Execution Flow**
1. The `foo()` bean is annotated with `@DependsOn("bar")`.
2. This means that the `bar()` bean will always be created **before** the `foo()` bean.
3. When the application context initializes:
   - `log.info("Create new Bar")` is executed first.
   - Then, `log.info("Create new Foo")` is executed.

---

## **2. Purpose of `@DependsOn`**

The `@DependsOn` annotation is used when:
- **Bean Initialization Order Matters**: You need to ensure that one bean is initialized before another.
- **Side Effects or Dependencies**: The dependent bean performs some setup or initialization that the annotated bean relies on.

For example:
- A database connection bean (`DataSource`) must be initialized before a repository bean (`FooRepository`).
- A caching service bean must be initialized before beans that rely on cached data.

---

## **3. Practical Use Case**

Consider a scenario where a `Bar` bean sets up some configuration or performs initialization tasks that the `Foo` bean depends on. Using `@DependsOn`, you can enforce that `Bar` is fully initialized before `Foo`.

---

## **4. Key Points**

- **Order of Initialization**: `@DependsOn` only controls the order of initialization, not dependency injection. It does not inject one bean into another.
- **Bean Names**: The value of the `@DependsOn` annotation refers to the **bean names**, not the class names.
- **Lazy Initialization**: If beans are lazily initialized, `@DependsOn` ensures the dependent beans are initialized first, even if they are also lazy.

---

## **5. Alternative Without `@DependsOn`**

If you want to enforce a dependency without using `@DependsOn`, you can directly inject the dependent bean into the other bean:

```java
@Bean
public Foo foo(Bar bar) {
    log.info("Create new Foo");
    return new Foo();
}

@Bean
public Bar bar() {
    log.info("Create new Bar");
    return new Bar();
}
```

In this case:
- The `Bar` bean is injected into the `Foo` bean.
- Spring will automatically ensure that `Bar` is initialized before `Foo`.

