The `<>` operator in PostgreSQL is a **comparison operator** that stands for **"not equal to"**. It is used to compare two values, and it evaluates to `TRUE` if the values are **not equal**.

---

### **Syntax**
```sql
value1 <> value2
```

- Returns `TRUE` if `value1` is not equal to `value2`.
- Returns `FALSE` if `value1` is equal to `value2`.
- Returns `NULL` if either value is `NULL` (because comparisons involving `NULL` return `NULL`).

---

### **Examples**

#### **1. Basic Comparison**
```sql
SELECT 5 <> 3 AS result;
```

**Output**:
| result |
|--------|
| TRUE   |

---

#### **2. Using `<>` in a `WHERE` Clause**
You can use `<>` to filter rows where a column's value is not equal to a specific value.

**Example**: Find all employees who are not in the "HR" department.
```sql
SELECT employee_id, name, department
FROM employees
WHERE department <> 'HR';
```

**Output**:
| employee_id | name      | department |
|-------------|-----------|------------|
| 2           | Alice     | IT         |
| 3           | Bob       | Sales      |

---

#### **3. Comparing Columns**
You can use `<>` to compare two columns in a table.

**Example**: Find rows where `start_date` is not equal to `end_date`.
```sql
SELECT project_id, start_date, end_date
FROM projects
WHERE start_date <> end_date;
```

---

#### **4. Handling `NULL` with `<>`**
When one or both values being compared are `NULL`, the result of `<>` is always `NULL` (not `TRUE` or `FALSE`). To explicitly check for inequality with `NULL`, you must use the `IS NOT NULL` clause.

**Example**:
```sql
SELECT 5 <> NULL AS result;
```

**Output**:
| result |
|--------|
| NULL   |

To handle `NULL` properly, use:
```sql
SELECT *
FROM employees
WHERE department IS NOT NULL AND department <> 'HR';
```

---

### **Difference Between `<>` and `!=`**
- In PostgreSQL, `<>` and `!=` are **functionally identical**. Both mean "not equal to."
- `<>` is the SQL standard, so it is preferred for portability across databases.

**Example**:
```sql
SELECT 5 <> 3 AS result1, 5 != 3 AS result2;
```

**Output**:
| result1 | result2 |
|---------|---------|
| TRUE    | TRUE    |

---

### **Combining `<>` with Other Conditions**
You can combine `<>` with logical operators like `AND`, `OR`, etc., to create more complex conditions.

**Example**: Find employees who are not in the "HR" department and have a salary greater than 50,000.
```sql
SELECT employee_id, name, department, salary
FROM employees
WHERE department <> 'HR' AND salary > 50000;
```

---

### **Summary**
- The `<>` operator checks for inequality between two values.
- It is equivalent to `!=` in PostgreSQL but is the SQL standard.
- If either value is `NULL`, the result is `NULL`.
