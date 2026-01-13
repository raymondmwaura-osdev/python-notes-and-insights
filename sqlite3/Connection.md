# SQLite3 Connection Object Reference

## Overview

A `Connection` object represents a connection to an SQLite database. It is created by calling `sqlite3.connect()` and serves as the main interface for interacting with the database. Through the connection, you can execute SQL statements, manage transactions, and configure database behavior.

---

## Creating a Connection

```python
import sqlite3
conn = sqlite3.connect('database.db')
```

Use `":memory:"` as the database name to create an in-memory database that exists only during the program's execution.

---

## Execution Methods

### `cursor(factory=Cursor)`

Creates and returns a cursor object for executing SQL statements.

**Parameters:**
- `factory` (callable): Optional custom factory for creating cursor objects. Default is the `Cursor` class.

**Returns:** A `Cursor` object.

**Purpose:** Cursors are the primary way to execute SQL statements and retrieve results. You typically create a cursor, execute statements through it, and fetch results.

**Example:**
```python
cursor = conn.cursor()
cursor.execute('SELECT * FROM users')
```

---

### `execute(sql, parameters=())`

Executes a single SQL statement directly on the connection without creating a cursor explicitly.

**Parameters:**
- `sql` (str): The SQL statement to execute.
- `parameters` (tuple or dict): Optional parameters to substitute into the SQL statement.

**Returns:** A `Cursor` object.

**Purpose:** This is a convenience method that creates a cursor, executes the statement, and returns the cursor. It is useful for quick, one-off queries.

**Example:**
```python
cursor = conn.execute('SELECT * FROM users WHERE id = ?', (1,))
row = cursor.fetchone()
```

---

### `executemany(sql, seq_of_parameters)`

Executes a single SQL statement multiple times with different parameter sets.

**Parameters:**
- `sql` (str): The SQL statement to execute.
- `seq_of_parameters` (iterable): A sequence of parameter tuples or dictionaries.

**Returns:** A `Cursor` object.

**Purpose:** This is efficient for batch operations like inserting multiple rows. It executes the same SQL statement once for each set of parameters.

**Example:**
```python
users = [('Alice', 25), ('Bob', 30), ('Charlie', 35)]
conn.executemany('INSERT INTO users (name, age) VALUES (?, ?)', users)
```

---

### `executescript(sql_script)`

Executes multiple SQL statements separated by semicolons.

**Parameters:**
- `sql_script` (str): A string containing one or more SQL statements.

**Returns:** A `Cursor` object.

**Purpose:** This method is useful for executing SQL scripts, such as creating multiple tables or running initialization commands. It issues a COMMIT before executing the script, so any pending transaction will be committed first.

**Important:** This method does not support parameter substitution. All SQL must be hardcoded in the script.

**Example:**
```python
script = """
CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT);
CREATE TABLE posts (id INTEGER PRIMARY KEY, user_id INTEGER);
INSERT INTO users (name) VALUES ('Alice');
"""
conn.executescript(script)
```

---

## Transaction Methods

### `commit()`

Commits the current transaction, saving all changes made since the last commit or rollback.

**Returns:** None.

**Purpose:** SQLite operates in transaction mode by default. Changes made with INSERT, UPDATE, or DELETE are not permanently saved until you call `commit()`. If the connection is closed without committing, all changes are lost.

**Example:**
```python
conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
conn.commit()  # Save the change
```

---

### `rollback()`

Rolls back the current transaction, discarding all changes made since the last commit.

**Returns:** None.

**Purpose:** This undoes all changes made in the current transaction. It is useful when an error occurs and you want to cancel the operations performed so far.

**Example:**
```python
try:
    conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
    # Something goes wrong
    raise ValueError("Error occurred")
except ValueError:
    conn.rollback()  # Undo the insert
```

---

## Connection Management

### `close()`

Closes the database connection immediately.

**Returns:** None.

**Purpose:** This method closes the connection and releases any resources associated with it. After closing, attempting to use the connection will raise an exception. It is good practice to close connections when you are done with them.

**Important:** Closing a connection does not automatically commit pending changes. Always call `commit()` before `close()` if you want to save your changes.

**Example:**
```python
conn.close()
```

---

### Context Manager Support (`with` statement)

Connection objects support the context manager protocol, allowing use with the `with` statement.

**Behavior:** When used with `with`, the connection automatically commits changes if no exception occurs, or rolls back if an exception is raised. The connection is **not** closed automatically when the block exits.

**Example:**
```python
with conn:
    conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
# Automatically commits if successful, rolls back if exception occurs
```

---

## Database Operations

### `backup(target, pages=-1, progress=None, name='main', sleep=0.250)`

Creates a backup of the database.

**Parameters:**
- `target` (Connection): The destination connection where the backup will be written.
- `pages` (int): Number of pages to copy at a time. Use -1 to copy the entire database at once.
- `progress` (callable): Optional callback function called after each batch of pages. It receives three arguments: status (always 0), remaining pages, and total pages.
- `name` (str): The name of the database to back up. Default is `'main'`. Use `'temp'` for temporary tables or the name of an attached database.
- `sleep` (float): Seconds to sleep between copying page batches. Default is 0.250.

**Returns:** None.

**Purpose:** This method copies the database to another connection, typically used for creating backup files or copying in-memory databases to disk.

**Example:**
```python
source = sqlite3.connect('source.db')
dest = sqlite3.connect('backup.db')
source.backup(dest)
dest.close()
source.close()
```

---

### `iterdump()`

Returns an iterator that yields SQL statements to recreate the database contents.

**Returns:** An iterator of SQL strings.

**Purpose:** This generates a complete SQL dump of the database, including table definitions and data. It is useful for creating backups in SQL format or migrating data.

**Example:**
```python
for line in conn.iterdump():
    print(line)
```

---

## Customization Methods

### `create_function(name, num_params, func, deterministic=False)`

Registers a custom Python function that can be called from SQL statements.

**Parameters:**
- `name` (str): The name of the function as it will appear in SQL.
- `num_params` (int): The number of parameters the function accepts. Use -1 to accept any number.
- `func` (callable): The Python function to call. It receives the specified number of arguments and must return a value compatible with SQLite (int, float, str, bytes, or None).
- `deterministic` (bool): Set to `True` if the function always returns the same output for the same input. This allows SQLite to optimize queries.

**Returns:** None.

**Purpose:** This allows you to extend SQLite with custom functionality written in Python.

**Example:**
```python
def multiply(a, b):
    return a * b

conn.create_function('multiply', 2, multiply)
cursor = conn.execute('SELECT multiply(3, 4)')
print(cursor.fetchone()[0])  # Output: 12
```

---

### `create_aggregate(name, num_params, aggregate_class)`

Registers a custom aggregate function for use in SQL.

**Parameters:**
- `name` (str): The name of the aggregate function as it will appear in SQL.
- `num_params` (int): The number of parameters the aggregate accepts.
- `aggregate_class` (class): A class with two methods: `step(value)` called for each row, and `finalize()` called to return the final result.

**Returns:** None.

**Purpose:** Aggregate functions process multiple rows and return a single result, like SUM or AVG. This method lets you create custom aggregates.

**Example:**
```python
class MySum:
    def __init__(self):
        self.total = 0
    
    def step(self, value):
        self.total += value
    
    def finalize(self):
        return self.total

conn.create_aggregate('mysum', 1, MySum)
cursor = conn.execute('SELECT mysum(age) FROM users')
print(cursor.fetchone()[0])
```

---

### `create_collation(name, callable)`

Registers a custom collation (string comparison) function.

**Parameters:**
- `name` (str): The name of the collation as it will appear in SQL.
- `callable` (function): A function that takes two string arguments and returns -1 if the first is less than the second, 0 if they are equal, or 1 if the first is greater.

**Returns:** None.

**Purpose:** Collations define how strings are compared and sorted. Custom collations allow locale-specific or custom sorting rules.

**Example:**
```python
def reverse_collation(s1, s2):
    # Sort in reverse alphabetical order
    if s1 < s2:
        return 1
    elif s1 > s2:
        return -1
    return 0

conn.create_collation('reverse', reverse_collation)
cursor = conn.execute('SELECT name FROM users ORDER BY name COLLATE reverse')
```

---

### `set_authorizer(authorizer_callback)`

Sets a callback function to authorize database operations before they are executed.

**Parameters:**
- `authorizer_callback` (callable or None): A function that receives operation codes and arguments, returning `sqlite3.SQLITE_OK` to allow, `sqlite3.SQLITE_DENY` to deny, or `sqlite3.SQLITE_IGNORE` to ignore. Pass `None` to remove the authorizer.

**Returns:** None.

**Purpose:** This provides fine-grained control over what SQL operations are permitted. It is useful for security, restricting access to certain tables or operations.

**Callback Signature:** `authorizer_callback(action, arg1, arg2, database_name, trigger_or_view_name)`

**Example:**
```python
def authorizer(action, arg1, arg2, database_name, trigger_or_view_name):
    if action == sqlite3.SQLITE_DELETE and arg1 == 'users':
        return sqlite3.SQLITE_DENY  # Prevent deleting from users table
    return sqlite3.SQLITE_OK

conn.set_authorizer(authorizer)
```

---

### `set_progress_handler(handler, n)`

Sets a callback function that is called periodically during long-running operations.

**Parameters:**
- `handler` (callable or None): A function called every `n` virtual machine instructions. If it returns non-zero, the operation is aborted. Pass `None` to remove the handler.
- `n` (int): The number of virtual machine instructions between each callback.

**Returns:** None.

**Purpose:** This is useful for implementing progress bars, timeouts, or allowing user cancellation of long operations.

**Example:**
```python
def progress():
    print("Operation in progress...")
    return 0  # Return 0 to continue, non-zero to abort

conn.set_progress_handler(progress, 1000)
```

---

### `set_trace_callback(trace_callback)`

Sets a callback function that is invoked for each SQL statement executed.

**Parameters:**
- `trace_callback` (callable or None): A function that receives the SQL statement as a string. Pass `None` to remove the callback.

**Returns:** None.

**Purpose:** This is useful for debugging, logging, or monitoring SQL statements executed by the application.

**Example:**
```python
def trace(statement):
    print(f"Executing: {statement}")

conn.set_trace_callback(trace)
conn.execute('SELECT * FROM users')
# Output: Executing: SELECT * FROM users
```

---

## Properties

### `isolation_level`

Gets or sets the transaction isolation level for the connection.

**Type:** str or None

**Values:**
- `"DEFERRED"`: Default. Transaction starts when the first statement is executed.
- `"IMMEDIATE"`: Transaction starts immediately with a reserved lock.
- `"EXCLUSIVE"`: Transaction starts immediately with an exclusive lock.
- `None`: Autocommit mode. Each statement is committed immediately.

**Purpose:** Controls how transactions are initiated and what locks are acquired. Setting this to `None` enables autocommit mode, where changes are saved automatically without calling `commit()`.

**Example:**
```python
conn.isolation_level = None  # Enable autocommit
conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
# Change is immediately saved
```

---

### `in_transaction`

A read-only property indicating whether a transaction is currently active.

**Type:** bool

**Returns:** `True` if a transaction is active, `False` otherwise.

**Purpose:** This helps you check transaction state, useful for conditional logic or debugging.

**Example:**
```python
print(conn.in_transaction)  # False
conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
print(conn.in_transaction)  # True
conn.commit()
print(conn.in_transaction)  # False
```

---

### `row_factory`

Gets or sets a callable that processes database rows before they are returned.

**Type:** callable or None

**Default:** None (rows returned as tuples)

**Purpose:** This allows you to customize how rows are represented. The most common use is setting it to `sqlite3.Row` to access columns by name instead of index.

**Common Values:**
- `None`: Default. Rows are tuples.
- `sqlite3.Row`: Rows are Row objects with dictionary-like access.
- Custom function: Takes a cursor and tuple, returns any object.

**Example:**
```python
conn.row_factory = sqlite3.Row
cursor = conn.execute('SELECT id, name FROM users')
row = cursor.fetchone()
print(row['name'])  # Access by column name
print(row[1])       # Also access by index
```

---

### `text_factory`

Gets or sets a callable that processes TEXT data retrieved from the database.

**Type:** callable

**Default:** `str`

**Purpose:** Controls how TEXT columns are converted from bytes to Python objects. The default converts bytes to strings using UTF-8 encoding.

**Common Values:**
- `str`: Default. Decodes bytes to strings.
- `bytes`: Returns raw bytes without decoding.
- `lambda x: x.decode('latin1')`: Custom decoding with different encoding.

**Example:**
```python
conn.text_factory = bytes
cursor = conn.execute('SELECT name FROM users')
row = cursor.fetchone()
print(type(row[0]))  # <class 'bytes'>
```

---

### `total_changes`

A read-only property returning the total number of rows modified, inserted, or deleted since the connection was opened.

**Type:** int

**Returns:** Total number of changes across all transactions.

**Purpose:** This provides a running count of all database modifications made through this connection.

**Example:**
```python
conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
conn.commit()
print(conn.total_changes)  # 1
conn.execute('DELETE FROM users')
conn.commit()
print(conn.total_changes)  # 2 (1 insert + 1 delete)
```

---

## Best Practices

### 1. Always Use Parameter Substitution

Never construct SQL with string formatting or concatenation. Use parameter substitution to prevent SQL injection.

```python
# WRONG - Vulnerable to SQL injection
name = "'; DROP TABLE users; --"
conn.execute(f"SELECT * FROM users WHERE name = '{name}'")

# CORRECT
conn.execute('SELECT * FROM users WHERE name = ?', (name,))
```

---

### 2. Commit Your Changes

Remember to call `commit()` after making changes, unless you're using autocommit mode.

```python
conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
conn.commit()  # Don't forget this!
```

---

### 3. Use Context Managers

Use the `with` statement to automatically commit or rollback transactions.

```python
with conn:
    conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
# Automatically commits on success, rolls back on exception
```

---

### 4. Close Connections

Always close connections when done, especially in long-running applications.

```python
try:
    conn = sqlite3.connect('database.db')
    # Do work...
finally:
    conn.close()
```

---

### 5. Handle Exceptions

Wrap database operations in try-except blocks to handle errors gracefully.

```python
try:
    conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
    conn.commit()
except sqlite3.IntegrityError:
    print("User already exists")
    conn.rollback()
```

---

## Common Patterns

### Pattern 1: Basic CRUD Operations

```python
# Create
conn.execute('INSERT INTO users (name, email) VALUES (?, ?)', ('Alice', 'alice@example.com'))
conn.commit()

# Read
cursor = conn.execute('SELECT * FROM users WHERE name = ?', ('Alice',))
user = cursor.fetchone()

# Update
conn.execute('UPDATE users SET email = ? WHERE name = ?', ('newemail@example.com', 'Alice'))
conn.commit()

# Delete
conn.execute('DELETE FROM users WHERE name = ?', ('Alice',))
conn.commit()
```

---

### Pattern 2: Transaction with Error Handling

```python
try:
    with conn:
        conn.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))
        conn.execute('INSERT INTO posts (user_id, content) VALUES (?, ?)', (1, 'Hello'))
except sqlite3.Error as e:
    print(f"Error: {e}")
    # Transaction automatically rolled back
```

---

### Pattern 3: Batch Insert

```python
users = [
    ('Alice', 'alice@example.com'),
    ('Bob', 'bob@example.com'),
    ('Charlie', 'charlie@example.com')
]
conn.executemany('INSERT INTO users (name, email) VALUES (?, ?)', users)
conn.commit()
```

---

### Pattern 4: Using Row Factory for Named Access

```python
conn.row_factory = sqlite3.Row
cursor = conn.execute('SELECT id, name, email FROM users')
for row in cursor:
    print(f"User {row['name']} has email {row['email']}")
```

---
