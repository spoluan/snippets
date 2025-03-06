### **Collection Configuration Properties**

In Spring Boot, you can use **collections** such as `List`, `Set`, and `Map` in `@ConfigurationProperties` classes to bind groups of related configuration values. This is particularly useful when you need to manage multiple values for a property or a key-value structure.

The provided image demonstrates how to define and use **collection-based configuration properties** in a `@ConfigurationProperties` class.

---

### **Code Explanation**

Here's an example based on the image:

```java
@ConfigurationProperties("application.database")
public class DatabaseProperties {

    private String password;
    private String url;
    private String database;

    private List<String> whitelistTables; // List of table names
    private Map<String, Integer> maxTablesSize; // Map of table name to max size

    // Getters and Setters
    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getDatabase() {
        return database;
    }

    public void setDatabase(String database) {
        this.database = database;
    }

    public List<String> getWhitelistTables() {
        return whitelistTables;
    }

    public void setWhitelistTables(List<String> whitelistTables) {
        this.whitelistTables = whitelistTables;
    }

    public Map<String, Integer> getMaxTablesSize() {
        return maxTablesSize;
    }

    public void setMaxTablesSize(Map<String, Integer> maxTablesSize) {
        this.maxTablesSize = maxTablesSize;
    }
}
```

---

### **Binding Properties in `application.yml` or `application.properties`**

#### **Using `application.yml`**
```yaml
application:
  database:
    password: mypassword
    url: jdbc:mysql://localhost:3306/mydb
    database: mydatabase
    whitelist-tables:
      - users
      - orders
      - products
    max-tables-size:
      users: 1000
      orders: 5000
      products: 2000
```

#### **Using `application.properties`**
```properties
application.database.password=mypassword
application.database.url=jdbc:mysql://localhost:3306/mydb
application.database.database=mydatabase
application.database.whitelist-tables[0]=users
application.database.whitelist-tables[1]=orders
application.database.whitelist-tables[2]=products
application.database.max-tables-size.users=1000
application.database.max-tables-size.orders=5000
application.database.max-tables-size.products=2000
```

---

### **How Spring Boot Binds Collections**

1. **`List<String>` Binding:**  
   - The `whitelistTables` field is bound to a list of strings defined under `whitelist-tables`.  
   - In YAML, you define a list with a `-` prefix.  
   - In properties files, you use indexed keys like `[0]`, `[1]`, etc.

2. **`Map<String, Integer>` Binding:**  
   - The `maxTablesSize` field is bound to a map where the key is a string (e.g., table name) and the value is an integer (e.g., max size).  
   - In YAML, this is defined as key-value pairs under `max-tables-size`.  
   - In properties files, you use dot notation for keys, e.g., `max-tables-size.users`.

---

### **Usage Example**

Once the properties are bound, you can inject and use the `DatabaseProperties` class in your components:

```java
@RestController
public class DatabaseController {

    private final DatabaseProperties databaseProperties;

    public DatabaseController(DatabaseProperties databaseProperties) {
        this.databaseProperties = databaseProperties;
    }

    @GetMapping("/database/config")
    public String getDatabaseConfig() {
        return "Database: " + databaseProperties.getDatabase() +
               ", URL: " + databaseProperties.getUrl() +
               ", Whitelist Tables: " + databaseProperties.getWhitelistTables() +
               ", Max Table Sizes: " + databaseProperties.getMaxTablesSize();
    }
}
```

---

### **Advantages of Using Collections in Configuration Properties**

1. **Flexible Configuration:**  
   - Easily manage lists or key-value pairs in your configuration files.

2. **Type Safety:**  
   - Spring Boot ensures that the values are correctly bound to the specified types (`List<String>`, `Map<String, Integer>`, etc.).

3. **Scalability:**  
   - Add or remove items from the list or map without modifying the code.

4. **Improved Readability:**  
   - Group related configuration values logically in the YAML or properties file.
