### **`@Builder` in Lombok**

The `@Builder` annotation in Lombok is a powerful tool that simplifies the creation of complex objects. It implements the **Builder Design Pattern**, which is especially useful when creating immutable objects or objects with many optional parameters. Instead of using constructors or setters, `@Builder` provides a fluent and readable way to construct objects step by step.

---

### **Key Features of `@Builder`**

1. **Fluent API for Object Creation:**
   - Allows you to set fields using a chain of method calls.
   - Eliminates the need for multiple constructors.

2. **Static Builder Method:**
   - Lombok generates a static `builder()` method that returns a builder object.

3. **Customizable:**
   - You can customize the builder's behavior using additional Lombok annotations or parameters.

4. **Immutable Objects:**
   - Works well with `final` fields, enabling the creation of immutable objects.

5. **Supports Collections:**
   - Allows you to initialize collections like `List`, `Set`, or `Map` easily.

---

### **Basic Example**

#### **Code:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Person {
    private String id;
    private String name;
    private Integer age;
    private List<String> hobbies;
}
```

#### **Generated Code:**
- **Static Method:**
  ```java
  public static PersonBuilder builder() {
      return new PersonBuilder();
  }
  ```

- **Builder Class:**
  ```java
  public static class PersonBuilder {
      private String id;
      private String name;
      private Integer age;
      private List<String> hobbies;

      public PersonBuilder id(String id) {
          this.id = id;
          return this;
      }

      public PersonBuilder name(String name) {
          this.name = name;
          return this;
      }

      public PersonBuilder age(Integer age) {
          this.age = age;
          return this;
      }

      public PersonBuilder hobbies(List<String> hobbies) {
          this.hobbies = hobbies;
          return this;
      }

      public Person build() {
          return new Person(id, name, age, hobbies);
      }
  }
  ```

#### **Usage:**
```java
import java.util.List;

public class Main {
    public static void main(String[] args) {
        Person person = Person.builder()
                              .id("123")
                              .name("Sevendi")
                              .age(25)
                              .hobbies(List.of("Coding", "Gaming", "Reading"))
                              .build();

        System.out.println(person);
    }
}
```

##### **Output:**
```
Person(id=123, name=Sevendi, age=25, hobbies=[Coding, Gaming, Reading])
```

---

### **Advanced Examples**

#### **1. Immutable Object with `@Builder`**

You can use `@Builder` with `final` fields to create immutable objects.

##### **Code:**
```java
import lombok.Builder;
import lombok.Getter;

@Getter
@Builder
public class ImmutableProduct {
    private final String id;
    private final String name;
    private final Double price;
}
```

##### **Usage:**
```java
ImmutableProduct product = ImmutableProduct.builder()
                                           .id("P001")
                                           .name("Laptop")
                                           .price(1500.0)
                                           .build();

System.out.println(product);
```

##### **Output:**
```
ImmutableProduct(id=P001, name=Laptop, price=1500.0)
```

---

#### **2. Customizing the Builder**

You can customize the builder by adding your own methods or logic.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Employee {
    private String id;
    private String name;
    private Double salary;

    public static class EmployeeBuilder {
        public EmployeeBuilder increaseSalary(double percentage) {
            this.salary = this.salary + (this.salary * percentage / 100);
            return this;
        }
    }
}
```

##### **Usage:**
```java
Employee employee = Employee.builder()
                            .id("E001")
                            .name("Alice")
                            .salary(5000.0)
                            .increaseSalary(10) // Custom method
                            .build();

System.out.println(employee);
```

##### **Output:**
```
Employee(id=E001, name=Alice, salary=5500.0)
```

---

#### **3. Using `@Builder` with `@Singular`**

The `@Singular` annotation can be used to simplify the addition of elements to collections.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;
import lombok.Singular;
import java.util.List;

@Data
@Builder
public class Team {
    private String name;
    @Singular
    private List<String> members;
}
```

##### **Usage:**
```java
Team team = Team.builder()
                .name("Developers")
                .member("Alice")
                .member("Bob")
                .member("Charlie")
                .build();

System.out.println(team);
```

##### **Output:**
```
Team(name=Developers, members=[Alice, Bob, Charlie])
```

---

#### **4. Builder for Nested Objects**

`@Builder` can also be used to create nested objects.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Address {
    private String street;
    private String city;
    private String country;
}

@Data
@Builder
public class User {
    private String id;
    private String name;
    private Address address;
}
```

##### **Usage:**
```java
Address address = Address.builder()
                         .street("123 Main St")
                         .city("Springfield")
                         .country("USA")
                         .build();

User user = User.builder()
                .id("U001")
                .name("John Doe")
                .address(address)
                .build();

System.out.println(user);
```

##### **Output:**
```
User(id=U001, name=John Doe, address=Address(street=123 Main St, city=Springfield, country=USA))
```

---

#### **5. Builder with Default Values**

You can specify default values for fields in the builder.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class Car {
    private String brand;
    private String model;
    @Builder.Default
    private String color = "Black";
}
```

##### **Usage:**
```java
Car car = Car.builder()
             .brand("Toyota")
             .model("Corolla")
             .build();

System.out.println(car);
```

##### **Output:**
```
Car(brand=Toyota, model=Corolla, color=Black)
```

---

#### **6. Builder with Inheritance**

You can use `@Builder` with inheritance, but it requires the `@SuperBuilder` annotation.

##### **Code:**
```java
import lombok.Data;
import lombok.experimental.SuperBuilder;

@Data
@SuperBuilder
class Vehicle {
    private String brand;
    private String model;
}

@Data
@SuperBuilder
class Truck extends Vehicle {
    private Double loadCapacity;
}
```

##### **Usage:**
```java
Truck truck = Truck.builder()
                   .brand("Ford")
                   .model("F-150")
                   .loadCapacity(1000.0)
                   .build();

System.out.println(truck);
```

##### **Output:**
```
Truck(brand=Ford, model=F-150, loadCapacity=1000.0)
```

---

### **When to Use `@Builder`**

- When creating objects with many optional parameters.
- When you want a clean and readable object creation process.
- When working with immutable objects.
- When dealing with nested or hierarchical objects.

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **Fluent API**                | Provides a chainable API for object creation.                                                   |
| **Supports Collections**      | Use `@Singular` for easier collection initialization.                                            |
| **Custom Logic**              | Add custom methods to the builder for additional functionality.                                 |
| **Default Values**            | Use `@Builder.Default` to provide default values for fields.                                    |
| **Inheritance**               | Use `@SuperBuilder` for builder support in inheritance hierarchies.                            |

