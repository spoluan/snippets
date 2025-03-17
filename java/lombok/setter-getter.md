### **Getters and Setters in Lombok**

In Lombok, `@Getter` and `@Setter` annotations are used to automatically generate getter and setter methods for fields in a class. This eliminates the need to manually write boilerplate code, making your code cleaner and more maintainable.

---

### **How `@Getter` and `@Setter` Work**

1. **Field-Level Annotations**:
   - When `@Getter` or `@Setter` is applied to a specific field, Lombok generates getter or setter methods **only for that field**.
   
   Example:
   ```java
   import lombok.Getter;
   import lombok.Setter;

   public class Person {
       @Getter
       private String name;

       @Setter
       private int age;
   }
   ```
   **Generated Methods**:
   ```java
   public String getName() {
       return name;
   }

   public void setAge(int age) {
       this.age = age;
   }
   ```

2. **Class-Level Annotations**:
   - When `@Getter` or `@Setter` is applied to a class, Lombok generates getter or setter methods
