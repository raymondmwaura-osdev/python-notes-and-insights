# SQLite3 Cursor Object Methods

## Introduction

A cursor object represents a database cursor used to execute SQL statements and manage the context of fetch operations. Cursors are created using the cursor() method of the connection object. Cursor objects are iterators, meaning that if you execute a SELECT query, you can simply iterate over the cursor to fetch the resulting rows.

---

## Creating a Cursor

To create a cursor object, call the `cursor()` method on a connection object.

**Syntax:**
```python
cursor = connection.cursor()
```

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()
```

---

## Cursor Methods

### execute()

Executes a single SQL statement, optionally binding Python values using placeholders.

**Syntax:**
```python
cursor.execute(sql, parameters=())
```

**Parameters:**
- `sql` (str): A single SQL statement to execute.
- `parameters` (dict or sequence): Python values to bind to placeholders in the SQL statement. Use a dict for named placeholders and a sequence for unnamed placeholders.

**Returns:**
The cursor object itself.

**Raises:**
- `ProgrammingError`: When the SQL statement contains more than one SQL statement, or when named placeholders are used with a sequence instead of a dict.

**Important Notes:**
If autocommit is LEGACY_TRANSACTION_CONTROL, isolation_level is not None, the SQL statement is an INSERT, UPDATE, DELETE, or REPLACE statement, and there is no open transaction, a transaction is implicitly opened before executing the SQL statement.

**Examples:**

Using unnamed placeholders (qmark style):
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

# Create table
cursor.execute("CREATE TABLE IF NOT EXISTS users (id INTEGER, name TEXT, age INTEGER)")

# Insert data with unnamed placeholders
cursor.execute("INSERT INTO users VALUES (?, ?, ?)", (1, "Alice", 30))
connection.commit()
```

Using named placeholders:
```python
cursor.execute(
    "INSERT INTO users VALUES (:id, :name, :age)",
    {"id": 2, "name": "Bob", "age": 25}
)
connection.commit()
```

Selecting data:
```python
cursor.execute("SELECT * FROM users WHERE age > ?", (20,))
results = cursor.fetchall()
print(results)
```

---

### executemany()

For every item in parameters, repeatedly executes the parameterized DML SQL statement.

**Syntax:**
```python
cursor.executemany(sql, parameters)
```

**Parameters:**
- `sql` (str): A single SQL DML statement (INSERT, UPDATE, DELETE).
- `parameters` (iterable): An iterable of parameters to bind with the placeholders in the SQL statement.

**Returns:**
The cursor object itself.

**Raises:**
- `ProgrammingError`: When the SQL statement contains more than one SQL statement, is not a DML statement, or when named placeholders are used with sequences instead of dicts.

**Important Notes:**
Any resulting rows are discarded, including DML statements with RETURNING clauses.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("CREATE TABLE IF NOT EXISTS products (id INTEGER, name TEXT, price REAL)")

# Insert multiple rows at once
products = [
    (1, "Laptop", 999.99),
    (2, "Mouse", 29.99),
    (3, "Keyboard", 79.99),
]

cursor.executemany("INSERT INTO products VALUES (?, ?, ?)", products)
connection.commit()
```

---

### executescript()

Executes the SQL statements in sql_script. If the autocommit is LEGACY_TRANSACTION_CONTROL and there is a pending transaction, an implicit COMMIT statement is executed first. No other implicit transaction control is performed; any transaction control must be added to sql_script.

**Syntax:**
```python
cursor.executescript(sql_script)
```

**Parameters:**
- `sql_script` (str): A string containing one or more SQL statements separated by semicolons.

**Returns:**
The cursor object itself.

**Important Notes:**
- The sql_script parameter must be a string.
- This method issues a COMMIT before executing the script.
- Transaction control must be explicitly included in the script.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.executescript("""
    BEGIN;
    CREATE TABLE IF NOT EXISTS employees (id INTEGER PRIMARY KEY, name TEXT, department TEXT);
    INSERT INTO employees (name, department) VALUES ('John', 'Engineering');
    INSERT INTO employees (name, department) VALUES ('Sarah', 'Marketing');
    COMMIT;
""")
```

---

### fetchone()

If row_factory is None, returns the next row query result set as a tuple. Else, passes it to the row factory and returns its result. Returns None if no more data is available.

**Syntax:**
```python
row = cursor.fetchone()
```

**Returns:**
- A tuple (or custom object if row_factory is set) representing the next row.
- `None` if no more rows are available.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("SELECT * FROM users WHERE age > ?", (25,))

row = cursor.fetchone()
if row:
    print(f"First result: {row}")

# Fetch next row
row = cursor.fetchone()
if row:
    print(f"Second result: {row}")
```

---

### fetchmany()

Returns the next set of rows of a query result as a list. Returns an empty list if no more rows are available.

**Syntax:**
```python
rows = cursor.fetchmany(size=cursor.arraysize)
```

**Parameters:**
- `size` (int, optional): The number of rows to fetch. If not specified, the `arraysize` attribute determines the number of rows to fetch.

**Returns:**
A list of rows (tuples or custom objects).

**Important Notes:**
If fewer than size rows are available, as many rows as are available are returned. The size parameter affects performance. For optimal performance, use the arraysize attribute consistently.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("SELECT * FROM users")

# Fetch 2 rows at a time
rows = cursor.fetchmany(2)
print(f"First batch: {rows}")

rows = cursor.fetchmany(2)
print(f"Second batch: {rows}")
```

---

### fetchall()

Returns all remaining rows of a query result as a list. Returns an empty list if no rows are available.

**Syntax:**
```python
rows = cursor.fetchall()
```

**Returns:**
A list of all remaining rows (tuples or custom objects).

**Important Notes:**
The arraysize attribute can affect the performance of this operation. Use fetchall() only when the dataset is small enough to fit in memory.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("SELECT * FROM users")
all_rows = cursor.fetchall()

for row in all_rows:
    print(row)
```

---

### close()

Closes the cursor. The cursor will be unusable from this point forward; a ProgrammingError exception will be raised if any operation is attempted with the cursor.

**Syntax:**
```python
cursor.close()
```

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("SELECT * FROM users")
results = cursor.fetchall()

cursor.close()  # Close the cursor when done
```

---

### setinputsizes()

Required by the DB-API. Does nothing in sqlite3.

**Syntax:**
```python
cursor.setinputsizes(sizes)
```

**Note:**
This method exists for DB-API 2.0 compliance but has no effect in sqlite3.

---

### setoutputsize()

Required by the DB-API. Does nothing in sqlite3.

**Syntax:**
```python
cursor.setoutputsize(size, column=None)
```

**Note:**
This method exists for DB-API 2.0 compliance but has no effect in sqlite3.

---

## Cursor Attributes

### arraysize

Read/write attribute that controls the number of rows returned by fetchmany(). The default value is 1 which means a single row would be fetched per call.

**Type:** int

**Example:**
```python
cursor.arraysize = 5  # Set fetchmany() to return 5 rows by default
rows = cursor.fetchmany()  # Will fetch 5 rows
```

---

### connection

Read-only attribute that provides the SQLite database Connection belonging to the cursor.

**Type:** Connection object

**Example:**
```python
cursor = connection.cursor()
print(cursor.connection == connection)  # True
```

---

### description

Read-only attribute that provides the column names of the last query. To remain compatible with the Python DB API, it returns a 7-tuple for each column where the last six items of each tuple are None. It is set for SELECT statements without any matching rows as well.

**Type:** List of 7-tuples or None

**Example:**
```python
cursor.execute("SELECT name, age FROM users")
print(cursor.description)
# Output: [('name', None, None, None, None, None, None), ('age', None, None, None, None, None, None)]

# Extract column names
column_names = [desc[0] for desc in cursor.description]
print(column_names)  # ['name', 'age']
```

---

### lastrowid

Read-only attribute that provides the row id of the last inserted row. It is only updated after successful INSERT or REPLACE statements using the execute() method. For other statements, after executemany() or executescript(), or if the insertion failed, the value of lastrowid is left unchanged.

**Type:** int or None

**Example:**
```python
cursor.execute("INSERT INTO users VALUES (?, ?, ?)", (3, "Charlie", 35))
print(f"Last inserted row ID: {cursor.lastrowid}")
```

---

### rowcount

Read-only attribute that provides the number of modified rows for INSERT, UPDATE, DELETE, and REPLACE statements; is -1 for other statements, including CTE queries. It is only updated by the execute() and executemany() methods, after the statement has run to completion.

**Type:** int

**Example:**
```python
cursor.execute("UPDATE users SET age = age + 1 WHERE age < 30")
connection.commit()
print(f"Rows updated: {cursor.rowcount}")
```

---

### row_factory

Controls how a row fetched from this Cursor is represented. If None, a row is represented as a tuple. Can be set to sqlite3.Row or a callable that accepts two arguments: a Cursor object and the tuple of row values, and returns a custom object representing an SQLite row.

**Type:** callable or None

**Example:**
```python
import sqlite3

def dict_factory(cursor, row):
    fields = [column[0] for column in cursor.description]
    return {key: value for key, value in zip(fields, row)}

connection = sqlite3.connect('example.db')
cursor = connection.cursor()
cursor.row_factory = dict_factory

cursor.execute("SELECT * FROM users")
for row in cursor.fetchall():
    print(row)  # Each row is now a dictionary
```

---

## Iterating Over a Cursor

Cursor objects are iterators, which means you can iterate directly over them to fetch results.

**Example:**
```python
import sqlite3

connection = sqlite3.connect('example.db')
cursor = connection.cursor()

cursor.execute("SELECT name, age FROM users")

for row in cursor:
    print(f"Name: {row[0]}, Age: {row[1]}")
```

---

## Best Practices

1. **Always use placeholders**: Use `?` or named placeholders to prevent SQL injection attacks. Never use string formatting to construct SQL queries.

2. **Close cursors when done**: Although Python will eventually close cursors through garbage collection, explicitly closing them ensures resources are freed promptly.

3. **Use context managers**: Combine cursors with the `with` statement for automatic cleanup.

```python
with connection:
    cursor = connection.cursor()
    cursor.execute("SELECT * FROM users")
    results = cursor.fetchall()
    # Cursor automatically managed within this block
```

4. **Choose appropriate fetch methods**:
   - Use `fetchone()` when retrieving a single result.
   - Use `fetchmany()` for processing large result sets in batches.
   - Use `fetchall()` only when the entire result set fits comfortably in memory.

5. **Set arraysize appropriately**: For large result sets processed with `fetchmany()`, adjust the `arraysize` attribute to optimize performance.

6. **Handle exceptions**: Always wrap database operations in try-except blocks to handle potential errors gracefully.

```python
try:
    cursor.execute("SELECT * FROM non_existent_table")
except sqlite3.OperationalError as e:
    print(f"Database error: {e}")
```

---

## Complete Working Example

```python
import sqlite3

# Connect to database
connection = sqlite3.connect('example.db')
cursor = connection.cursor()

try:
    # Create table
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS employees (
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            department TEXT,
            salary REAL
        )
    """)
    
    # Insert single record
    cursor.execute(
        "INSERT INTO employees (name, department, salary) VALUES (?, ?, ?)",
        ("Alice Johnson", "Engineering", 85000)
    )
    
    # Insert multiple records
    employees = [
        ("Bob Smith", "Marketing", 65000),
        ("Charlie Brown", "Engineering", 90000),
        ("Diana Prince", "HR", 70000),
    ]
    cursor.executemany(
        "INSERT INTO employees (name, department, salary) VALUES (?, ?, ?)",
        employees
    )
    
    # Commit changes
    connection.commit()
    
    # Query with parameters
    cursor.execute("SELECT * FROM employees WHERE salary > ?", (70000,))
    
    # Fetch and display results
    print("Employees with salary > 70000:")
    for row in cursor:
        print(f"ID: {row[0]}, Name: {row[1]}, Department: {row[2]}, Salary: {row[3]}")
    
    # Get row count for UPDATE
    cursor.execute("UPDATE employees SET salary = salary * 1.1 WHERE department = ?", ("Engineering",))
    connection.commit()
    print(f"\nUpdated {cursor.rowcount} employee salaries in Engineering")
    
    # Use fetchone()
    cursor.execute("SELECT COUNT(*) FROM employees")
    total = cursor.fetchone()[0]
    print(f"\nTotal employees: {total}")
    
except sqlite3.Error as e:
    print(f"Database error: {e}")
    connection.rollback()
finally:
    cursor.close()
    connection.close()
```

---
