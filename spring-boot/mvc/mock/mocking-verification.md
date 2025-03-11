### **Mocking Verification in Mockito**

Mocking verification in **Mockito** is a way to ensure that specific methods on mock objects are called with the expected arguments and the correct number of times during a test. This is especially useful when testing methods that do not return any value (`void` methods) or when the correctness of the test depends on the interactions with mocked dependencies.

---

### **Why Use Verification?**

1. **Ensure Method Calls**:
   - Verify that a method on a mocked object is called during the execution of the test.
   - This is particularly useful for `void` methods, where there is no return value to assert.

2. **Prevent Missing Logic**:
   - If a method is not called as expected, it might indicate missing or incorrect logic in the code.

3. **Catch Over-Invocations**:
   - Ensure that methods are not called more times than necessary (e.g., duplicated logic or redundant calls).

4. **Validate Interactions**:
   - Verify the interaction between the class under test and its dependencies.

---

### **Basic Syntax for Verification**

1. **Verify Method Call**:
   ```java
   verify(mockObject).methodName(arguments);
   ```

2. **Verify Number of Invocations**:
   ```java
   verify(mockObject, times(1)).methodName(arguments); // Called exactly once
   verify(mockObject, never()).methodName(arguments);  // Never called
   verify(mockObject, atLeast(2)).methodName(arguments); // At least 2 times
   verify(mockObject, atMost(3)).methodName(arguments);  // At most 3 times
   ```

3. **Verify with Argument Matchers**:
   ```java
   verify(mockObject).methodName(anyString(), eq("expectedValue"));
   ```

---

### **Example: Verifying Method Calls**

#### **Scenario**
- We have a `PersonService` class that interacts with a `PersonRepository`.
- The `register` method creates a `Person` object and calls the `insert` method of the repository to save the data.

---

#### **Code Walkthrough**

##### **1. Interface: `PersonRepository`**
The repository interface has a `void` method to insert a `Person`.

```java
package belajar.javatest.repository;

import belajar.javatest.model.Person;

public interface PersonRepository {
    void insert(Person person);
}
```

---

##### **2. Class: `PersonService`**
The service class contains business logic that interacts with the repository.

```java
package belajar.javatest.service;

import belajar.javatest.model.Person;
import belajar.javatest.repository.PersonRepository;

import java.util.UUID;

public class PersonService {
    private final PersonRepository personRepository;

    public PersonService(PersonRepository personRepository) {
        this.personRepository = personRepository;
    }

    public Person register(String name) {
        var person = new Person(UUID.randomUUID().toString(), name);
        personRepository.insert(person);
        return person;
    }
}
```

---

##### **3. Test Class: `PersonServiceTest`**

This test case verifies that the `insert` method of the repository is called exactly once with the correct arguments.

```java
package belajar.javatest;

import belajar.javatest.model.Person;
import belajar.javatest.repository.PersonRepository;
import belajar.javatest.service.PersonService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class PersonServiceTest {

    @Mock
    private PersonRepository personRepository;

    private PersonService personService;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        personService = new PersonService(personRepository);
    }

    @Test
    public void testRegisterSuccess() {
        // Act
        var person = personService.register("Sevendi");

        // Assert
        assertEquals("Sevendi", person.getName());
        assertNotNull(person.getId());

        // Verify that the insert method was called exactly once
        verify(personRepository, times(1)).insert(person);
    }
}
```

---

### **Common Verification Scenarios**

#### **1. Verifying a Method is Called**
Ensure that a specific method is called during the test.

```java
verify(personRepository).insert(any(Person.class));
```

#### **2. Verifying No Interactions**
Ensure that no methods are called on the mock.

```java
verifyNoInteractions(personRepository);
```

#### **3. Verifying Specific Arguments**
Capture the arguments passed to a mocked method and verify them.

```java
@Test
public void testArgumentCaptor() {
    // Arrange
    ArgumentCaptor<Person> captor = ArgumentCaptor.forClass(Person.class);

    // Act
    personService.register("Sevendi");

    // Assert
    verify(personRepository).insert(captor.capture());
    assertEquals("Sevendi", captor.getValue().getName());
}
```

#### **4. Verifying Method is Never Called**
Ensure that a method is not called.

```java
verify(personRepository, never()).insert(any(Person.class));
```

#### **5. Verifying Multiple Calls**
Ensure that a method is called a specific number of times.

```java
verify(personRepository, times(2)).insert(any(Person.class));
```

---

### **Advanced Examples**

#### **1. Verifying Order of Method Calls**
You can verify the order in which methods are called using `InOrder`.

```java
@Test
public void testVerifyOrder() {
    // Arrange
    InOrder inOrder = inOrder(personRepository);

    // Act
    personService.register("Sevendi");

    // Assert
    inOrder.verify(personRepository).insert(any(Person.class));
}
```

---

#### **2. Verifying No More Interactions**
Ensure that no other methods are called on the mock after specific interactions.

```java
@Test
public void testNoMoreInteractions() {
    // Act
    personService.register("Sevendi");

    // Verify insert method is called
    verify(personRepository).insert(any(Person.class));

    // Ensure no more interactions with the mock
    verifyNoMoreInteractions(personRepository);
}
```

---

#### **3. Verifying with Custom Matchers**
You can use `ArgumentMatchers` to verify method calls with flexible conditions.

```java
@Test
public void testCustomMatcher() {
    // Act
    personService.register("Sevendi");

    // Verify insert is called with a Person whose name is "Sevendi"
    verify(personRepository).insert(argThat(person -> person.getName().equals("Sevendi")));
}
```

---

### **Common Mistakes in Verification**

1. **Not Verifying Critical Interactions**:
   - Forgetting to verify critical method calls (e.g., saving data to the database).

2. **Over-Verifying**:
   - Verifying every single interaction unnecessarily, which can make tests harder to maintain.

3. **Ignoring Argument Matching**:
   - Failing to verify the arguments passed to the mocked methods.

4. **Not Using `verifyNoMoreInteractions`**:
   - Missing redundant or unintended method calls.

---

### **Best Practices for Mocking Verification**

1. **Focus on Key Interactions**:
   - Only verify critical interactions that are essential to the business logic.

2. **Use Argument Captors**:
   - Capture arguments to ensure that the correct data is passed to mocked methods.

3. **Combine Verification with Assertions**:
   - Verify interactions and assert the outcomes to ensure comprehensive testing.

4. **Keep Tests Simple**:
   - Avoid over-complicating tests with excessive verifications.

5. **Use `InOrder` for Sequential Logic**:
   - Verify the order of method calls when the sequence is important.
