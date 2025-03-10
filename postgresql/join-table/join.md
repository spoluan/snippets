

## PostgreSQL JOINs Guide

PostgreSQL supports several types of JOINs to combine rows from two or more tables based on a related column. Below are the types of JOINs with examples:

---

### **1. INNER JOIN**
- Combines rows from two tables where there is a match in the specified columns.

#### **Syntax:**
```sql
SELECT columns
FROM table1
INNER JOIN table2
ON table1.column = table2.column;
```

#### **Example:**
```sql
-- Tables:
-- employees: id | name | department_id
-- departments: id | department_name

SELECT employees.name, departments.department_name
FROM employees
INNER JOIN departments
ON employees.department_id = departments.id;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Bob        | IT              |

---

### **2. LEFT JOIN (or LEFT OUTER JOIN)**
- Returns all rows from the left table, and matching rows from the right table. If no match is found, NULL is returned for the right table.

#### **Syntax:**
```sql
SELECT columns
FROM table1
LEFT JOIN table2
ON table1.column = table2.column;
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
LEFT JOIN departments
ON employees.department_id = departments.id;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Bob        | IT              |
| Charlie    | NULL            |

---

### **3. RIGHT JOIN (or RIGHT OUTER JOIN)**
- Returns all rows from the right table, and matching rows from the left table. If no match is found, NULL is returned for the left table.

#### **Syntax:**
```sql
SELECT columns
FROM table1
RIGHT JOIN table2
ON table1.column = table2.column;
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
RIGHT JOIN departments
ON employees.department_id = departments.id;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Bob        | IT              |
| NULL       | Finance         |

---

### **4. FULL JOIN (or FULL OUTER JOIN)**
- Combines the result of both LEFT and RIGHT JOINs. Returns all rows when there is a match in either table. If no match is found, NULL is returned for the missing side.

#### **Syntax:**
```sql
SELECT columns
FROM table1
FULL JOIN table2
ON table1.column = table2.column;
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
FULL JOIN departments
ON employees.department_id = departments.id;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Bob        | IT              |
| Charlie    | NULL            |
| NULL       | Finance         |

---

### **5. CROSS JOIN**
- Produces a Cartesian product of the two tables, combining every row from the first table with every row from the second table.

#### **Syntax:**
```sql
SELECT columns
FROM table1
CROSS JOIN table2;
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
CROSS JOIN departments;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Alice      | IT              |
| Alice      | Finance         |
| Bob        | HR              |
| Bob        | IT              |
| Bob        | Finance         |

---

### **6. SELF JOIN**
- A table is joined with itself. Useful for hierarchical or recursive data.

#### **Syntax:**
```sql
SELECT a.column, b.column
FROM table a
INNER JOIN table b
ON a.column = b.column;
```

#### **Example:**
```sql
-- employees: id | name | manager_id
SELECT e1.name AS employee, e2.name AS manager
FROM employees e1
LEFT JOIN employees e2
ON e1.manager_id = e2.id;
```

#### **Result:**
| employee   | manager         |
|------------|-----------------|
| Alice      | Bob             |
| Bob        | NULL            |
| Charlie    | Alice           |

---

### **7. NATURAL JOIN**
- Automatically joins tables based on columns with the same names and compatible data types.

#### **Syntax:**
```sql
SELECT columns
FROM table1
NATURAL JOIN table2;
```

#### **Example:**
```sql
-- Tables:
-- employees: id | name | department_id
-- departments: department_id | department_name

SELECT *
FROM employees
NATURAL JOIN departments;
```

#### **Result:**
| id | name       | department_id | department_name |
|----|------------|---------------|-----------------|
| 1  | Alice      | 1             | HR              |
| 2  | Bob        | 2             | IT              |

---

### **8. USING Clause**
- Simplifies JOIN conditions when column names are the same in both tables.

#### **Syntax:**
```sql
SELECT columns
FROM table1
JOIN table2 USING (column_name);
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
JOIN departments USING (department_id);
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |
| Bob        | IT              |

---

### **9. JOIN with Multiple Conditions**
- JOINs can include multiple conditions using `AND` or `OR`.

#### **Syntax:**
```sql
SELECT columns
FROM table1
JOIN table2
ON table1.column1 = table2.column1 AND table1.column2 = table2.column2;
```

#### **Example:**
```sql
SELECT employees.name, departments.department_name
FROM employees
JOIN departments
ON employees.department_id = departments.id
AND departments.department_name = 'IT';
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Bob        | IT              |

---

### **10. JOIN with Subqueries**
- JOINs can include subqueries as one of the tables.

#### **Example:**
```sql
SELECT employees.name, subquery.department_name
FROM employees
JOIN (
    SELECT id, department_name
    FROM departments
    WHERE department_name LIKE 'H%'
) AS subquery
ON employees.department_id = subquery.id;
```

#### **Result:**
| name       | department_name |
|------------|-----------------|
| Alice      | HR              |

---

### **Sample Tables**
For all the examples above, assume the following tables:

#### **employees**
| id | name     | department_id | manager_id |
|----|----------|---------------|------------|
| 1  | Alice    | 1             | 2          |
| 2  | Bob      | 2             | NULL       |
| 3  | Charlie  | NULL          | 1          |

#### **departments**
| id | department_name |
|----|-----------------|
| 1  | HR              |
| 2  | IT              |
| 3  | Finance         |

