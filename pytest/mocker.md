# Comprehensive Reference Guide: The `mocker` Fixture in pytest

## Core Functionality

### Basic Mock Creation

The `mocker` fixture provides several methods for creating mock objects:

**mocker.Mock()**: Creates a standard mock object with configurable attributes and return values.

```python
def test_basic_mock(mocker):
    mock_obj = mocker.Mock()
    mock_obj.method.return_value = 42
    assert mock_obj.method() == 42
```

**mocker.MagicMock()**: Creates a mock with predefined magic methods, suitable for mocking objects that implement special Python protocols.

```python
def test_magic_mock(mocker):
    mock_obj = mocker.MagicMock()
    mock_obj.__len__.return_value = 5
    assert len(mock_obj) == 5
```

### Patching Objects and Functions

The `mocker.patch()` method replaces target objects with mock instances for the duration of the test:

```python
def test_patch_function(mocker):
    mock_open = mocker.patch('builtins.open', mocker.mock_open(read_data='test data'))
    with open('file.txt') as f:
        content = f.read()
    assert content == 'test data'
```

**Key patching methods include:**

- `mocker.patch(target)`: Patches the specified target with a MagicMock
- `mocker.patch.object(target, attribute)`: Patches a specific attribute of an object
- `mocker.patch.dict(dictionary, values)`: Patches dictionary entries
- `mocker.patch.multiple(target, **kwargs)`: Patches multiple attributes simultaneously

### Spying on Real Objects

The `mocker.spy()` method allows observation of calls to real methods without replacing their implementation:

```python
def test_spy_on_method(mocker):
    obj = MyClass()
    spy = mocker.spy(obj, 'method_name')
    obj.method_name(arg1, arg2)
    spy.assert_called_once_with(arg1, arg2)
```

This functionality proves particularly valuable when verifying that specific methods are invoked while maintaining their original behavior.

---

## Advanced Configuration

### Return Values and Side Effects

Mock objects support sophisticated configuration of return values:

**Single return value:**
```python
mock_func = mocker.Mock(return_value=42)
```

**Sequential return values:**
```python
mock_func = mocker.Mock(side_effect=[1, 2, 3])
assert mock_func() == 1
assert mock_func() == 2
assert mock_func() == 3
```

**Exception raising:**
```python
mock_func = mocker.Mock(side_effect=ValueError("Error message"))
```

**Callable side effects:**
```python
def custom_behavior(*args, **kwargs):
    return args[0] * 2

mock_func = mocker.Mock(side_effect=custom_behavior)
```

### Attribute Specification

The `spec` parameter constrains mock objects to match the interface of the specified class or instance:

```python
def test_with_spec(mocker):
    mock_obj = mocker.Mock(spec=MyClass)
    # Only attributes present in MyClass can be accessed
    mock_obj.existing_method()  # Valid
    mock_obj.nonexistent_method()  # Raises AttributeError
```

The `spec_set` parameter provides stricter enforcement, preventing attribute assignment for non-existent attributes.

---

## Assertion Methods

The `mocker` fixture provides comprehensive assertion capabilities for verifying mock interactions:

### Call Verification

```python
mock_obj.assert_called()  # Verifies at least one call
mock_obj.assert_called_once()  # Verifies exactly one call
mock_obj.assert_called_with(arg1, arg2)  # Verifies last call arguments
mock_obj.assert_called_once_with(arg1, arg2)  # Verifies single call with arguments
mock_obj.assert_not_called()  # Verifies no calls occurred
mock_obj.assert_any_call(arg1, arg2)  # Verifies at least one call with arguments
mock_obj.assert_has_calls([call(arg1), call(arg2)])  # Verifies call sequence
```

### Call Inspection

```python
mock_obj.call_count  # Returns number of calls
mock_obj.call_args  # Returns arguments of last call
mock_obj.call_args_list  # Returns list of all call arguments
mock_obj.method_calls  # Returns list of method calls on mock
```

---

## Context-Specific Patching

The `mocker.patch()` method supports context manager syntax for scoped patching:

```python
def test_context_patch(mocker):
    with mocker.patch('module.function', return_value=10) as mock_func:
        result = module.function()
        assert result == 10
        mock_func.assert_called_once()
```

---

## Property Mocking

Properties require specialized handling through `PropertyMock`:

```python
def test_property_mock(mocker):
    mock_obj = mocker.Mock()
    type(mock_obj).property_name = mocker.PropertyMock(return_value='value')
    assert mock_obj.property_name == 'value'
```

---

## Patching Strategies

### Module-Level Patching

When patching imported objects, the patch target must reference the location where the object is used, not where it is defined:

```python
# In module_a.py
from module_b import function

# Correct patching location
mocker.patch('module_a.function')  # Not 'module_b.function'
```

### Class Method Patching

Instance methods, class methods, and static methods each require specific patching approaches:

```python
# Instance method
mocker.patch.object(MyClass, 'instance_method')

# Class method
mocker.patch.object(MyClass, 'class_method')

# Static method
mocker.patch.object(MyClass, 'static_method')
```

---

## Integration with Fixtures

The `mocker` fixture integrates seamlessly with other pytest fixtures:

```python
@pytest.fixture
def mock_database(mocker):
    return mocker.Mock(spec=Database)

def test_with_fixture(mock_database):
    mock_database.query.return_value = [1, 2, 3]
    result = mock_database.query("SELECT *")
    assert result == [1, 2, 3]
```

---

## Async Mocking

The `mocker` fixture supports asynchronous code through `AsyncMock`:

```python
async def test_async_mock(mocker):
    mock_async = mocker.AsyncMock(return_value=42)
    result = await mock_async()
    assert result == 42
    mock_async.assert_awaited_once()
```

---

## Best Practices

Several principles enhance the effectiveness of mock-based testing:

1. **Minimize patching scope**: Patch only the specific components necessary for test isolation to maintain test clarity and reduce brittleness.

2. **Use specifications**: Apply `spec` or `spec_set` parameters to ensure mocks accurately reflect actual interfaces, catching interface mismatches early.

3. **Verify behavior, not implementation**: Focus assertions on observable outcomes rather than internal implementation details.

4. **Prefer dependency injection**: When feasible, inject dependencies rather than patching, as this approach yields more maintainable and transparent tests.

5. **Reset mocks explicitly when needed**: Although the `mocker` fixture automatically cleans up after tests, complex scenarios may benefit from explicit `mock.reset_mock()` calls.

6. **Document patching rationale**: Complex patching strategies benefit from inline comments explaining the necessity and scope of mocks.

---

## Common Pitfalls

### Import Location Confusion

Patching must target the namespace where the object is accessed, not where it is originally defined. This distinction frequently causes confusion when objects are imported and re-exported across modules.

### Shared Mock State

When using fixtures that return mocks, ensure each test receives an independent mock instance to prevent state leakage between tests.

### Over-Mocking

Excessive mocking can result in tests that pass despite broken production code. Balance mock usage with integration tests that exercise real components.

---
