### **Mockito Overview**

Mockito is a **mocking framework** for Java that allows developers to create mock objects and define their behavior. It is widely used for **unit testing** to isolate the system under test (SUT) from its dependencies, ensuring the test focuses only on the logic of the class being tested.

---

### **Key Concepts of Mockito**

1. **Mocking**:
   - Mocking is the process of creating a fake object (mock) that simulates the behavior of a real object.
   - This is useful when testing a class that depends on other classes or external systems (e.g., databases, APIs).

2. **Why Use Mockito?**:
   - To avoid testing real dependencies.
   - To control the behavior of dependencies.
   - To verify interactions between the class under test and its dependencies.

3. **Mockito Features**:
   - Creating mock objects.
   - Stubbing methods (defining behavior for mock methods).
   - Verifying method calls and interactions.
   - Handling exceptions in mocks.
   - Spying on real objects.
   - Argument matchers for flexible stubbing and verification.

---

### **Basic Mockito Annotations**

1. **`@Mock`**:
   - Used to create a mock object.
   - Example:
     ```java
     @Mock
     private MyService myService;
     ```

2. **`@InjectMocks`**:
   - Automatically injects mock objects into the class under test.
   - Example:
     ```java
     @InjectMocks
     private MyController myController;
     ```

3. **`@Captor`**:
   - Used to capture arguments passed to a method.
   - Example:
     ```java
     @Captor
     ArgumentCaptor<String> captor;
     ```

4. **`@Spy`**:
   - Creates a partial mock, where real methods are called unless explicitly stubbed.
   - Example:
     ```java
     @Spy
     private MyService myService;
     ```

5. **`@RunWith(MockitoJUnitRunner.class)`**:
   - A JUnit runner that initializes mocks and injects them into the test class.

---

### **Basic Mockito Usage**

#### 1. **Creating a Mock Object**
   ```java
   List<String> mockList = Mockito.mock(List.class);
   ```

#### 2. **Stubbing (Defining Behavior)**
   - Define what a method should return when called:
     ```java
     Mockito.when(mockList.get(0)).thenReturn("Hello");
     ```

#### 3. **Verifying Interactions**
   - Verify if a method was called:
     ```java
     Mockito.verify(mockList).get(0);
     ```

#### 4. **Asserting Results**
   - Use assertions to verify the expected output:
     ```java
     assertEquals("Hello", mockList.get(0));
     ```

---

### **Examples**

#### **Example 1: Basic Mocking**
```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class MockitoExample {

    @Test
    public void testMocking() {
        // Create mock object
        List<String> mockList = Mockito.mock(List.class);

        // Define behavior
        Mockito.when(mockList.get(0)).thenReturn("Hello");

        // Test behavior
        assertEquals("Hello", mockList.get(0));

        // Verify interaction
        Mockito.verify(mockList).get(0);
    }
}
```

---

#### **Example 2: Mocking a Service**
```java
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class ServiceTest {

    @Mock
    private MyService myService;

    @Test
    public void testService() {
        MockitoAnnotations.openMocks(this);

        // Define behavior for service
        Mockito.when(myService.getData()).thenReturn("Mock Data");

        // Test service
        String result = myService.getData();
        assertEquals("Mock Data", result);

        // Verify interaction
        Mockito.verify(myService).getData();
    }

    interface MyService {
        String getData();
    }
}
```

---

#### **Example 3: Using `@InjectMocks`**
```java
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class ControllerTest {

    @Mock
    private MyService myService;

    @InjectMocks
    private MyController myController;

    @Test
    public void testController() {
        MockitoAnnotations.openMocks(this);

        // Define behavior for service
        Mockito.when(myService.getData()).thenReturn("Mock Data");

        // Test controller
        String result = myController.getData();
        assertEquals("Mock Data", result);

        // Verify interaction
        Mockito.verify(myService).getData();
    }

    static class MyController {
        private final MyService myService;

        public MyController(MyService myService) {
            this.myService = myService;
        }

        public String getData() {
            return myService.getData();
        }
    }

    interface MyService {
        String getData();
    }
}
```

---

#### **Example 4: Capturing Arguments**
```java
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Mockito;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class ArgumentCaptorTest {

    @Test
    public void testArgumentCaptor() {
        MyService mockService = Mockito.mock(MyService.class);

        // Call method with arguments
        mockService.saveData("Test Data");

        // Capture argument
        ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
        Mockito.verify(mockService).saveData(captor.capture());

        // Assert captured value
        assertEquals("Test Data", captor.getValue());
    }

    interface MyService {
        void saveData(String data);
    }
}
```

---

#### **Example 5: Throwing Exceptions**
```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import static org.junit.jupiter.api.Assertions.assertThrows;

public class ExceptionTest {

    @Test
    public void testException() {
        MyService mockService = Mockito.mock(MyService.class);

        // Define behavior to throw exception
        Mockito.when(mockService.getData()).thenThrow(new RuntimeException("Error"));

        // Test exception
        assertThrows(RuntimeException.class, mockService::getData);
    }

    interface MyService {
        String getData();
    }
}
```

---

#### **Example 6: Partial Mocking with `@Spy`**
```java
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;
import org.mockito.Spy;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class SpyTest {

    @Spy
    private MyService myService = new MyService();

    @Test
    public void testSpy() {
        // Override specific behavior
        Mockito.doReturn("Mock Response").when(myService).getData();

        // Test spy
        assertEquals("Mock Response", myService.getData());

        // Verify real method calls
        Mockito.verify(myService).getData();
    }

    static class MyService {
        public String getData() {
            return "Real Response";
        }
    }
}
```

---

### **Mockito Best Practices**

1. **Focus on Unit Testing**:
   - Use Mockito to test one unit of code at a time. Avoid testing multiple classes together (use integration tests for that).

2. **Avoid Overusing Mocks**:
   - Only mock dependencies that are external to the class under test.
   - Avoid mocking value objects or simple classes.

3. **Use `@InjectMocks` Wisely**:
   - Ensure the class under test is properly initialized with its dependencies.

4. **Verify Interactions**:
   - Verify that the class under test interacts with its dependencies as expected.

5. **Keep Tests Simple**:
   - Avoid overly complex mocks or stubs. Keep your tests easy to read and maintain.

---

### **Resources**

- **Mockito Documentation**: [https://site.mockito.org/](https://site.mockito.org/)
