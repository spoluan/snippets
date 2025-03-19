PostgreSQL provides a rich set of **date/time functions and expressions** that allow you to manipulate, format, and extract data from date and time values. Let's dive into **`EXTRACT`, `EPOCH`, `TO_CHAR`,** and other useful expressions with examples.

---

### **1. `EXTRACT`**

The `EXTRACT` function is used to retrieve specific parts (fields) of a date/time value, such as the year, month, day, hour, etc.

#### **Syntax**
```sql
EXTRACT(field FROM source)
```

- **`field`**: The part of the date/time you want to extract (e.g., `year`, `month`, `day`, `hour`, `minute`, `second`, etc.).
- **`source`**: The date/time value (can be a column, literal, or expression).

#### **Example: Extracting Components**
```sql
SELECT 
    EXTRACT(YEAR FROM CURRENT_DATE) AS year,
    EXTRACT(MONTH FROM CURRENT_DATE) AS month,
    EXTRACT(DAY FROM CURRENT_DATE) AS day;
```

**Output** (Assume today is `2025-03-19`):
| year | month | day |
|------|-------|-----|
| 2025 | 3     | 19  |

---

### **2. `EPOCH`**

The `EPOCH` field in `EXTRACT` returns the number of **seconds** since the Unix epoch (`1970-01-01 00:00:00 UTC`). This is useful for working with timestamps in a numeric format.

#### **Extracting Epoch**
```sql
SELECT EXTRACT(EPOCH FROM TIMESTAMP '2025-03-19 12:00:00') AS epoch_seconds;
```

**Output**:
| epoch_seconds |
|---------------|
| 1742616000    |

---

#### **Convert Epoch Seconds Back to a Timestamp**
You can convert an epoch value back to a timestamp using the `TO_TIMESTAMP` function:
```sql
SELECT TO_TIMESTAMP(1742616000) AS readable_time;
```

**Output**:
| readable_time          |
|------------------------|
| 2025-03-19 12:00:00+00 |

---

### **3. `TO_CHAR`**

The `TO_CHAR` function is used to format date/time values into a string based on a specified format.

#### **Syntax**
```sql
TO_CHAR(source, format)
```

- **`source`**: The date/time value to format.
- **`format`**: A string that specifies the output format.

---

#### **Common Format Patterns**
| **Pattern** | **Description**                 | **Example**         |
|-------------|---------------------------------|---------------------|
| `YYYY`      | 4-digit year                   | `2025`              |
| `YY`        | 2-digit year                   | `25`                |
| `MM`        | Month (01-12)                  | `03`                |
| `Mon`       | Abbreviated month name         | `Mar`               |
| `Month`     | Full month name                | `March`             |
| `DD`        | Day of the month (01-31)       | `19`                |
| `HH24`      | Hour (24-hour clock)           | `14`                |
| `HH12`      | Hour (12-hour clock)           | `02`                |
| `MI`        | Minutes                        | `45`                |
| `SS`        | Seconds                        | `30`                |
| `AM/PM`     | Meridian indicator             | `PM`                |
| `Day`       | Full day name                  | `Wednesday`         |

---

#### **Example: Formatting Dates**
```sql
SELECT 
    TO_CHAR(CURRENT_DATE, 'YYYY-MM-DD') AS formatted_date,
    TO_CHAR(CURRENT_DATE, 'Day, Month DD, YYYY') AS friendly_date;
```

**Output**:
| formatted_date | friendly_date           |
|----------------|-------------------------|
| 2025-03-19     | Wednesday, March 19, 2025 |

---

#### **Example: Formatting Time**
```sql
SELECT 
    TO_CHAR(CURRENT_TIMESTAMP, 'HH24:MI:SS') AS time_24_hour,
    TO_CHAR(CURRENT_TIMESTAMP, 'HH12:MI:SS AM') AS time_12_hour;
```

**Output**:
| time_24_hour | time_12_hour   |
|--------------|----------------|
| 16:03:00     | 04:03:00 PM    |

---

### **4. Other Useful Date/Time Expressions**

#### **4.1. `AGE`**
The `AGE` function calculates the difference between two dates in terms of years, months, and days.

**Example: Calculate Age**
```sql
SELECT AGE(TIMESTAMP '1990-03-01', TIMESTAMP '2025-03-19') AS age_difference;
```

**Output**:
| age_difference   |
|------------------|
| 35 years 18 days |

---

#### **4.2. `NOW()`**
The `NOW()` function returns the current date and time.

**Example**:
```sql
SELECT NOW() AS current_datetime;
```

**Output**:
| current_datetime        |
|-------------------------|
| 2025-03-19 16:03:00+00 |

---

#### **4.3. `CURRENT_DATE` and `CURRENT_TIME`**
- `CURRENT_DATE`: Returns the current date (without time).
- `CURRENT_TIME`: Returns the current time (without date).

**Example**:
```sql
SELECT CURRENT_DATE AS today, CURRENT_TIME AS current_time;
```

**Output**:
| today      | current_time   |
|------------|----------------|
| 2025-03-19 | 16:03:00+00    |

---

#### **4.4. `JUSTIFY_DAYS` / `JUSTIFY_HOURS`**
These functions adjust interval values to make them more human-readable.

**Example**:
```sql
SELECT JUSTIFY_DAYS(INTERVAL '40 days') AS adjusted_interval;
```

**Output**:
| adjusted_interval |
|-------------------|
| 1 mon 10 days     |

---

#### **4.5. `DATE_PART`**
Similar to `EXTRACT`, but returns the result as a numeric value.

**Example**:
```sql
SELECT DATE_PART('year', CURRENT_DATE) AS year_part;
```

**Output**:
| year_part |
|-----------|
| 2025      |

---

#### **4.6. `DATE_TRUNC`**
The `DATE_TRUNC` function truncates a date/time value to a specified level of precision (e.g., year, month, day, hour).

**Example: Truncate to Month**
```sql
SELECT DATE_TRUNC('month', CURRENT_TIMESTAMP) AS start_of_month;
```

**Output**:
| start_of_month        |
|-----------------------|
| 2025-03-01 00:00:00+00 |

---

#### **4.7. Arithmetic with Dates**
You can perform arithmetic operations on date/time values.

**Example: Add/Subtract Days**
```sql
SELECT 
    CURRENT_DATE + INTERVAL '7 days' AS next_week,
    CURRENT_DATE - INTERVAL '30 days' AS last_month;
```

**Output**:
| next_week  | last_month  |
|------------|-------------|
| 2025-03-26 | 2025-02-17  |

---

### **5. Combining Functions**

You can combine various date/time functions for more complex use cases.

**Example: Format a Future Date**
```sql
SELECT 
    TO_CHAR(CURRENT_DATE + INTERVAL '7 days', 'Day, Month DD, YYYY') AS next_week_formatted;
```

**Output**:
| next_week_formatted      |
|--------------------------|
| Wednesday, March 26, 2025 |

---

### **Summary**

- **`EXTRACT`**: Extracts specific parts of a date/time (e.g., year, month, day).
- **`EPOCH`**: Converts a timestamp to Unix epoch seconds.
- **`TO_CHAR`**: Formats date/time into a string using custom patterns.
- **`AGE`**: Calculates the difference between two dates.
- **`DATE_TRUNC`**: Truncates date/time to a specific level (e.g., month, day).
- **Arithmetic**: Add or subtract intervals to/from dates.

