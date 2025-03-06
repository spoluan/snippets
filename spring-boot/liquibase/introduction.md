### **Introduction to Liquibase in Java Spring Boot**

**Liquibase** is a popular open-source database schema change management tool that helps manage, track, and apply database changes in a structured and version-controlled manner. It integrates seamlessly with Spring Boot, making it an ideal choice for managing database migrations in Java applications.

---

### **Why Use Liquibase?**

1. **Version Control for Database Changes:**  
   - Tracks and manages schema changes as part of your source code.
   
2. **Database Agnostic:**  
   - Supports multiple database systems (e.g., MySQL, PostgreSQL, Oracle, etc.).

3. **Easy Rollbacks:**  
   - Enables rolling back changes if something goes wrong.

4. **Integration with CI/CD Pipelines:**  
   - Automates database migrations as part of the deployment process.

5. **Multiple Formats for Change Logs:**  
   - Supports XML, YAML, JSON, and SQL formats for defining changes.

---

### **How Liquibase Works**

1. **Change Log File:**  
   - A file (`db.changelog.xml`, `db.changelog.yaml`, etc.) contains the database changes in a structured format.

2. **ChangeSet:**  
   - Each database change (e.g., creating a table, adding a column) is defined as a `ChangeSet` in the change log file.
   - Each `ChangeSet` has a unique identifier to ensure it is executed only once.

3. **Tracking Table:**  
   - Liquibase uses a special table (`DATABASECHANGELOG`) in the database to track which `ChangeSet` has been executed.

4. **Execution:**  
   - Liquibase reads the change log file, compares it with the `DATABASECHANGELOG` table, and applies only the new changes.

---

### **Setting Up Liquibase in Spring Boot**

#### **Step 1: Add Liquibase Dependency**
Add the Liquibase dependency to your `pom.xml` if you're using Maven:

```xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
    <version>4.23.0</version> <!-- Use the latest version -->
</dependency>
```

For Gradle, add this to your `build.gradle`:

```gradle
implementation 'org.liquibase:liquibase-core:4.23.0'
```

---

#### **Step 2: Configure Liquibase in `application.properties`**

Add the database connection details and Liquibase configurations:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=root

spring.liquibase.change-log=classpath:db/changelog/db.changelog-master.xml
```

- **`spring.liquibase.change-log`:** Points to the master change log file, which acts as an entry point for all database changes.

---

#### **Step 3: Create the Change Log File**

Create a directory structure like `src/main/resources/db/changelog/` and add a master change log file (`db.changelog-master.xml`):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <!-- Include other changelog files -->
    <include file="db.changelog-1.0.xml" relativeToChangelogFile="true"/>
    <include file="db.changelog-2.0.xml" relativeToChangelogFile="true"/>

</databaseChangeLog>
```

The `db.changelog-master.xml` acts as the main file that includes other change log files.

---

#### **Step 4: Create a Change Log File for Schema Changes**

Create `db.changelog-1.0.xml` to define database changes:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<databaseChangeLog
    xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-3.8.xsd">

    <changeSet id="1" author="user">
        <createTable tableName="users">
            <column name="id" type="BIGINT" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="username" type="VARCHAR(255)">
                <constraints nullable="false" unique="true"/>
            </column>
            <column name="email" type="VARCHAR(255)">
                <constraints nullable="false"/>
            </column>
            <column name="created_at" type="TIMESTAMP"/>
        </createTable>
    </changeSet>

    <changeSet id="2" author="user">
        <addColumn tableName="users">
            <column name="last_login" type="TIMESTAMP"/>
        </addColumn>
    </changeSet>

</databaseChangeLog>
```

---

### **Liquibase Change Log Formats**

You can write change logs in different formats:

1. **XML** (as shown above)
2. **YAML**
   ```yaml
   databaseChangeLog:
     - changeSet:
         id: 1
         author: user
         changes:
           - createTable:
               tableName: users
               columns:
                 - column:
                     name: id
                     type: BIGINT
                     autoIncrement: true
                     constraints:
                       primaryKey: true
                       nullable: false
                 - column:
                     name: username
                     type: VARCHAR(255)
                     constraints:
                       nullable: false
                       unique: true
   ```

3. **SQL**
   ```sql
   --liquibase formatted sql
   --changeset user:1
   CREATE TABLE users (
       id BIGINT AUTO_INCREMENT PRIMARY KEY,
       username VARCHAR(255) NOT NULL UNIQUE,
       email VARCHAR(255) NOT NULL,
       created_at TIMESTAMP
   );
   ```

---

### **Running Liquibase**

When you start your Spring Boot application, Liquibase automatically runs and applies the changes defined in the change log files.

---

### **Rollback in Liquibase**

You can define rollback instructions for each `ChangeSet` to revert the changes if needed.

```xml
<changeSet id="3" author="user">
    <createTable tableName="orders">
        <column name="id" type="BIGINT" autoIncrement="true">
            <constraints primaryKey="true" nullable="false"/>
        </column>
        <column name="user_id" type="BIGINT"/>
        <column name="total" type="DECIMAL(10,2)"/>
    </createTable>
    <rollback>
        <dropTable tableName="orders"/>
    </rollback>
</changeSet>
```

---

### **Advanced Features**

1. **Preconditions:**  
   Run `ChangeSet` only if certain conditions are met.
   ```xml
   <changeSet id="4" author="user">
       <preConditions onFail="MARK_RAN">
           <not>
               <tableExists tableName="products"/>
           </not>
       </preConditions>
       <createTable tableName="products">
           <column name="id" type="BIGINT" autoIncrement="true">
               <constraints primaryKey="true"/>
           </column>
           <column name="name" type="VARCHAR(255)"/>
       </createTable>
   </changeSet>
   ```

2. **Data Insertion:**
   Insert initial data into tables.
   ```xml
   <changeSet id="5" author="user">
       <insert tableName="users">
           <column name="id" value="1"/>
           <column name="username" value="admin"/>
           <column name="email" value="admin@example.com"/>
       </insert>
   </changeSet>
   ```

3. **Custom SQL Execution:**
   ```xml
   <changeSet id="6" author="user">
       <sql>ALTER TABLE users ADD COLUMN status VARCHAR(20);</sql>
   </changeSet>
   ```

---

### **Liquibase Commands**

You can use the Liquibase CLI to manage migrations:

- **Update Database:**  
  `liquibase update`
  
- **Rollback Changes:**  
  `liquibase rollbackCount 1` (rolls back the last change)

- **Generate Change Log:**  
  `liquibase generateChangeLog`

