

### 1. Introduction to `@TestConfiguration`

`@TestConfiguration` is a specialized version of `@Configuration` that is only used in the context of tests. It allows you to define beans that are specific to your test cases, overriding or complementing the beans defined in the main application context.

#### Key Use Cases:
- To define real implementations of services or components for testing.
- To override beans in the application context with custom test-specific configurations.
- To inject dependencies and custom parameters into beans in the test context.

---

### 2. Basic Example: Defining a Real Service in Tests

#### Service Class
```java
@Service
public class MyService {
    public String process() {
        return "Processed by MyService";
    }
}
```

#### Test Class
```java
@SpringBootTest
public class MyServiceTest {

    @Autowired
    private MyService myService;

    @TestConfiguration
    static class TestConfig {
        @Bean
        public MyService myService() {
            return new MyService(); // Real service bean
        }
    }

    @Test
    public void testMyService() {
        String result = myService.process();
        assertEquals("Processed by MyService", result);
    }
}
```

---

### 3. Service with Constructor Parameters

If your service has a constructor that requires parameters, you can pass them in the `@TestConfiguration` class.

#### Service with Constructor Parameters
```java
@Service
public class MyService {

    private final String customParam;

    public MyService(String customParam) {
        this.customParam = customParam;
    }

    public String process() {
        return "Processed with " + customParam;
    }
}
```

#### Test Class
```java
@SpringBootTest
public class MyServiceTest {

    @Autowired
    private MyService myService;

    @TestConfiguration
    static class TestConfig {
        @Bean
        public MyService myService() {
            return new MyService("Custom Parameter");
        }
    }

    @Test
    public void testMyService() {
        String result = myService.process();
        assertEquals("Processed with Custom Parameter", result);
    }
}
```

---

### 4. Service with Multiple Dependencies

When your service has multiple constructor parameters, including `@Autowired` dependencies, you can define the required beans and pass them into the constructor.

#### Service with Dependencies
```java
@Service
public class MyService {

    private final AnotherService anotherService;
    private final String customParam;

    public MyService(AnotherService anotherService, String customParam) {
        this.anotherService = anotherService;
        this.customParam = customParam;
    }

    public String process() {
        return anotherService.doSomething() + " with " + customParam;
    }
}
```

#### Dependency Service
```java
@Service
public class AnotherService {
    public String doSomething() {
        return "Done by AnotherService";
    }
}
```

#### Test Class
```java
@SpringBootTest
public class MyServiceTest {

    @Autowired
    private MyService myService;

    @TestConfiguration
    static class TestConfig {

        @Bean
        public AnotherService anotherService() {
            return new AnotherService(); // Real dependency bean
        }

        @Bean
        public MyService myService(AnotherService anotherService) {
            return new MyService(anotherService, "Custom Parameter");
        }
    }

    @Test
    public void testMyService() {
        String result = myService.process();
        assertEquals("Done by AnotherService with Custom Parameter", result);
    }
}
```

---

### 5. Using Mock Services Alongside Real Services

You can mix real services and mock services in your test configuration using `@MockBean`.

#### Example
```java
@SpringBootTest
public class MyServiceTest {

    @Autowired
    private MyService myService;

    @MockBean
    private AnotherService anotherService; // Mocked dependency

    @TestConfiguration
    static class TestConfig {
        @Bean
        public MyService myService(AnotherService anotherService) {
            return new MyService(anotherService, "Custom Parameter");
        }
    }

    @Test
    public void testMyService() {
        // Define mock behavior
        Mockito.when(anotherService.doSomething()).thenReturn("Mocked AnotherService");

        String result = myService.process();
        assertEquals("Mocked AnotherService with Custom Parameter", result);
    }
}
```

---

### 6. Testing with Profiles

You can use Spring profiles to load different configurations for tests.

#### Example
```java
@TestConfiguration
@Profile("test")
static class TestConfig {
    @Bean
    public MyService myService(AnotherService anotherService) {
        return new MyService(anotherService, "Test Parameter");
    }
}
```

Set the profile in your test:
```java
@SpringBootTest
@ActiveProfiles("test")
public class MyServiceTest {
    // Test code here
}
```

---

### 7. Best Practices

1. **Keep Test Configuration Isolated**:
   - Use `@TestConfiguration` to ensure test-specific beans don’t interfere with the main application context.

2. **Use `@MockBean` for External Dependencies**:
   - Mock external dependencies (like services or repositories) unless you’re testing integration.

3. **Avoid Overcomplicating Test Configurations**:
   - Keep your `@TestConfiguration` simple and focused on the specific test case.

4. **Use Profiles for Complex Scenarios**:
   - Use Spring profiles if you need different configurations for different test environments.

5. **Limit the Scope of `@TestConfiguration`**:
   - Define `@TestConfiguration` as an inner static class within the test class to limit its scope.

---

### Summary of Possible Examples

| Scenario                                  | Solution                                                                 |
|-------------------------------------------|--------------------------------------------------------------------------|
| Real service with no dependencies         | Define a bean in `@TestConfiguration`.                                   |
| Service with custom parameters            | Pass parameters directly in the `@TestConfiguration` bean definition.   |
| Service with autowired dependencies       | Define dependent beans in `@TestConfiguration`.                         |
| Mix of real and mock services             | Use `@TestConfiguration` for real beans and `@MockBean` for mocks.      |
| Testing with different environments       | Use `@TestConfiguration` with profiles (`@Profile` and `@ActiveProfiles`). |
