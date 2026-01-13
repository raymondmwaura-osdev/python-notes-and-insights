# SQLite3 Module Reference Guide

## Overview

The `sqlite3` module provides an interface for interacting with SQLite databases in Python. SQLite is a lightweight, file-based database that requires no separate server process. This document covers the primary methods and properties available in the `sqlite3` module.

---

## Module-Level Functions

### `sqlite3.connect(database, timeout=5.0, detect_types=0, isolation_level='DEFERRED', check_same_thread=True, factory=Connection, cached_statements=128, uri=False)`

Creates and returns a connection to an SQLite database.

**Parameters:**
- `database` (str): The path to the database file. Use `":memory:"` to create a database in RAM instead of on disk.
- `timeout` (float): How many seconds the connection should wait when the database is locked by another connection before raising an exception. Default is 5.0 seconds.
- `detect_types` (int): Controls type detection. Set to `sqlite3.PARSE_DECLTYPES` to parse declared column types, or `sqlite3.PARSE_COLNAMES` to parse column names for type hints.
- `isolation_level` (str): Controls transaction behavior. Can be `"DEFERRED"`, `"IMMEDIATE"`, `"EXCLUSIVE"`, or `None` for autocommit mode.
- `check_same_thread` (bool): If `True`, only the creating thread may use the connection. Set to `False` to allow multiple threads to share the connection (use with caution).
- `factory` (callable): A custom factory function to create connection objects. Default is the `Connection` class.
- `cached_statements` (int): The number of statements to cache for this connection. Default is 128.
- `uri` (bool): If `True`, interprets the database parameter as a URI. This enables advanced features like shared cache mode.

**Returns:** A `Connection` object.

**Example:**
```python
import sqlite3
conn = sqlite3.connect('example.db')
```

---

### `sqlite3.complete_statement(sql)`

Checks whether a string contains a complete SQL statement.

**Parameters:**
- `sql` (str): The SQL string to check.

**Returns:** `True` if the statement is complete and properly terminated (ends with a semicolon), `False` otherwise.

**Purpose:** This function is useful when building interactive SQL tools or when parsing user input. A complete statement is one that ends with a semicolon and is syntactically complete.

**Example:**
```python
sqlite3.complete_statement("SELECT * FROM users;")  # Returns True
sqlite3.complete_statement("SELECT * FROM users")   # Returns False
```

---

### `sqlite3.enable_callback_tracebacks(flag)`

Enables or disables callback tracebacks for debugging.

**Parameters:**
- `flag` (bool): Set to `True` to enable traceback printing for user-defined functions, aggregates, converters, and authorizers.

**Purpose:** By default, exceptions raised in callbacks are printed to `sys.stderr` but not propagated. Enabling this helps debug custom functions by showing full tracebacks.

**Example:**
```python
sqlite3.enable_callback_tracebacks(True)
```

---

### `sqlite3.register_adapter(type, callable)`

Registers a callable to convert custom Python types to SQLite-compatible types.

**Parameters:**
- `type` (type): The Python type to convert.
- `callable` (function): A function that takes one argument (the Python object) and returns a value that SQLite can store (int, float, str, bytes, or None).

**Purpose:** This allows you to store custom Python objects in SQLite by defining how they should be converted to basic types.

**Example:**
```python
import datetime

def adapt_date(date_obj):
    return date_obj.isoformat()

sqlite3.register_adapter(datetime.date, adapt_date)
```

---

### `sqlite3.register_converter(typename, callable)`

Registers a callable to convert SQLite types back to custom Python types.

**Parameters:**
- `typename` (str): The name of the SQLite type to convert (as declared in the column definition).
- `callable` (function): A function that takes one argument (a bytes object) and returns a Python object.

**Purpose:** This is the reverse of `register_adapter`. It converts data from SQLite back into your custom Python types when reading from the database.

**Example:**
```python
def convert_date(value):
    return datetime.date.fromisoformat(value.decode())

sqlite3.register_converter("date", convert_date)
```

---

## Module-Level Constants

### `sqlite3.PARSE_DECLTYPES`

A constant used with the `detect_types` parameter of `connect()`. When set, the module parses the declared type of each column and uses registered converters to convert values to Python types.

**Example:**
```python
conn = sqlite3.connect('example.db', detect_types=sqlite3.PARSE_DECLTYPES)
```

---

### `sqlite3.PARSE_COLNAMES`

A constant used with the `detect_types` parameter of `connect()`. When set, the module parses column names for type information in the format `name [type]` and uses registered converters.

**Example:**
```python
conn = sqlite3.connect('example.db', detect_types=sqlite3.PARSE_COLNAMES)
```

---

### `sqlite3.SQLITE_OK`, `sqlite3.SQLITE_DENY`, `sqlite3.SQLITE_IGNORE`

Constants returned by authorizer callbacks to control database operations:
- `SQLITE_OK`: Allow the operation.
- `SQLITE_DENY`: Deny the operation and raise an exception.
- `SQLITE_IGNORE`: Ignore the operation (treat the column or table as non-existent).

**Purpose:** These are used with the `set_authorizer()` method to control which SQL operations are permitted.

---

### `sqlite3.version`

A string containing the version number of the `sqlite3` module.

**Example:**
```python
print(sqlite3.version)  # Output: '2.6.0' (example)
```

---

### `sqlite3.version_info`

A tuple containing the version number of the `sqlite3` module as separate integers.

**Example:**
```python
print(sqlite3.version_info)  # Output: (2, 6, 0) (example)
```

---

### `sqlite3.sqlite_version`

A string containing the version of the SQLite library being used.

**Example:**
```python
print(sqlite3.sqlite_version)  # Output: '3.39.4' (example)
```

---

### `sqlite3.sqlite_version_info`

A tuple containing the version of the SQLite library as separate integers.

**Example:**
```python
print(sqlite3.sqlite_version_info)  # Output: (3, 39, 4) (example)
```

---

## Exception Classes

The `sqlite3` module defines several exception classes that inherit from `sqlite3.Error`, which itself inherits from Python's `Exception` class.

### `sqlite3.Error`

Base exception class for all other exceptions in the module. Catch this to handle any SQLite-related error.

---

### `sqlite3.InterfaceError`

Raised for errors related to the database interface rather than the database itself. This typically indicates a programming error, such as passing incorrect parameter types.

---

### `sqlite3.DatabaseError`

Raised for errors related to the database. This is the base class for most database-related exceptions.

---

### `sqlite3.DataError`

Raised for errors related to processed data, such as numeric value out of range or string too long.

---

### `sqlite3.OperationalError`

Raised for errors related to the database's operation, such as unexpected disconnect, database not found, or transaction failed.

---

### `sqlite3.IntegrityError`

Raised when database integrity is violated, such as foreign key constraint failures or unique constraint violations.

---

### `sqlite3.InternalError`

Raised when SQLite encounters an internal error. This typically indicates a bug in SQLite itself.

---

### `sqlite3.ProgrammingError`

Raised for programming errors, such as table not found or attempting to operate on a closed connection.

---

### `sqlite3.NotSupportedError`

Raised when attempting to use a feature not supported by SQLite.

---

## Best Practices

1. **Always close connections:** Use context managers (`with` statement) or explicitly call `close()` on connections when done.

2. **Use parameterized queries:** Never construct SQL with string formatting. Use parameter substitution to prevent SQL injection attacks.

3. **Commit transactions:** Remember to call `commit()` after making changes, or use autocommit mode.

4. **Handle exceptions:** Wrap database operations in try-except blocks to handle potential errors gracefully.

5. **Use appropriate isolation levels:** Choose the right isolation level for your application's concurrency requirements.

---

## Quick Reference Example

```python
import sqlite3

# Connect to database
conn = sqlite3.connect('example.db')

# Create a cursor and execute SQL
cursor = conn.cursor()
cursor.execute('CREATE TABLE IF NOT EXISTS users (id INTEGER PRIMARY KEY, name TEXT)')
cursor.execute('INSERT INTO users (name) VALUES (?)', ('Alice',))

# Commit changes
conn.commit()

# Query data
cursor.execute('SELECT * FROM users')
results = cursor.fetchall()
print(results)

# Close connection
conn.close()
```

This example demonstrates the typical workflow: connect, execute SQL, commit changes, query data, and close the connection.

---
