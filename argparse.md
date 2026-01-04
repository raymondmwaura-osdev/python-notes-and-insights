# `argparse`

## Module Import

```python
import argparse
```

---

## ArgumentParser Class

### Constructor: `ArgumentParser()`

**Purpose:** Creates a parser object for command-line arguments.

**Parameters:**

- `prog` (str): Program name (default: `sys.argv[0]`)
- `usage` (str): Custom usage message (default: auto-generated)
- `description` (str): Text displayed before argument help
- `epilog` (str): Text displayed after argument help
- `parents` (list): List of ArgumentParser objects to inherit arguments from
- `formatter_class`: Help formatting class (default: `HelpFormatter`)
  - `HelpFormatter`: Default
  - `RawDescriptionHelpFormatter`: Preserves description whitespace
  - `RawTextHelpFormatter`: Preserves all help text formatting
  - `ArgumentDefaultsHelpFormatter`: Shows defaults in help
  - `MetavarTypeHelpFormatter`: Uses type names as display names
- `prefix_chars` (str): Characters that prefix optional arguments (default: `'-'`)
- `fromfile_prefix_chars` (str): Characters that prefix files with arguments (default: `None`)
- `argument_default`: Global default for arguments (default: `None`)
- `conflict_handler` (str): Strategy for resolving conflicts ('error' or 'resolve')
- `add_help` (bool): Add `-h`/`--help` option (default: `True`)
- `allow_abbrev` (bool): Allow abbreviated long options (default: `True`)
- `exit_on_error` (bool): Exit on parse errors (default: `True`, available since Python 3.9)

**Returns:** ArgumentParser object

**Example:**
```python
parser = argparse.ArgumentParser(
    prog='myapp',
    description='Process data files',
    epilog='Report bugs to bugs@example.com'
)
```

---

## ArgumentParser Methods

### `add_argument()`

**Purpose:** Adds an argument specification to the parser.

**Signature:**
```python
add_argument(name_or_flags, **kwargs)
```

**Parameters:**

- `name_or_flags`: Either a name ('foo') for positional arguments or option strings ('-f', '--foo') for optional arguments

**Keyword Arguments:**

- **`action`** (str): How the argument should be handled
  - `'store'`: Store value (default)
  - `'store_const'`: Store constant value specified by `const`
  - `'store_true'`: Store `True` when flag present
  - `'store_false'`: Store `False` when flag present
  - `'append'`: Append values to a list
  - `'append_const'`: Append constant to a list
  - `'count'`: Count occurrences of the flag
  - `'help'`: Print help and exit
  - `'version'`: Print version and exit
  - `'extend'`: Extend a list with multiple values (Python 3.8+)
  - `argparse.BooleanOptionalAction`: Create `--foo`/`--no-foo` pairs (Python 3.9+)

- **`nargs`**: Number of arguments to consume
  - Integer (e.g., `2`): Exact number of arguments
  - `'?'`: Zero or one argument
  - `'*'`: Zero or more arguments (returns list)
  - `'+'`: One or more arguments (returns list)
  - `argparse.REMAINDER`: All remaining arguments

- **`const`**: Constant value for `store_const` and `append_const` actions, or default value for optional arguments with `nargs='?'`

- **`default`**: Value when argument is absent (default: `None`)

- **`type`**: Callable to convert argument string
  - Built-in: `int`, `float`, `str`, `bool`
  - `argparse.FileType(mode)`: Opens file in specified mode
  - Custom function: Must accept string, return converted value, raise `argparse.ArgumentTypeError` on failure

- **`choices`**: Container of allowed values (list, set, range, etc.)

- **`required`** (bool): Whether optional argument is required (default: `False`)

- **`help`** (str): Help message for argument. Use `argparse.SUPPRESS` to hide from help

- **`metavar`** (str): Display name in usage messages (does not affect attribute name)

- **`dest`** (str): Attribute name in result namespace (default: derived from option string or positional name)

- **`deprecated`** (bool): Mark argument as deprecated (Python 3.13+)

**Returns:** Argument object

**Examples:**
```python
# Positional
parser.add_argument('filename')

# Optional with short and long form
parser.add_argument('-v', '--verbose', action='store_true')

# With type conversion
parser.add_argument('-n', '--number', type=int, default=0)

# With choices
parser.add_argument('--format', choices=['json', 'xml', 'csv'])

# Multiple values
parser.add_argument('--files', nargs='+', help='One or more files')

# Constant value
parser.add_argument('--debug', action='store_const', const=True, dest='debug_mode')
```

---

### `add_argument_group()`

**Purpose:** Creates a logical group of arguments for help display.

**Parameters:**
- `title` (str): Group title in help output
- `description` (str): Group description

**Returns:** ArgumentGroup object (has `add_argument()` method)

**Example:**
```python
group = parser.add_argument_group('authentication', 'Authentication options')
group.add_argument('--username')
group.add_argument('--password')
```

---

### `add_mutually_exclusive_group()`

**Purpose:** Creates a group where only one argument can be specified.

**Parameters:**
- `required` (bool): Whether one of the arguments is required (default: `False`)

**Returns:** MutuallyExclusiveGroup object (has `add_argument()` method)

**Example:**
```python
group = parser.add_mutually_exclusive_group(required=True)
group.add_argument('--json', action='store_true')
group.add_argument('--xml', action='store_true')
```

---

### `add_subparsers()`

**Purpose:** Creates subcommands with their own argument sets.

**Parameters:**
- `title` (str): Title for subparser group in help
- `description` (str): Description for subparser group
- `prog` (str): Usage information displayed with subcommand help
- `parser_class`: Class to create subparser instances (default: ArgumentParser)
- `action`: Action class for storing subparser choice
- `dest` (str): Attribute name to store subcommand name
- `required` (bool): Whether subcommand is required (Python 3.7+)
- `help` (str): Help for subparser group
- `metavar` (str): String for subcommand in usage messages

**Returns:** Action object with `add_parser()` method

**Example:**
```python
subparsers = parser.add_subparsers(dest='command', help='Available commands')

# Add subparser
parser_add = subparsers.add_parser('add', help='Add item')
parser_add.add_argument('name')

parser_delete = subparsers.add_parser('delete', help='Delete item')
parser_delete.add_argument('id', type=int)
```

---

### `parse_args()`

**Purpose:** Parses command-line arguments.

**Parameters:**
- `args` (list): List of strings to parse (default: `sys.argv[1:]`)
- `namespace` (Namespace): Object to populate attributes (default: new Namespace)

**Returns:** Namespace object with parsed arguments as attributes

**Raises:** SystemExit on parsing errors (unless `exit_on_error=False`)

**Example:**
```python
args = parser.parse_args()
# Access: args.filename, args.verbose, etc.

# Parse custom argument list
args = parser.parse_args(['--verbose', 'file.txt'])

# Use existing namespace
namespace = argparse.Namespace(foo=1)
parser.parse_args(namespace=namespace)
```

---

### `parse_known_args()`

**Purpose:** Parses known arguments, ignoring unknown ones.

**Parameters:**
- `args` (list): List of strings to parse (default: `sys.argv[1:]`)
- `namespace` (Namespace): Object to populate

**Returns:** Tuple of (Namespace, list of remaining arguments)

**Example:**
```python
args, remaining = parser.parse_known_args()
# remaining contains unparsed arguments
```

---

### `parse_intermixed_args()`

**Purpose:** Parses arguments allowing positionals and optionals to be intermixed.

**Parameters:**
- `args` (list): List of strings to parse
- `namespace` (Namespace): Object to populate

**Returns:** Namespace object

**Note:** Available since Python 3.7

**Example:**
```python
# Allows: script.py file1 --option file2
args = parser.parse_intermixed_args()
```

---

### `parse_known_intermixed_args()`

**Purpose:** Combines `parse_known_args()` and `parse_intermixed_args()`.

**Returns:** Tuple of (Namespace, list of remaining arguments)

---

### `set_defaults()`

**Purpose:** Sets default values for arguments.

**Parameters:** Keyword arguments mapping dest names to values

**Example:**
```python
parser.set_defaults(verbose=False, output='stdout')
```

---

### `get_default()`

**Purpose:** Gets default value for an argument.

**Parameters:**
- `dest` (str): Destination attribute name

**Returns:** Default value or `None`

**Example:**
```python
default_val = parser.get_default('verbose')
```

---

### `print_help()`

**Purpose:** Prints help message to file.

**Parameters:**
- `file`: File object (default: `sys.stdout`)

**Example:**
```python
parser.print_help()
```

---

### `format_help()`

**Purpose:** Returns help message as a string.

**Returns:** str

**Example:**
```python
help_text = parser.format_help()
```

---

### `print_usage()`

**Purpose:** Prints usage message to file.

**Parameters:**
- `file`: File object (default: `sys.stdout`)

---

### `format_usage()`

**Purpose:** Returns usage message as a string.

**Returns:** str

---

### `exit()`

**Purpose:** Exits with status code and optional message.

**Parameters:**
- `status` (int): Exit code (default: `0`)
- `message` (str): Message to print to stderr

**Example:**
```python
parser.exit(status=1, message='Fatal error\n')
```

---

### `error()`

**Purpose:** Prints error message and exits with status 2.

**Parameters:**
- `message` (str): Error message

**Example:**
```python
if args.value < 0:
    parser.error('value must be non-negative')
```

---

## Namespace Class

**Purpose:** Simple object holding parsed argument values as attributes.

### Constructor: `Namespace()`

**Parameters:** Keyword arguments become attributes

**Example:**
```python
ns = argparse.Namespace(x=1, y=2)
print(ns.x)  # 1
```

### Converting to Dictionary

```python
args = parser.parse_args()
args_dict = vars(args)  # Returns dict
```

---

## FileType Class

**Purpose:** Factory for creating file objects from command-line arguments.

### Constructor: `FileType(mode, bufsize, encoding, errors)`

**Parameters:**
- `mode` (str): File mode ('r', 'w', 'a', 'rb', 'wb', etc.)
- `bufsize` (int): Buffer size (default: `-1` for default buffering)
- `encoding` (str): Text encoding (default: `None`)
- `errors` (str): Error handling strategy (default: `None`)

**Special Values:**
- `'-'`: stdin for readable modes, stdout for writable modes

**Example:**
```python
parser.add_argument('input', type=argparse.FileType('r'))
parser.add_argument('--output', type=argparse.FileType('w'), default='-')

args = parser.parse_args()
content = args.input.read()
args.output.write(content)
```

---

## Action Classes

Custom actions can be created by subclassing `argparse.Action`.

### Action Base Class

**Methods to Override:**

- `__init__(self, option_strings, dest, **kwargs)`: Initialize action
- `__call__(self, parser, namespace, values, option_string)`: Execute action

**Attributes:**
- `option_strings`: List of option strings ('-f', '--foo')
- `dest`: Attribute name in namespace
- `nargs`: Number of arguments
- `const`: Constant value
- `default`: Default value
- `type`: Type conversion function
- `choices`: Allowed values
- `required`: Whether required
- `help`: Help message
- `metavar`: Display name

**Example:**
```python
class CustomAction(argparse.Action):
    def __call__(self, parser, namespace, values, option_string=None):
        # Custom processing
        setattr(namespace, self.dest, values.upper())

parser.add_argument('--name', action=CustomAction)
```

---

## Constants

### `argparse.SUPPRESS`

**Purpose:** Suppresses attribute creation or help display.

**Usage:**
```python
# Don't create attribute if argument not provided
parser.add_argument('--foo', default=argparse.SUPPRESS)

# Hide argument from help
parser.add_argument('--internal', help=argparse.SUPPRESS)
```

---

### `argparse.REMAINDER`

**Purpose:** Collects all remaining command-line arguments.

**Usage:**
```python
parser.add_argument('command')
parser.add_argument('args', nargs=argparse.REMAINDER)
# script.py run --debug file.txt
# command='run', args=['--debug', 'file.txt']
```

---

## Exceptions

### `ArgumentError`

**Raised:** When argument configuration is invalid.

**Attributes:**
- `argument_name`: Name of problematic argument
- `message`: Error description

---

### `ArgumentTypeError`

**Raised:** By type conversion functions to indicate invalid values.

**Usage:**
```python
def positive_int(value):
    ivalue = int(value)
    if ivalue <= 0:
        raise argparse.ArgumentTypeError(f"{value} must be positive")
    return ivalue

parser.add_argument('--count', type=positive_int)
```

---

## Type Conversion Functions

### Built-in Types

```python
parser.add_argument('--number', type=int)
parser.add_argument('--ratio', type=float)
parser.add_argument('--name', type=str)  # default, usually omitted
```

### Custom Type Functions

**Requirements:**
- Accept single string argument
- Return converted value
- Raise `ArgumentTypeError` for invalid input

**Example:**
```python
def valid_date(s):
    from datetime import datetime
    try:
        return datetime.strptime(s, '%Y-%m-%d')
    except ValueError:
        raise argparse.ArgumentTypeError(f"Invalid date: {s}")

parser.add_argument('--date', type=valid_date)
```

---

## Format Strings in Help

### Available Formatting

**In help text:**
- `%(prog)s`: Program name
- `%(default)s`: Default value
- `%(type)s`: Argument type
- `%(choices)s`: Available choices
- `%(dest)s`: Destination attribute name

**Example:**
```python
parser.add_argument(
    '--retry',
    type=int,
    default=3,
    help='Number of retries (default: %(default)s)'
)
```

---

## Parent Parsers

**Purpose:** Share common arguments across multiple parsers.

**Parameters:** `parents` list in ArgumentParser constructor

**Note:** Parent parsers must be fully configured before being used as parents.

**Example:**
```python
# Common arguments
parent = argparse.ArgumentParser(add_help=False)
parent.add_argument('--verbose', action='store_true')
parent.add_argument('--debug', action='store_true')

# Child parsers inherit parent arguments
parser_a = argparse.ArgumentParser(parents=[parent])
parser_a.add_argument('--file')

parser_b = argparse.ArgumentParser(parents=[parent])
parser_b.add_argument('--output')
```

---

## Prefix Characters

**Purpose:** Customize characters that indicate optional arguments.

**Default:** `'-'` (produces `-f`, `--foo`)

**Example:**
```python
# Use '+' for optional arguments
parser = argparse.ArgumentParser(prefix_chars='+')
parser.add_argument('+f')  # +f instead of -f
parser.add_argument('++foo')  # ++foo instead of --foo
```

---

## Fromfile Prefix Characters

**Purpose:** Read arguments from files.

**Configuration:**
```python
parser = argparse.ArgumentParser(fromfile_prefix_chars='@')
```

**Usage:**
```bash
# Contents of args.txt: --verbose\n--output=file.txt
python script.py @args.txt
```

**File Format:**
- One argument per line
- Comments and blank lines ignored

---

## Abbreviations

**Behavior:** Long options can be abbreviated if unambiguous (default: enabled).

**Control:**
```python
parser = argparse.ArgumentParser(allow_abbrev=False)
```

**Example:**
```python
parser.add_argument('--verbose')
parser.add_argument('--version')

# With allow_abbrev=True (default):
# --ver is ambiguous (error)
# --verb works (matches --verbose)
# --vers works (matches --version)
```

---

## Conflict Resolution

**Purpose:** Handle duplicate option strings.

**Strategies:**
- `'error'`: Raise error (default)
- `'resolve'`: Override previous argument

**Example:**
```python
parser = argparse.ArgumentParser(conflict_handler='resolve')
parser.add_argument('-f', '--foo')
parser.add_argument('-f', '--bar')  # Replaces first -f
```

---

## Partial Parsing

**Scenario:** Parse subset of arguments, leave rest for other processing.

**Method:** `parse_known_args()`

**Example:**
```python
# Main parser knows about --verbose
parser = argparse.ArgumentParser()
parser.add_argument('--verbose', action='store_true')

args, remaining = parser.parse_known_args()
# remaining contains unknown arguments

# Pass remaining to subprocess or another parser
```

---

## Intermixed Arguments

**Purpose:** Allow positional and optional arguments in any order.

**Default Behavior:** Positionals must come after optionals.

**Methods:** `parse_intermixed_args()`, `parse_known_intermixed_args()`

**Example:**
```python
parser.add_argument('files', nargs='+')
parser.add_argument('--verbose', action='store_true')

# Standard parsing requires:
# script.py --verbose file1 file2

# Intermixed allows:
# script.py file1 --verbose file2
args = parser.parse_intermixed_args()
```

---

## Argument Abbreviations

**Behavior:** Partial matches of long options are accepted if unambiguous.

**Disable:**
```python
parser = argparse.ArgumentParser(allow_abbrev=False)
```

---

## Exit Behavior

**Default:** Parser exits on error with status 2.

**Custom Handling (Python 3.9+):**
```python
parser = argparse.ArgumentParser(exit_on_error=False)

try:
    args = parser.parse_args()
except argparse.ArgumentError as e:
    # Handle error without exiting
    print(f"Error: {e}")
```

---

## Version Action

**Purpose:** Display version and exit.

**Configuration:**
```python
parser.add_argument(
    '--version',
    action='version',
    version='%(prog)s 2.0'
)
```

**Usage:**
```bash
python script.py --version
# Output: script.py 2.0
```

---

## Special Argument Values

### `--` (Double Dash)

**Purpose:** Indicates end of optional arguments; remaining items treated as positionals.

**Example:**
```bash
python script.py --option -- -file-that-starts-with-dash.txt
# -file-that-starts-with-dash.txt treated as positional, not as option
```

---

## Accessing Arguments

### As Attributes
```python
args = parser.parse_args()
print(args.verbose)
print(args.output)
```

### As Dictionary
```python
args = parser.parse_args()
args_dict = vars(args)
print(args_dict['verbose'])
```

### Checking Existence
```python
args = parser.parse_args()
if hasattr(args, 'verbose'):
    # Argument was set
    pass
```

---

## Common Patterns

### Optional Input File (stdin if not specified)
```python
parser.add_argument(
    'infile',
    nargs='?',
    type=argparse.FileType('r'),
    default=sys.stdin
)
```

### Optional Output File (stdout if not specified)
```python
parser.add_argument(
    '--output',
    type=argparse.FileType('w'),
    default=sys.stdout
)
```

### Verbosity Levels
```python
parser.add_argument(
    '-v', '--verbose',
    action='count',
    default=0,
    help='Increase verbosity (-v, -vv, -vvv)'
)
```

### Dry Run Flag
```python
parser.add_argument(
    '--dry-run',
    action='store_true',
    help='Simulate actions without executing'
)
```

### Configuration Override
```python
parser.add_argument('--config', help='Config file path')
parser.set_defaults(timeout=30, retries=3)

args = parser.parse_args()
# Command line arguments override defaults
```

---

## Notes

- Option strings starting with `-` are treated as optional arguments
- Arguments without `-` prefix are positional
- `parse_args()` returns Namespace object; access values as attributes
- Help is automatically generated; `-h`/`--help` flags added by default
- Type conversion happens before value is stored
- Default value is used when argument is not provided
- Required optional arguments are validated after parsing
- Mutually exclusive groups enforce that only one argument from group is specified
- Subparsers allow command hierarchies (e.g., `git commit`, `git push`)
- Argument order in code determines help display order
- Positional arguments are required by default; optional arguments are optional by default

---
