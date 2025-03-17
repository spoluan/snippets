### **`@Singular` in Lombok**

The `@Singular` annotation in Lombok is a helper annotation used in conjunction with `@Builder`. It simplifies the creation of **collections** (like `List`, `Set`, or `Map`) within a builder. Normally, when building an object with collections, you’d need to create and pass the entire collection directly. With `@Singular`, Lombok generates methods to add individual elements to the collection, making the builder more flexible and easier to use.

---

### **Key Features of `@Singular`**

1. **Simplifies Collection Handling:**
   - Instead of passing the entire collection, you can add elements one by one using the generated methods.

2. **Supports `List`, `Set`, and `Map`:**
   - Works seamlessly with these commonly used collection types.

3. **Generated Methods for Collections:**
   - Adds methods to add single items (`addX`), clear the collection (`clearX`), and set the entire collection (`x`).

4. **Immutable Collections:**
   - By default, the generated collection in the built object is immutable.

5. **Automatic Null Safety:**
   - Lombok ensures that the collection is initialized even if no elements are added.

---

### **How `@Singular` Works**

When you annotate a collection field with `@Singular`, Lombok generates:

1. **Methods to Add Items:**
   - For example, if the field is `List<String> hobbies`, Lombok generates a method like `hobby(String hobby)` to add a single hobby.

2. **Method to Clear the Collection:**
   - A method like `clearHobbies()` is generated to clear all elements from the collection.

3. **Immutable Collection in the Built Object:**
   - The final collection in the built object is made immutable to prevent accidental modifications.

---

### **Basic Example**

#### **Code:**
```java
import lombok.Builder;
import lombok.Data;
import lombok.Singular;
import java.util.List;

@Data
@Builder
public class Person {
    private String id;
    private String name;
    private Integer age;

    @Singular
    private List<String> hobbies;
}
```

#### **Generated Methods:**
1. **Add Single Hobby:**
   ```java
   public PersonBuilder hobby(String hobby) {
       if (this.hobbies == null) {
           this.hobbies = new ArrayList<>();
       }
       this.hobbies.add(hobby);
       return this;
   }
   ```

2. **Clear All Hobbies:**
   ```java
   public PersonBuilder clearHobbies() {
       if (this.hobbies != null) {
           this.hobbies.clear();
       }
       return this;
   }
   ```

3. **Immutable Collection:**
   ```java
   public Person build() {
       List<String> hobbies;
       switch (this.hobbies == null ? 0 : this.hobbies.size()) {
           case 0:
               hobbies = Collections.emptyList();
               break;
           case 1:
               hobbies = Collections.singletonList(this.hobbies.get(0));
               break;
           default:
               hobbies = Collections.unmodifiableList(new ArrayList<>(this.hobbies));
       }
       return new Person(id, name, age, hobbies);
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
                              .age(30)
                              .hobby("Gaming")
                              .hobby("Reading")
                              .hobby("Traveling")
                              .build();

        System.out.println(person);
    }
}
```

##### **Output:**
```
Person(id=123, name=Sevendi, age=30, hobbies=[Gaming, Reading, Traveling])
```

---

### **Advanced Examples**

#### **1. Using `@Singular` with `Set`**

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;
import lombok.Singular;
import java.util.Set;

@Data
@Builder
public class Team {
    private String name;

    @Singular
    private Set<String> members;
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

#### **2. Using `@Singular` with `Map`**

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;
import lombok.Singular;
import java.util.Map;

@Data
@Builder
public class Configuration {
    private String name;

    @Singular
    private Map<String, String> settings;
}
```

##### **Usage:**
```java
Configuration config = Configuration.builder()
                                     .name("AppConfig")
                                     .setting("theme", "dark")
                                     .setting("language", "en")
                                     .build();

System.out.println(config);
```

##### **Output:**
```
Configuration(name=AppConfig, settings={theme=dark, language=en})
```

---

#### **3. Clearing Collections**

You can use the `clearX()` method to remove all elements from the collection.

##### **Code:**
```java
Person person = Person.builder()
                      .id("123")
                      .name("Sevendi")
                      .hobby("Gaming")
                      .hobby("Reading")
                      .clearHobbies() // Clears all hobbies
                      .build();

System.out.println(person);
```

##### **Output:**
```
Person(id=123, name=Sevendi, age=null, hobbies=[])
```

---

#### **4. Combining `@Singular` with Other Fields**

You can mix `@Singular` with non-collection fields in the same class.

##### **Code:**
```java
import lombok.Builder;
import lombok.Data;
import lombok.Singular;
import java.util.List;

@Data
@Builder
public class Book {
    private String title;
    private String author;

    @Singular
    private List<String> chapters;
}
```

##### **Usage:**
```java
Book book = Book.builder()
                .title("Learning Lombok")
                .author("John Doe")
                .chapter("Introduction")
                .chapter("Getting Started")
                .chapter("Advanced Topics")
                .build();

System.out.println(book);
```

##### **Output:**
```
Book(title=Learning Lombok, author=John Doe, chapters=[Introduction, Getting Started, Advanced Topics])
```

---

#### **5. Immutable Collections**

The collections generated by `@Singular` are immutable in the built object. Any attempt to modify them will throw an exception.

##### **Code:**
```java
Person person = Person.builder()
                      .id("123")
                      .name("Sevendi")
                      .hobby("Gaming")
                      .build();

person.getHobbies().add("Reading"); // Throws UnsupportedOperationException
```

---

### **Limitations of `@Singular`**

1. **No Support for Non-Standard Collections:**
   - Only supports `List`, `Set`, and `Map`.

2. **Immutable Collections:**
   - The generated collections are immutable, so they cannot be modified after the object is built.

3. **Cannot Use with Primitive Types:**
   - Fields must be of a collection type, not primitive arrays.

---

### **When to Use `@Singular`**

- When working with classes that have collection fields.
- When you want to simplify the process of adding elements to collections.
- When you need immutable collections in the final object.

---

### **Summary**

| **Feature**                  | **Description**                                                                                   |
|------------------------------|---------------------------------------------------------------------------------------------------|
| **Simplifies Collection Handling** | Allows adding elements one by one to `List`, `Set`, or `Map`.                                   |
| **Generated Methods**         | Adds methods to add single elements, clear the collection, and set the entire collection.         |
| **Immutable Collections**     | The final collection in the built object is immutable.                                           |
| **Supports Common Collections** | Works with `List`, `Set`, and `Map`.                                                            |
