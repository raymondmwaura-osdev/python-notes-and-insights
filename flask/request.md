# Flask Request Object - Comprehensive Reference

## Introduction

The Flask request object is a global proxy that provides access to incoming HTTP request data in Flask applications. It is automatically created by Flask for each incoming request and contains information about the request method, headers, URL parameters, form data, cookies, and uploaded files.

The request object must be imported from Flask before use:

```python
from flask import request
```

The request object is only available within the request context. It is a context-local object, meaning it is unique to each thread handling a request.

## Important Notes

The Flask request object is based on Werkzeug's Request class and inherits all of its attributes and methods. Flask adds several Flask-specific attributes such as `endpoint`, `view_args`, and `blueprint`.

The request object is read-only. Modifications to the request object are not supported.

## Core Attributes

### HTTP Method and URL Information

**`request.method`**
- Returns the HTTP method used in the request (GET, POST, PUT, DELETE, PATCH, etc.)
- Type: string
- Example:
```python
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        # Handle form submission
        pass
    else:
        # Display login form
        pass
```

**`request.url`**
- Returns the complete URL of the request, including scheme, host, path, and query string
- Type: string
- Example: `https://example.com/page?id=5`

**`request.base_url`**
- Returns the URL without the query string
- Type: string
- Example: `https://example.com/page`

**`request.url_root`**
- Returns the root URL with scheme and host
- Type: string
- Example: `https://example.com/`

**`request.path`**
- Returns the path portion of the URL without the domain
- Type: string
- Example: `/page`

**`request.full_path`**
- Returns the path with query string
- Type: string
- Example: `/page?id=5`

**`request.script_root`**
- Returns the application root path
- Type: string

**`request.host`**
- Returns the hostname from the request
- Type: string
- Example: `example.com:8080`

**`request.host_url`**
- Returns the host URL with scheme
- Type: string
- Example: `https://example.com:8080/`

**`request.scheme`**
- Returns the URL scheme (http or https)
- Type: string

**`request.is_secure`**
- Returns True if the request was made over HTTPS
- Type: boolean

### Query Parameters

**`request.args`**
- Contains parsed URL query parameters (the part after the question mark in the URL)
- Type: ImmutableMultiDict
- Query parameters are key-value pairs appended to the URL after a question mark and separated by ampersands
- Example:
```python
# URL: /search?q=flask&page=2
@app.route('/search')
def search():
    query = request.args.get('q')  # Returns 'flask'
    page = request.args.get('page', default=1, type=int)  # Returns 2
    return f"Searching for: {query}, Page: {page}"
```

### Form Data

**`request.form`**
- Contains parsed form data from POST or PUT requests
- Type: ImmutableMultiDict
- Only contains data if the request method was POST, PUT, or PATCH and the content type is application/x-www-form-urlencoded or multipart/form-data
- Example:
```python
@app.route('/login', methods=['POST'])
def login():
    username = request.form.get('username')
    password = request.form.get('password')
    return f"Logging in as: {username}"
```

**`request.values`**
- A CombinedMultiDict containing both `form` and `args` data
- Type: CombinedMultiDict
- Useful when you want to accept data from either query parameters or form data

### JSON Data

**`request.json`**
- Contains parsed JSON data if the request content type indicates JSON
- Type: dict, list, or None
- This attribute is deprecated. Use `request.get_json()` instead
- Returns None if the mimetype is not application/json

**`request.get_json(force=False, silent=False, cache=True)`**
- Parses the incoming JSON request data and returns it
- Parameters:
  - `force`: If True, the mimetype is ignored and parsing is attempted anyway
  - `silent`: If True, the method fails silently and returns None on parsing errors
  - `cache`: If True, the parsed JSON data is cached on the request object
- Returns the parsed JSON data or None
- Example:
```python
@app.route('/api/data', methods=['POST'])
def receive_data():
    data = request.get_json()
    name = data.get('name')
    return {'message': f'Hello, {name}'}
```

**`request.is_json`**
- Returns True if the mimetype indicates JSON (application/json or application/*+json)
- Type: boolean

### Request Body

**`request.data`**
- Contains the raw request body data as bytes
- Type: bytes
- Only available if the content type is not a known form type
- If the data represents form data, it will be empty and the form data will be in `request.form`

**`request.get_data(cache=True, as_text=False, parse_form_data=False)`**
- Reads and returns the request body data
- Parameters:
  - `cache`: Whether to cache the data
  - `as_text`: If True, return data as string instead of bytes
  - `parse_form_data`: If True, parse form data
- Returns bytes by default or string if as_text is True

**`request.stream`**
- The WSGI input stream for reading raw request data
- This stream can only be consumed once
- Use `get_data()` to get the full data as bytes or text

### File Uploads

**`request.files`**
- Contains uploaded files from multipart/form-data requests
- Type: ImmutableMultiDict
- Each file is stored as a FileStorage object
- Files will only be present if the request method was POST, PUT, or PATCH and the form had `enctype="multipart/form-data"`
- Example:
```python
@app.route('/upload', methods=['POST'])
def upload_file():
    if 'file' in request.files:
        file = request.files['file']
        if file.filename != '':
            file.save('/path/to/uploads/' + file.filename)
            return 'File uploaded successfully'
    return 'No file uploaded'
```

**FileStorage Methods:**
- `file.save(path)`: Saves the file to the specified path
- `file.filename`: The filename from the client
- `file.content_type`: The content type of the file
- `file.stream`: The file stream

**`request.content_length`**
- Returns the length of the request body in bytes
- Type: int or None

**`request.content_type`**
- Returns the content type of the request with parameters
- Type: string

**`request.mimetype`**
- Returns the mimetype of the request without parameters
- Type: string

**`request.mimetype_params`**
- Returns the parameters of the mimetype as a dictionary
- Type: dict

### Headers

**`request.headers`**
- Contains all HTTP headers sent with the request
- Type: EnvironHeaders (dict-like)
- Header names are case-insensitive
- Example:
```python
@app.route('/')
def index():
    user_agent = request.headers.get('User-Agent')
    content_type = request.headers.get('Content-Type')
    return f"User Agent: {user_agent}"
```

**`request.user_agent`**
- Parsed user agent information
- Type: UserAgent object
- Provides properties like `browser`, `platform`, `version`

**`request.referrer`** or **`request.referer`**
- The HTTP Referer header (URL of the referring page)
- Type: string or None

**`request.authorization`**
- The parsed Authorization header
- Type: Authorization object or None
- Contains username and password for HTTP Basic Auth

### Cookies

**`request.cookies`**
- Contains all cookies transmitted with the request
- Type: ImmutableMultiDict
- Example:
```python
@app.route('/')
def index():
    username = request.cookies.get('username')
    return f"Welcome back, {username}"
```

Note: To set cookies, you must use the response object's `set_cookie()` method.

### Remote Information

**`request.remote_addr`**
- The IP address of the client
- Type: string

**`request.remote_user`**
- The username if the server supports user authentication and the script is protected
- Type: string or None

**`request.access_route`**
- If a forwarded header exists, this is a list of all IP addresses from the client to the last proxy server
- Type: list

### Flask-Specific Attributes

**`request.endpoint`**
- The name of the endpoint that matched the request
- Type: string or None
- Returns None if matching failed or has not been performed yet
- Can be used with `view_args` to reconstruct URLs
- Example:
```python
@app.route('/user/<username>')
def user_profile(username):
    print(request.endpoint)  # Prints 'user_profile'
    print(request.view_args)  # Prints {'username': 'john'}
```

**`request.view_args`**
- A dictionary of view arguments that matched the request
- Type: dict or None
- Contains the values extracted from the URL rule
- Returns None if an exception happened during matching

**`request.blueprint`**
- The name of the current blueprint
- Type: string or None
- Returns None if the request is not associated with a blueprint
- For nested blueprints, returns the full path separated by dots

**`request.blueprints`**
- A list of blueprint names in the hierarchy from parent to child
- Type: list
- Returns an empty list if there is no blueprint or URL matching failed

**`request.url_rule`**
- The internal URL rule that matched the request
- Type: Rule object or None
- Useful for inspecting which methods are allowed for the URL

**`request.routing_exception`**
- If matching the URL failed, this is the exception that was raised
- Type: Exception or None
- Usually a NotFound exception or similar

### Environment and Context

**`request.environ`**
- The underlying WSGI environment dictionary
- Type: dict
- Contains all WSGI environment variables

**`request.shallow`**
- True if this is a shallow request object (does not modify environ)
- Type: boolean

**`request.max_content_length`**
- Read-only view of the MAX_CONTENT_LENGTH configuration key
- Type: int or None

**`request.max_form_memory_size`**
- The maximum form field size in bytes
- Type: int
- Default is 500kB

**`request.max_form_parts`**
- The maximum number of multipart parts to parse
- Type: int

### Accept Headers

**`request.accept_mimetypes`**
- List of mimetypes this client supports
- Type: MIMEAccept object
- Example:
```python
@app.route('/')
def index():
    if request.accept_mimetypes.best_match(['application/json', 'text/html']) == 'application/json':
        return jsonify({'message': 'JSON response'})
    return '<h1>HTML response</h1>'
```

**`request.accept_charsets`**
- List of charsets this client supports
- Type: CharsetAccept object

**`request.accept_encodings`**
- List of encodings this client accepts (compression encodings like gzip)
- Type: Accept object

**`request.accept_languages`**
- List of languages this client accepts
- Type: LanguageAccept object

### Cache Control

**`request.cache_control`**
- The Cache-Control header parsed into a RequestCacheControl object
- Type: RequestCacheControl object

**`request.if_match`**
- The If-Match header
- Type: ETags object

**`request.if_none_match`**
- The If-None-Match header
- Type: ETags object

**`request.if_modified_since`**
- The If-Modified-Since header parsed as a datetime
- Type: datetime or None

**`request.if_unmodified_since`**
- The If-Unmodified-Since header parsed as a datetime
- Type: datetime or None

**`request.if_range`**
- The If-Range header
- Type: IfRange object

**`request.range`**
- The Range header
- Type: Range object or None

### Other Headers

**`request.content_encoding`**
- The Content-Encoding header
- Type: string or None

**`request.content_md5`**
- The Content-MD5 header
- Type: string or None

**`request.date`**
- The Date header parsed as a datetime
- Type: datetime or None

**`request.pragma`**
- The Pragma header
- Type: HeaderSet

### Cross-Origin Resource Sharing (CORS)

**`request.access_control_request_headers`**
- Sent with a preflight request to indicate which headers will be used
- Type: HeaderSet

**`request.access_control_request_method`**
- Sent with a preflight request to indicate which method will be used
- Type: string or None

**`request.origin`**
- The Origin header
- Type: string or None

## Common Methods

### Getting Values Safely

When accessing query parameters, form data, or cookies, it is recommended to use the `.get()` method instead of direct dictionary access. This prevents KeyError exceptions if the key does not exist.

```python
# Recommended approach
username = request.args.get('username')  # Returns None if not present
page = request.args.get('page', default=1, type=int)  # Returns 1 if not present

# Not recommended
username = request.args['username']  # Raises KeyError if not present
```

### Getting Multiple Values

When a form or query parameter has multiple values (such as checkboxes), use `.getlist()`:

```python
# For checkboxes or multi-select fields
selected_items = request.form.getlist('items')
# Returns a list of all selected values
```

### Checking Request Type

```python
@app.route('/data', methods=['GET', 'POST'])
def handle_data():
    if request.method == 'POST':
        # Handle POST request
        data = request.form
    else:
        # Handle GET request
        data = request.args
```

### Checking for AJAX Requests

Note: The `request.is_xhr` attribute was deprecated and removed. Modern applications should check headers directly:

```python
if request.headers.get('X-Requested-With') == 'XMLHttpRequest':
    # This is an AJAX request
    pass
```

## Working with File Uploads

To handle file uploads in Flask, the HTML form must include `enctype="multipart/form-data"`:

```html
<form method="POST" enctype="multipart/form-data">
    <input type="file" name="photo">
    <input type="submit">
</form>
```

In the view function:

```python
from werkzeug.utils import secure_filename

@app.route('/upload', methods=['POST'])
def upload():
    if 'photo' not in request.files:
        return 'No file uploaded'
    
    file = request.files['photo']
    
    if file.filename == '':
        return 'No file selected'
    
    if file:
        filename = secure_filename(file.filename)
        file.save(os.path.join('uploads', filename))
        return 'File uploaded successfully'
```

Always use `secure_filename()` to sanitize filenames before saving them to prevent directory traversal attacks.

## Request Context

The request object is only available within the request context. This context is automatically created when Flask handles a request.

If you need to access the request object outside of a request (such as in tests), you can create a test request context:

```python
with app.test_request_context('/hello?name=Flask'):
    print(request.path)  # Prints: /hello
    print(request.args.get('name'))  # Prints: Flask
```

## Request Lifecycle

During request processing, Flask follows this order:

1. Before each request, `before_request()` functions are called
2. The view function for the matched route is called and returns a response
3. The response is passed to `after_request()` functions
4. After the response is returned, `teardown_request()` functions are called

## Security Considerations

1. Always validate and sanitize user input from `request.args`, `request.form`, and `request.json`
2. Use `secure_filename()` when saving uploaded files
3. Check file types and sizes for uploaded files
4. Be cautious with `request.data` and `request.stream` as they can contain arbitrary data
5. Validate JSON data structure before processing
6. Use HTTPS for sensitive data transmission
7. Set appropriate `max_content_length` to prevent DoS attacks

## Best Practices

1. Use `.get()` method instead of direct dictionary access to avoid KeyError
2. Specify default values and types when calling `.get()`: `request.args.get('page', 1, type=int)`
3. Always check if a file exists before processing: `if 'file' in request.files`
4. Use `request.get_json()` instead of the deprecated `request.json` attribute
5. Handle both GET and POST methods appropriately in your routes
6. Validate all user input before processing
7. Use proper HTTP methods for different operations (GET for retrieval, POST for creation, etc.)

## Example: Complete Request Handling

```python
from flask import Flask, request, jsonify
from werkzeug.utils import secure_filename

app = Flask(__name__)

@app.route('/submit', methods=['GET', 'POST'])
def submit_form():
    if request.method == 'POST':
        # Get form data
        name = request.form.get('name')
        email = request.form.get('email')
        
        # Get file upload
        if 'resume' in request.files:
            file = request.files['resume']
            if file.filename != '':
                filename = secure_filename(file.filename)
                file.save(f'uploads/{filename}')
        
        # Get JSON data (if sent as JSON)
        if request.is_json:
            data = request.get_json()
            name = data.get('name')
            email = data.get('email')
        
        # Access headers
        user_agent = request.headers.get('User-Agent')
        
        # Access cookies
        session_id = request.cookies.get('session_id')
        
        # Get client IP
        ip_address = request.remote_addr
        
        # Return JSON response
        return jsonify({
            'status': 'success',
            'name': name,
            'email': email,
            'ip': ip_address
        })
    
    else:
        # GET request - show form
        return '''
            <form method="POST" enctype="multipart/form-data">
                <input type="text" name="name" placeholder="Name">
                <input type="email" name="email" placeholder="Email">
                <input type="file" name="resume">
                <input type="submit">
            </form>
        '''

if __name__ == '__main__':
    app.run(debug=True)
```

---
