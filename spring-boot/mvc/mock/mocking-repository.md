### **Mockito for Repository Testing**

Mockito is widely used to test service classes that interact with repositories. Repositories typically handle database operations, and mocking them allows developers to test the business logic in service classes without depending on an actual database.

---

### **Scenario: Testing a Repository Interaction**

In this example, we have the following structure:

1. **Class `Person`:** A model representing a person with fields `id` and `name`.
2. **Interface `PersonRepository`:** A repository interface to interact with the database.
3. **Class `PersonService`:** A service class that uses `PersonRepository` to perform business logic.
4. **Test Class `PersonServiceTest`:** A test class to verify the functionality of `PersonService` using Mockito.

---

### **Code Walkthrough**

#### **1. Model Class: `Person`**
The `Person` class is a simple POJO (Plain Old Java Object) that represents a person.

```java
public class Person {
    private String id;
    private String name;

    public Person(String id, String name) {
        this.id = id;
        this.name = name;
    }

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

---

#### **2. Repository Interface: `PersonRepository`**
The repository interface defines a method to fetch a `Person` by their ID.

```java
package belajar.javatest.repository;

import belajar.javatest.model.Person;

public interface PersonRepository {
    Person selectById(String id);
}
```

---

#### **3. Service Class: `PersonService`**
The service class contains business logic. It uses `PersonRepository` to fetch data and throws an exception if the person is not found.

```java
package belajar.javatest.service;

import belajar.javatest.model.Person;
import belajar.javatest.repository.PersonRepository;

public class PersonService {
    private final PersonRepository personRepository;

    public PersonService(PersonRepository personRepository) {
        this.personRepository = personRepository;
    }

    public Person get(String id) {
        Person person = personRepository.selectById(id);
        if (person != null) {
            return person;
        } else {
            throw new IllegalArgumentException("Person not found");
        }
    }
}
```

---

#### **4. Test Class: `PersonServiceTest`**
The test class uses Mockito to mock the `PersonRepository` and test the `PersonService` logic.

##### **Setup with Mockito**
- Use `@ExtendWith(MockitoExtension.class)` to enable Mockito annotations.
- Use `@Mock` to create a mock instance of `PersonRepository`.
- Inject the mock into `PersonService`.

```java
package belajar.javatest;

import belajar.javatest.model.Person;
import belajar.javatest.repository.PersonRepository;
import belajar.javatest.service.PersonService;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.Mockito;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class PersonServiceTest {

    @Mock
    private PersonRepository personRepository;

    private PersonService personService;

    @BeforeEach
    public void setUp() {
        personService = new PersonService(personRepository);
    }

    @Test
    public void testGetNotFound() {
        // Arrange
        when(personRepository.selectById("not_found")).thenReturn(null);

        // Act & Assert
        assertThrows(IllegalArgumentException.class, () -> personService.get("not_found"));
    }

    @Test
    public void testGetSuccess() {
        // Arrange
        Person person = new Person("eko", "Eko");
        when(personRepository.selectById("eko")).thenReturn(person);

        // Act
        Person result = personService.get("eko");

        // Assert
        assertEquals("eko", result.getId());
        assertEquals("Eko", result.getName());
    }
}
```

---

### **Explanation of the Test Cases**

1. **Test Case: `testGetNotFound`**
   - **Objective:** Verify that the service throws an exception when the repository returns `null`.
   - **Steps:**
     - Stub the `selectById` method to return `null` for the given ID.
     - Use `assertThrows` to ensure that an `IllegalArgumentException` is thrown.

2. **Test Case: `testGetSuccess`**
   - **Objective:** Verify that the service returns the correct `Person` object when the repository returns valid data.
   - **Steps:**
     - Stub the `selectById` method to return a `Person` object for the given ID.
     - Call the `get` method and verify the returned object's properties using assertions.

---

### **Additional Examples**

#### **1. Verifying Method Calls**
Mockito allows you to verify that a specific method was called on the mock.

```java
@Test
public void testVerifyMethodCall() {
    // Arrange
    Person person = new Person("eko", "Eko");
    when(personRepository.selectById("eko")).thenReturn(person);

    // Act
    personService.get("eko");

    // Assert
    verify(personRepository, times(1)).selectById("eko");
}
```

---

#### **2. Capturing Arguments**
You can capture the arguments passed to a mocked method using `ArgumentCaptor`.

```java
import org.mockito.ArgumentCaptor;

@Test
public void testArgumentCaptor() {
    // Arrange
    ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class);
    when(personRepository.selectById("eko")).thenReturn(new Person("eko", "Eko"));

    // Act
    personService.get("eko");

    // Assert
    verify(personRepository).selectById(captor.capture());
    assertEquals("eko", captor.getValue());
}
```

---

#### **3. Throwing Exceptions**
You can stub a mocked method to throw an exception.

```java
@Test
public void testThrowException() {
    // Arrange
    when(personRepository.selectById("error")).thenThrow(new RuntimeException("Database error"));

    // Act & Assert
    assertThrows(RuntimeException.class, () -> personService.get("error"));
}
```

---

#### **4. Partial Mocking with `@Spy`**
You can use `@Spy` to create a partial mock that calls real methods unless explicitly stubbed.

```java
import org.mockito.Spy;

public class PersonServicePartialTest {

    @Spy
    private PersonRepository personRepository = new PersonRepository() {
        @Override
        public Person selectById(String id) {
            return new Person("real_id", "Real Name");
        }
    };

    @Test
    public void testSpy() {
        // Stub a specific method
        doReturn(new Person("spy_id", "Spy Name")).when(personRepository).selectById("spy_id");

        // Act
        Person result = personRepository.selectById("spy_id");

        // Assert
        assertEquals("Spy Name", result.getName());
    }
}
```

---

### **Best Practices for Repository Testing**

1. **Focus on Business Logic**:
   - Mock repositories to isolate the service logic being tested.

2. **Avoid Mocking Everything**:
   - Only mock dependencies that involve external systems (e.g., databases, APIs).

3. **Use `@Mock` and `@InjectMocks`**:
   - Simplify setup by using annotations to inject mocks into the class under test.

4. **Verify Interactions**:
   - Use `verify` to ensure the service interacts with the repository as expected.

5. **Handle Edge Cases**:
   - Test scenarios where the repository returns `null`, throws exceptions, or returns unexpected data.

---

### **Mockito Resources**

- **Official Documentation**: [Mockito](https://site.mockito.org/)
- **JUnit Integration**: Use with JUnit 4 or JUnit 5 for seamless testing.
- **Advanced Features**:
  - Use `ArgumentMatchers` for flexible stubbing.
  - Explore `BDDMockito` for behavior-driven testing.
