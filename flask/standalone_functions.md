# Flask Standalone Functions Reference

## Introduction

Flask provides numerous standalone functions that can be imported directly from the `flask` module. These functions are not methods on objects but independent utilities that perform specific tasks. This document provides a comprehensive reference of all major standalone functions available in Flask.

## Response Creation Functions

### render_template()

Renders a Jinja2 template and returns the result as a string.

**Syntax:**
```python
render_template(template_name_or_list, **context)
```

**Parameters:**
- `template_name_or_list`: Name of the template file or list of template names
- `**context`: Variables to pass to the template as keyword arguments

**Returns:** A string containing the rendered template

**Example:**
```python
from flask import Flask, render_template

@app.route('/hello/<name>')
def hello(name):
    return render_template('hello.html', name=name)
```

**Notes:**
- Flask looks for templates in the `templates` folder by default
- Variables passed to the template are automatically escaped for security
- Templates with `.html`, `.htm`, `.xml`, or `.xhtml` extensions have autoescaping enabled

### render_template_string()

Renders a template from a string rather than a file.

**Syntax:**
```python
render_template_string(source, **context)
```

**Parameters:**
- `source`: The template source as a string
- `**context`: Variables to pass to the template

**Returns:** A string containing the rendered template

**Example:**
```python
from flask import render_template_string

@app.route('/greet')
def greet():
    template = '<h1>Hello {{ name }}!</h1>'
    return render_template_string(template, name='World')
```

**Security Warning:** Using `render_template_string()` with user input can create security vulnerabilities. Use `render_template()` whenever possible.

### jsonify()

Creates a JSON response with the correct content type header.

**Syntax:**
```python
jsonify(*args, **kwargs)
```

**Parameters:**
- `*args`: A single dict or list to serialize
- `**kwargs`: Key-value pairs to serialize as a dict

**Returns:** A Response object with JSON data and `application/json` content type

**Example:**
```python
from flask import jsonify

@app.route('/api/user')
def get_user():
    return jsonify(username='john', email='john@example.com')

@app.route('/api/data')
def get_data():
    data = {'items': [1, 2, 3], 'total': 3}
    return jsonify(data)
```

**Notes:**
- Automatically sets the `Content-Type` header to `application/json`
- Can return a status code as a tuple: `return jsonify(data), 200`
- Supports serializing dicts and lists

### make_response()

Converts a return value into a Response object.

**Syntax:**
```python
make_response(*args)
```

**Parameters:**
- Can accept: `(body)`, `(body, status)`, `(body, headers)`, or `(body, status, headers)`

**Returns:** A Response object

**Example:**
```python
from flask import make_response

@app.route('/custom')
def custom_response():
    response = make_response('<h1>Custom Response</h1>', 200)
    response.headers['X-Custom-Header'] = 'Value'
    return response
```

**Use Cases:**
- Setting custom headers
- Modifying cookies
- Customizing response behavior

## URL and Routing Functions

### url_for()

Generates a URL for a given endpoint.

**Syntax:**
```python
url_for(endpoint, **values)
```

**Parameters:**
- `endpoint`: The name of the view function
- `**values`: Variable arguments for the URL rule, plus optional special arguments
- `_external`: If `True`, generates an absolute URL with scheme and domain
- `_scheme`: Specifies URL scheme (http, https)
- `_anchor`: Appends an anchor to the URL
- `_method`: Generates URL for specific HTTP method

**Returns:** A string containing the generated URL

**Example:**
```python
from flask import url_for

@app.route('/')
def index():
    return 'Home'

@app.route('/user/<username>')
def profile(username):
    return f'Profile: {username}'

# Generate URLs
with app.test_request_context():
    print(url_for('index'))  # Output: /
    print(url_for('profile', username='john'))  # Output: /user/john
    print(url_for('index', _external=True))  # Output: http://example.com/
```

**Notes:**
- Prefixing with `.` references the current blueprint: `url_for('.index')`
- Unknown keyword arguments are appended as query parameters
- More maintainable than hardcoding URLs

### redirect()

Creates a redirect response to another URL.

**Syntax:**
```python
redirect(location, code=302)
```

**Parameters:**
- `location`: The URL to redirect to
- `code`: HTTP status code (301, 302, 303, 305, or 307)

**Returns:** A Response object that redirects to the specified location

**Example:**
```python
from flask import redirect, url_for

@app.route('/old-page')
def old_page():
    return redirect(url_for('new_page'))

@app.route('/new-page')
def new_page():
    return 'This is the new page'

@app.route('/external')
def external_redirect():
    return redirect('https://example.com')
```

**Status Codes:**
- 301: Permanent redirect
- 302: Temporary redirect (default)
- 303: See other
- 307: Temporary redirect (preserves method)

## File Handling Functions

### send_file()

Sends a file to the client.

**Syntax:**
```python
send_file(path_or_file, mimetype=None, as_attachment=False, 
          download_name=None, **kwargs)
```

**Parameters:**
- `path_or_file`: Path to file or file-like object
- `mimetype`: MIME type of the file
- `as_attachment`: If `True`, triggers download
- `download_name`: Name to use for download

**Returns:** A Response object containing the file

**Example:**
```python
from flask import send_file

@app.route('/download/<filename>')
def download_file(filename):
    return send_file(f'/path/to/{filename}', as_attachment=True)

@app.route('/image')
def get_image():
    return send_file('static/image.png', mimetype='image/png')
```

### send_from_directory()

Securely sends a file from a directory.

**Syntax:**
```python
send_from_directory(directory, path, **kwargs)
```

**Parameters:**
- `directory`: The directory containing the file
- `path`: The filename relative to the directory
- `**kwargs`: Additional arguments passed to `send_file()`

**Returns:** A Response object containing the file

**Example:**
```python
from flask import send_from_directory

@app.route('/uploads/<path:filename>')
def uploaded_file(filename):
    return send_from_directory('uploads', filename)

@app.route('/static-files/<path:filename>')
def serve_static(filename):
    return send_from_directory('static', filename, as_attachment=True)
```

**Security:** This function prevents directory traversal attacks by validating the path.

### safe_join()

Safely joins directory and filename paths.

**Syntax:**
```python
safe_join(directory, *pathnames)
```

**Parameters:**
- `directory`: The base directory
- `*pathnames`: Path components to join

**Returns:** The safely joined path

**Raises:** `NotFound` error if the path would escape the directory

**Example:**
```python
from flask import safe_join

@app.route('/wiki/<path:filename>')
def wiki_page(filename):
    filepath = safe_join(app.config['WIKI_FOLDER'], filename)
    with open(filepath, 'r') as f:
        content = f.read()
    return content
```

## Error Handling Functions

### abort()

Raises an HTTP exception to abort request handling.

**Syntax:**
```python
abort(status, *args, **kwargs)
```

**Parameters:**
- `status`: HTTP status code or a Response object
- `description`: Optional error description

**Raises:** HTTPException with the specified status code

**Example:**
```python
from flask import abort

@app.route('/user/<int:user_id>')
def get_user(user_id):
    user = find_user(user_id)
    if user is None:
        abort(404, description='User not found')
    return render_template('user.html', user=user)

@app.route('/admin')
def admin():
    if not is_admin():
        abort(403)
    return 'Admin panel'
```

**Common Status Codes:**
- 400: Bad Request
- 401: Unauthorized
- 403: Forbidden
- 404: Not Found
- 500: Internal Server Error

## Message Flashing Functions

### flash()

Stores a message for the next request.

**Syntax:**
```python
flash(message, category='message')
```

**Parameters:**
- `message`: The message to flash
- `category`: Category for filtering messages (e.g., 'error', 'info', 'success')

**Returns:** None

**Example:**
```python
from flask import flash, redirect, url_for

@app.route('/login', methods=['POST'])
def login():
    if authenticate(request.form['username'], request.form['password']):
        flash('Login successful!', 'success')
        return redirect(url_for('index'))
    else:
        flash('Invalid credentials', 'error')
        return redirect(url_for('login'))
```

**Notes:**
- Requires `app.secret_key` to be set
- Messages are stored in the session
- Messages are displayed only once

### get_flashed_messages()

Retrieves and removes flashed messages from the session.

**Syntax:**
```python
get_flashed_messages(with_categories=False, category_filter=[])
```

**Parameters:**
- `with_categories`: If `True`, returns tuples of `(category, message)`
- `category_filter`: List of categories to include

**Returns:** List of messages or list of tuples

**Example:**
```python
from flask import get_flashed_messages

# In a template (Jinja2):
# {% with messages = get_flashed_messages() %}
#   {% if messages %}
#     {% for message in messages %}
#       <p>{{ message }}</p>
#     {% endfor %}
#   {% endif %}
# {% endwith %}

# With categories:
# {% with messages = get_flashed_messages(with_categories=true) %}
#   {% if messages %}
#     {% for category, message in messages %}
#       <p class="{{ category }}">{{ message }}</p>
#     {% endfor %}
#   {% endif %}
# {% endwith %}
```

**Notes:**
- Automatically available in templates
- Messages are cleared after retrieval
- Can filter by category

## Streaming Functions

### stream_template()

Renders a template as a stream of strings.

**Syntax:**
```python
stream_template(template_name_or_list, **context)
```

**Parameters:**
- `template_name_or_list`: Name of the template
- `**context`: Variables to pass to the template

**Returns:** A Response object that streams the template

**Example:**
```python
from flask import stream_template

@app.route('/large-page')
def large_page():
    return stream_template('large_page.html', data=large_data)
```

**Use Cases:**
- Large templates that should load progressively
- Reducing memory usage
- Faster time to first byte

### stream_template_string()

Renders a template string as a stream.

**Syntax:**
```python
stream_template_string(source, **context)
```

**Parameters:**
- `source`: Template source as a string
- `**context`: Variables to pass to the template

**Returns:** A Response object that streams the template

### stream_with_context()

Keeps the request context active during streaming.

**Syntax:**
```python
stream_with_context(generator_or_function)
```

**Parameters:**
- `generator_or_function`: A generator or function to wrap

**Returns:** A generator that maintains the request context

**Example:**
```python
from flask import stream_with_context, request
from markupsafe import escape

@app.route('/stream')
def streamed_response():
    def generate():
        yield '<p>Hello '
        yield escape(request.args['name'])
        yield '!</p>'
    return stream_with_context(generate())
```

**Use Cases:**
- Accessing `request` or other context objects in generators
- Long-running responses that need context
- Server-sent events

## Security Functions

### escape()

Escapes special HTML characters to prevent XSS attacks.

**Syntax:**
```python
escape(text)
```

**Parameters:**
- `text`: The string to escape

**Returns:** A string with HTML-safe characters

**Example:**
```python
from flask import escape

@app.route('/hello/<name>')
def hello(name):
    # Manually escape user input
    safe_name = escape(name)
    return f'<h1>Hello {safe_name}!</h1>'
```

**Characters Escaped:**
- `&` → `&amp;`
- `<` → `&lt;`
- `>` → `&gt;`
- `'` → `&#39;`
- `"` → `&#34;`

**Notes:**
- Templates automatically escape variables by default
- Use the `|safe` filter in templates to disable escaping (with caution)

## Context Functions

### has_request_context()

Checks if a request context is currently active.

**Syntax:**
```python
has_request_context()
```

**Returns:** `True` if a request context is active, `False` otherwise

**Example:**
```python
from flask import has_request_context, request

class User:
    def __init__(self, username, remote_addr=None):
        self.username = username
        if remote_addr is None and has_request_context():
            remote_addr = request.remote_addr
        self.remote_addr = remote_addr
```

**Use Cases:**
- Code that may run inside or outside requests
- Optional access to request data
- Testing and CLI commands

### has_app_context()

Checks if an application context is currently active.

**Syntax:**
```python
has_app_context()
```

**Returns:** `True` if an app context is active, `False` otherwise

**Example:**
```python
from flask import has_app_context, current_app

def get_config_value(key):
    if has_app_context():
        return current_app.config.get(key)
    return None
```

### after_this_request()

Registers a function to run after the current request.

**Syntax:**
```python
after_this_request(func)
```

**Parameters:**
- `func`: A function that takes a response object and returns a response object

**Returns:** The decorated function

**Example:**
```python
from flask import after_this_request

@app.route('/')
def index():
    @after_this_request
    def add_header(response):
        response.headers['X-Custom-Header'] = 'Value'
        return response
    return 'Hello World!'
```

**Use Cases:**
- Modifying responses in specific views
- Adding headers conditionally
- Logging or cleanup tasks

### copy_current_request_context()

Decorates a function to retain the current request context.

**Syntax:**
```python
copy_current_request_context(func)
```

**Parameters:**
- `func`: The function to decorate

**Returns:** A decorated function that preserves the request context

**Example:**
```python
from flask import copy_current_request_context
import gevent

@app.route('/')
def index():
    @copy_current_request_context
    def do_work():
        # Can access request here
        return process_request_data(request.args)
    
    gevent.spawn(do_work)
    return 'Processing...'
```

**Use Cases:**
- Background tasks with greenlets
- Asynchronous operations
- Threading scenarios

## Summary Table

| Function | Purpose | Common Use Case |
|----------|---------|-----------------|
| `render_template()` | Render HTML templates | Displaying pages |
| `render_template_string()` | Render template from string | Dynamic templates |
| `jsonify()` | Create JSON responses | API endpoints |
| `make_response()` | Create custom responses | Setting headers/cookies |
| `url_for()` | Generate URLs | Links in templates |
| `redirect()` | Redirect to another URL | After form submission |
| `send_file()` | Send files to client | File downloads |
| `send_from_directory()` | Securely serve files | User uploads |
| `safe_join()` | Join paths safely | File path validation |
| `abort()` | Raise HTTP errors | Error handling |
| `flash()` | Store user messages | User feedback |
| `get_flashed_messages()` | Retrieve messages | Display notifications |
| `stream_template()` | Stream large templates | Performance optimization |
| `stream_with_context()` | Maintain context in generators | Streaming responses |
| `escape()` | Escape HTML characters | Security |
| `has_request_context()` | Check request context | Conditional logic |
| `has_app_context()` | Check app context | Conditional logic |
| `after_this_request()` | Run function after request | Response modification |
| `copy_current_request_context()` | Preserve context | Background tasks |

## Best Practices

1. **Always use `url_for()`** instead of hardcoding URLs for maintainability
2. **Use `send_from_directory()`** instead of `send_file()` when serving user uploads
3. **Set `app.secret_key`** before using sessions or flash messages
4. **Use `render_template()`** instead of `render_template_string()` to avoid security issues
5. **Check contexts** with `has_request_context()` when writing reusable code
6. **Use `jsonify()`** for JSON responses instead of manual serialization
7. **Validate user input** before using it with file functions
8. **Use `abort()`** to handle error conditions explicitly
9. **Keep flash messages concise** as they are stored in cookies
10. **Use streaming functions** for large responses to improve performance

---
