 
# **Liquibase Database Schema Management**
 
### **Prerequisites**
1. **Java**: Make sure you have Java installed (version 8 or higher).
2. **Liquibase**: Install Liquibase by downloading it from [Liquibase Downloads](https://www.liquibase.org/download).
3. **Database**: Set up a PostgreSQL (or any other supported database) instance.
4. **Changelog File**: A changelog file (e.g., `db-changelog.xml`) to define your schema changes.

### **Dependencies**
If you're using Liquibase in a Java project, ensure you include the necessary dependencies in your `pom.xml` (for Maven) or `build.gradle` (for Gradle):

#### Maven
```xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
    <version>4.23.0</version>
</dependency>
```

#### Gradle
```gradle
implementation 'org.liquibase:liquibase-core:4.23.0'
```

---

## **How Liquibase Works**

1. **Changelog File**:  
   Liquibase uses a changelog file (in XML, YAML, JSON, or SQL) to define database changes. Each change is grouped into a **change set**, which includes a unique `id`, `author`, and the actual database operation (e.g., creating a table).

   Example:
   ```xml
   <databaseChangeLog
       xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
           http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

       <changeSet id="1" author="user">
           <createTable tableName="example_table">
               <column name="id" type="int" autoIncrement="true">
                   <constraints primaryKey="true"/>
               </column>
               <column name="name" type="varchar(255)"/>
           </createTable>
       </changeSet>
   </databaseChangeLog>
   ```

2. **Tracking Changes**:  
   Liquibase tracks applied changes in the `DATABASECHANGELOG` table in your database. It ensures that each change set is applied only once.

3. **Running Updates**:  
   To apply the changes, run:
   ```bash
   liquibase update
   ```

---

## **Updating the Changelog File**

### **Manually Adding Changes**
You can manually add a new change set to the changelog file whenever you want to modify the schema. Each change set must have a unique `id` and `author`.

Example of adding a new column:
```xml
<changeSet id="2" author="user">
    <addColumn tableName="example_table">
        <column name="email" type="varchar(255)"/>
    </addColumn>
</changeSet>
```

### **Generating Changelog Automatically**
If you’ve made manual changes to the database schema, you can generate a changelog automatically:

1. **Generate Full Changelog**:  
   Capture the entire schema as a changelog file:
   ```bash
   liquibase generateChangeLog \
       --url="jdbc:postgresql://localhost:5432/your_database" \
       --username=your_user \
       --password=your_password \
       --outputFile=generated-changelog.xml
   ```

2. **Generate a Diff Changelog**:  
   Compare the current database schema with a reference schema and generate a changelog for the differences:
   ```bash
   liquibase diffChangeLog \
       --url="jdbc:postgresql://localhost:5432/your_database" \
       --username=your_user \
       --password=your_password \
       --referenceUrl="jdbc:postgresql://localhost:5432/reference_database" \
       --outputFile=diff-changelog.xml
   ```

---

## **Testing with Liquibase**

### **Resetting the Database**
To ensure a consistent state for each test, reset the database before running Liquibase. Options include:

1. **Drop and Recreate the Database**:
   ```java
   @BeforeEach
   void resetDatabase() throws Exception {
       dataSource.getConnection().createStatement().execute("DROP DATABASE IF EXISTS test_db");
       dataSource.getConnection().createStatement().execute("CREATE DATABASE test_db");

       try (Connection connection = dataSource.getConnection()) {
           Database database = DatabaseFactory.getInstance()
                   .findCorrectDatabaseImplementation(new JdbcConnection(connection));
           Liquibase liquibase = new Liquibase("db-changelog.xml",
                   new ClassLoaderResourceAccessor(),
                   database);
           liquibase.update("");
       }
   }
   ```

2. **Truncate Tables**:  
   Truncate all tables to remove test data without dropping the schema.

### **Using an In-Memory Database**
For faster tests, use an in-memory database like **H2**:
1. Add the H2 dependency:
   ```xml
   <dependency>
       <groupId>com.h2database</groupId>
       <artifactId>h2</artifactId>
       <version>2.1.214</version>
       <scope>test</scope>
   </dependency>
   ```
2. Configure your tests to use H2, and let Liquibase apply the schema.

---

## **Common Commands**

1. **Apply Changes**:
   ```bash
   liquibase update
   ```

2. **Rollback Changes**:
   Rollback the last change set:
   ```bash
   liquibase rollbackCount 1
   ```

3. **Check Pending Changes**:
   ```bash
   liquibase status
   ```

4. **Generate Changelog**:
   ```bash
   liquibase generateChangeLog
   ```

5. **Diff Changelog**:
   ```bash
   liquibase diffChangeLog
   ```

---

## **Best Practices**

1. **Always Use Version Control**:
   - Store your changelog files in version control (e.g., Git) to track schema changes over time.

2. **One Change Set per Logical Change**:
   - Each change set should represent a single, logical change (e.g., adding a table or column).

3. **Test Changes Locally**:
   - Test your changelog files in a local or staging environment before applying them to production.

4. **Use Contexts for Environment-Specific Changes**:
   - Use Liquibase contexts to apply specific changes only in certain environments (e.g., `dev`, `test`, `prod`).

   Example:
   ```xml
   <changeSet id="3" author="user" context="dev">
       <insert tableName="example_table">
           <column name="name" value="Test Data"/>
       </insert>
   </changeSet>
   ```

5. **Automate Database Reset for Tests**:
   - Use tools like in-memory databases or Docker-based test containers to ensure a clean database for every test.

---

## **Resources**

- [Liquibase Official Documentation](https://www.liquibase.org/documentation)
- [Liquibase GitHub Repository](https://github.com/liquibase/liquibase)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)

