# Pytest

## Install Mocks

Remember to install `pystest-mock` to be able to use the `mocker` fixture.

```sh
pip install pytest pytest-mock
```

---

## `pathlib.Path` mocks

You can't make mock functions for `pathlib.Path` methods.

```python
# module.py
import pathlib
file = pathlib.Path.cwd() / "file.txt"
file.write_text("Sample text.")

# test_module.py
def test_function(mocker):
    mocker.patch("module.file.write_text") # This will fail.
```

This will fail and raise an error similar to this:

```
FAILED test_module.py::test_function - AttributeError: 'PosixPath' object attribute 'write_text' is read-only
```

This happens mainly because `pathlib.Path` methods are implemented at the C level for performance, and Python's C API makes these attributes immutable descriptors. When you try to assign a mock to a method like `write_bytes`, Python raises an AttributeError because these methods are defined as read-only at the C level.

**Workaround**: Mock the Path constructor.

```python
def test_function(mocker):
    file_mock = mocker.patch("module.file")
    file_mock.write_text.return_value = None
    file_mock.write_text.assert_called_once()
```

---

## `capsys` Fixture

The `capsys` fixture captures stdout and stderr output during test execution.

```python
def test_print_output(capsys):
    print("Hello, World!")
    print("Error message", file=sys.stderr)
    
    captured = capsys.readouterr()
    
    assert captured.out == "Hello, World!\n"
    assert captured.err == "Error message\n"

    new_captured = capsys.readouterr()
    # new_captured.out == ""
    # new_captured.err == ""
    # Reading the output clears the capture buffer.
```

+ `capsys.readouterr()` returns a named tuple with `.out` (stdout) and `.err` (stderr).
+ Reading the output clears the capture buffer. Subsequent calls get only new output.
+ `capsys.disabled()` disables capturing. Output will be displayed on the terminal.

**Note**: `capfd` captures output at the file descriptor level.
