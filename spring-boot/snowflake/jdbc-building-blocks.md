### **JDBC Building Blocks: Overview, Examples, and Scenarios**

---

### **What are JDBC Building Blocks?**

JDBC (Java Database Connectivity) is an API that provides Java applications with the ability to interact with databases. The building blocks of JDBC involve the essential steps and components required to connect, query, and manipulate databases programmatically. These steps can be applied to various use cases, such as migrating data from RDBMS to Snowflake, executing SQL queries, managing transactions, and more.

---

### **Key Steps in JDBC Building Blocks**

The process of working with JDBC can be broken down into the following steps:

#### **Step 0: Establish Connection**
- Establish a connection to the source database (e.g., PostgreSQL) and the target database (e.g., Snowflake).
- Use `DriverManager` or a `DataSource` to create a connection object.

#### **Step 1: Fetch All Table Names**
- Query the metadata of the source database to retrieve the list of all table names.
- Use SQL queries like `SELECT table_name FROM information_schema.tables`.

#### **Step 2: Build DDL Statements**
- Generate Data Definition Language (DDL) statements for creating tables in the target database.
- Extract column names and data types from the source database to replicate the schema in the target database.

#### **Step 3: Create Insert SQL Statements**
- Create SQL `INSERT` statements dynamically for inserting data into the target database.
- Use prepared statements to handle batch inserts efficiently.

#### **Step 4: Fetch Data in Memory**
- Retrieve data from the source database and store it in memory for processing.
- This step is not ideal for large datasets due to memory constraints, but it works for smaller datasets.

#### **Step 5: Execute DDL in Target Database**
- Execute the generated DDL statements in the target database (e.g., Snowflake) to create tables.

#### **Step 6: Load Data into Target Database**
- Insert the data into the target database using batch operations for efficiency.

---

### **Detailed Explanation of Each Step**

#### **Step 0: Establish Connection**
**Code Example:**
```java
public static Connection getSourceDBConnection() {
    String jdbcUrl = "jdbc:postgresql://localhost:5432/mydb";
    String user = "demo-user";
    String password = "password";
    Connection connection = null;
    try {
        Class.forName("org.postgresql.Driver");
        connection = DriverManager.getConnection(jdbcUrl, user, password);
    } catch (Exception e) {
        e.printStackTrace();
        System.exit(1);
    }
    return connection;
}

public static Connection getSnowflakeConnection() {
    String jdbcUrl = "jdbc:snowflake://<account>.snowflakecomputing.com/";
    Properties properties = new Properties();
    properties.put("user", "JDBC_Batch");
    properties.put("password", "password");
    properties.put("warehouse", "COMPUTE_WH");
    properties.put("db", "DATA_WAREHOUSE_DEV");
    properties.put("schema", "STAGE_SCHEMA");
    properties.put("role", "SYSADMIN");
    Connection connection = null;
    try {
        connection = DriverManager.getConnection(jdbcUrl, properties);
    } catch (SQLException e) {
        e.printStackTrace();
        System.exit(1);
    }
    return connection;
}
```

---

#### **Step 1: Fetch All Table Names**
**Code Example:**
```java
public static ArrayList<String> getTableNames(Connection srcDBConnection) {
    ArrayList<String> tableNames = new ArrayList<>();
    String sql = "SELECT table_name FROM information_schema.tables WHERE table_schema='public'";
    try (Statement stmt = srcDBConnection.createStatement();
         ResultSet rs = stmt.executeQuery(sql)) {
        while (rs.next()) {
            tableNames.add(rs.getString("table_name"));
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
    return tableNames;
}
```

---

#### **Step 2: Build DDL Statements**
**Code Example:**
```java
public static ArrayList<String> createDDLForSnowflake(Connection srcDBConnection, ArrayList<String> tableNames) {
    ArrayList<String> ddlStatements = new ArrayList<>();
    for (String table : tableNames) {
        String ddl = "CREATE OR REPLACE TABLE " + table + " (";
        String sql = "SELECT column_name, data_type FROM information_schema.columns " +
                     "WHERE table_name = '" + table + "'";
        try (Statement stmt = srcDBConnection.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            while (rs.next()) {
                ddl += rs.getString("column_name") + " " + rs.getString("data_type") + ",";
            }
            ddl = ddl.substring(0, ddl.length() - 1) + ")";
            ddlStatements.add(ddl);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    return ddlStatements;
}
```

---

#### **Step 3: Create Insert SQL Statements**
**Code Example:**
```java
public static ArrayList<String> createPreparedStmtForSnowflake(Connection srcDBConnection, ArrayList<String> tableNames) {
    ArrayList<String> insertStatements = new ArrayList<>();
    for (String table : tableNames) {
        String sql = "INSERT INTO " + table + " VALUES (?, ?)";
        insertStatements.add(sql);
    }
    return insertStatements;
}
```

---

#### **Step 4: Fetch Data in Memory**
**Code Example:**
```java
public static HashMap<String, ArrayList<ArrayList<String>>> getInsertData(Connection srcDBConnection, ArrayList<String> tableNames) {
    HashMap<String, ArrayList<ArrayList<String>>> dataMap = new HashMap<>();
    for (String table : tableNames) {
        ArrayList<ArrayList<String>> tableData = new ArrayList<>();
        String sql = "SELECT * FROM " + table;
        try (Statement stmt = srcDBConnection.createStatement();
             ResultSet rs = stmt.executeQuery(sql)) {
            while (rs.next()) {
                ArrayList<String> row = new ArrayList<>();
                row.add(rs.getString(1)); // Adjust column index as needed
                row.add(rs.getString(2)); // Adjust column index as needed
                tableData.add(row);
            }
            dataMap.put(table, tableData);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    return dataMap;
}
```

---

#### **Step 5: Execute DDL in Snowflake**
**Code Example:**
```java
public static void createTablesInSnowflake(Connection snowflakeConnection, ArrayList<String> ddlStatements) {
    try (Statement stmt = snowflakeConnection.createStatement()) {
        for (String ddl : ddlStatements) {
            stmt.executeUpdate(ddl);
        }
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

---

#### **Step 6: Load Data into Snowflake**
**Code Example:**
```java
public static void insertDataInSnowflake(Connection snowflakeConnection, ArrayList<String> tableNames,
                                         ArrayList<String> insertStatements,
                                         HashMap<String, ArrayList<ArrayList<String>>> dataMap) {
    try {
        snowflakeConnection.setAutoCommit(false);
        for (int i = 0; i < tableNames.size(); i++) {
            String tableName = tableNames.get(i);
            String insertStmt = insertStatements.get(i);
            ArrayList<ArrayList<String>> data = dataMap.get(tableName);
            try (PreparedStatement pstmt = snowflakeConnection.prepareStatement(insertStmt)) {
                for (ArrayList<String> row : data) {
                    pstmt.setString(1, row.get(0)); // Adjust column index as needed
                    pstmt.setString(2, row.get(1)); // Adjust column index as needed
                    pstmt.addBatch();
                }
                pstmt.executeBatch();
            }
        }
        snowflakeConnection.commit();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

---

### **Scenarios for JDBC Building Blocks**

1. **Data Migration Between Databases**:
   - Migrate data from PostgreSQL to Snowflake using the steps outlined above.
   - Handle schema replication and batch inserts.

2. **ETL (Extract, Transform, Load) Operations**:
   - Extract data from a source database, transform it in memory (if needed), and load it into Snowflake.

3. **Real-Time Data Synchronization**:
   - Use JDBC to periodically fetch changes from a source database and update the target database.

4. **Batch Processing**:
   - Insert large datasets into Snowflake using batch operations for better performance.

5. **Schema Comparison**:
   - Compare the schema of two databases by fetching metadata and identifying differences.

6. **Data Validation**:
   - Fetch data from both source and target databases to ensure data integrity after migration.

---

### **Best Practices**

1. **Use Connection Pooling**:
   - Use libraries like HikariCP for efficient connection management.

2. **Batch Inserts**:
   - Always use batch operations for large datasets to improve performance.

3. **Error Handling**:
   - Implement proper error handling and rollback mechanisms in case of failures.

4. **Memory Management**:
   - Avoid loading large datasets into memory; use streaming or pagination where possible.

5. **Logging**:
   - Log each step of the process for debugging and monitoring.
