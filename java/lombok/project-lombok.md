### **Introduction to Project Lombok**

**Project Lombok** is a Java library designed to reduce boilerplate code in application development. With Lombok, developers can eliminate the need to manually write repetitive code such as getters, setters, `toString`, `equals`, and other methods. Lombok automatically generates this code during the compilation process, making the codebase cleaner and easier to maintain.

---

### **1. What is Project Lombok?**

- **Definition**:  
  Project Lombok is a Java library that automatically generates boilerplate code such as getters, setters, `equals`, `hashCode`, `toString`, and more.

- **Key Benefits**:
  - Saves time by eliminating the need to write repetitive boilerplate code manually.
  - Makes code cleaner, shorter, and easier to read.
  - Reduces human errors when writing boilerplate code.

- **How It Works**:  
  Lombok uses annotations to mark certain elements (like classes or fields) that require additional code. During the compilation process, Lombok generates the necessary code automatically. The workflow is illustrated below:

  **Workflow Diagram**:
  ```
  Java Code (with Lombok annotations) --> Lombok --> Java Code (with generated boilerplate code)
  ```

---

### **2. Key Features of Lombok**

#### **a. Getter and Setter**
- **Annotations**: `@Getter` and `@Setter`
- **Purpose**: Automatically generates getter and setter methods for class fields.
- **Example**:

```java
import lombok.Getter;
import lombok.Setter;

public class Person {
    @Getter @Setter
    private String name;

    @Getter @Setter
    private int age;
}
```

**Generated Code**:
```java
public class Person {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

---

#### **b. `@ToString`**
- **Annotation**: `@ToString`
- **Purpose**: Generates a `toString` method for the class, including all fields by default.
- **Example**:

```java
import lombok.ToString;

@ToString
public class Person {
    private String name;
    private int age;
}
```

**Generated Code**:
```java
public class Person {
    private String name;
    private int age;

    @Override
    public String toString() {
        return "Person(name=" + name + ", age=" + age + ")";
    }
}
```

---

#### **c. `@EqualsAndHashCode`**
- **Annotation**: `@EqualsAndHashCode`
- **Purpose**: Generates `equals` and `hashCode` methods based on class fields.
- **Example**:

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class Person {
    private String name;
    private int age;
}
```

**Generated Code**:
```java
public class Person {
    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

---

#### **d. `@NoArgsConstructor`, `@AllArgsConstructor`, and `@RequiredArgsConstructor`**
- **Annotations**:
  - `@NoArgsConstructor`: Generates a no-argument constructor.
  - `@AllArgsConstructor`: Generates a constructor with all fields as parameters.
  - `@RequiredArgsConstructor`: Generates a constructor for final fields or fields annotated with `@NonNull`.

- **Example**:

```java
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.RequiredArgsConstructor;

@NoArgsConstructor
@AllArgsConstructor
@RequiredArgsConstructor
public class Person {
    private final String name;
    private int age;
}
```

**Generated Code**:
```java
public class Person {
    private final String name;
    private int age;

    public Person() {}

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public Person(String name) {
        this.name = name;
    }
}
```

---

#### **e. `@Data`**
- **Annotation**: `@Data`
- **Purpose**: Combines `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode`, and `@RequiredArgsConstructor`.
- **Example**:

```java
import lombok.Data;

@Data
public class Person {
    private final String name;
    private int age;
}
```

**Generated Code**:
```java
public class Person {
    private final String name;
    private int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person(name=" + name + ", age=" + age + ")";
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

---

#### **f. `@Builder`**
- **Annotation**: `@Builder`
- **Purpose**: Implements the Builder pattern for the class.
- **Example**:

```java
import lombok.Builder;

@Builder
public class Person {
    private String name;
    private int age;
}
```

**Usage**:
```java
Person person = Person.builder()
                      .name("John")
                      .age(30)
                      .build();
```

---

#### **g. `@Slf4j`**
- **Annotation**: `@Slf4j`
- **Purpose**: Provides a logger instance (`log`) for the class.
- **Example**:

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Example {
    public void doSomething() {
        log.info("This is a log message");
    }
}
```

---

### **3. Why Use Lombok?**

- **Reduces Boilerplate Code**: Automatically generates repetitive code.
- **Improves Readability**: Keeps the codebase clean and focused on business logic.
- **Saves Time**: Eliminates the need to manually write getters, setters, and other utility methods.

---

### **4. Limitations of Lombok**

- **Dependency**: Requires adding Lombok as a dependency in your project.
- **IDE Support**: Some IDEs may require additional plugins or configurations to fully support Lombok.
- **Code Generation**: Since code is generated at compile-time, debugging may be slightly more challenging.

---

### **5. Adding Lombok to Your Project**

#### **Maven**
Add this dependency to your `pom.xml`:
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.28</version>
    <scope>provided</scope>
</dependency>
```

#### **Gradle**
Add this to your `build.gradle`:
```groovy
dependencies {
    compileOnly 'org.projectlombok:lombok:1.18.28'
    annotationProcessor 'org.projectlombok:lombok:1.18.28'
}
```
