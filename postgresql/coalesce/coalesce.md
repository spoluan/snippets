In PostgreSQL, `COALESCE` is a **function** that returns the first non-NULL value from a list of expressions. It is commonly used to handle NULL values in queries.

### Syntax:
```sql
COALESCE(value1, value2, ..., value_n)
```

- The function evaluates the arguments in the order they are listed.
- It returns the first non-NULL value it encounters.
- If all the values in the list are NULL, it returns NULL.

### Example Usage:

#### 1. Handling NULL Values:
Suppose you have a table `users` with columns `first_name`, `nickname`, and `last_name`. You want to display the first non-NULL name for each user.

```sql
SELECT COALESCE(nickname, first_name, last_name) AS preferred_name
FROM users;
```
This query will return:
- `nickname` if it is not NULL,
- Otherwise `first_name` if it is not NULL,
- Otherwise `last_name`.

#### 2. Providing Default Values:
You can use `COALESCE` to provide a default value when a column contains NULL.

```sql
SELECT COALESCE(salary, 0) AS salary_with_default
FROM employees;
```
This ensures that if `salary` is NULL, it will return `0` instead.

#### 3. Combining with Other Functions:
You can use `COALESCE` with other functions to make queries dynamic.

```sql
SELECT id, COALESCE(updated_at, created_at) AS activity_date
FROM logs;
```
Here, if `updated_at` is NULL, it will fall back to `created_at`.

### Key Points:
- `COALESCE` is similar to a `CASE` statement but more concise.
- It can work with any data type, but all arguments must be of compatible types.
- It is very useful for handling NULLs in PostgreSQL queries.
