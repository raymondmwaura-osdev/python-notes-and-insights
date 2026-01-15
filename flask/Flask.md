# Flask Object Reference Guide

This document provides a comprehensive reference for the methods and properties of the Flask application object.

## Flask Object Overview

The Flask object is the central component of a Flask application. It implements a WSGI application and serves as the registry for view functions, URL rules, template configuration, and application settings. The object is created by instantiating the Flask class with the application's module or package name.

## Constructor

### Flask(import_name, static_url_path=None, static_folder='static', static_host=None, host_matching=False, subdomain_matching=False, template_folder='templates', instance_path=None, instance_relative_config=False, root_path=None)

Creates a Flask application object.

**Parameters:**
- **import_name** (str): The name of the application package or module. Used to locate resources.
- **static_url_path** (str, optional): URL path where static files are served. Defaults to '/static'.
- **static_folder** (str, optional): Folder containing static files. Defaults to 'static'.
- **static_host** (str, optional): Host to use when adding static route.
- **host_matching** (bool, optional): Enable URL host matching. Defaults to False.
- **subdomain_matching** (bool, optional): Enable subdomain matching for routes. Defaults to False.
- **template_folder** (str, optional): Folder containing templates. Defaults to 'templates'.
- **instance_path** (str, optional): Path to instance folder for configuration files.
- **instance_relative_config** (bool, optional): Load config from instance folder. Defaults to False.
- **root_path** (str, optional): Root path of the application.

## Configuration Properties

### config
A dictionary-like object (Config class instance) that holds configuration values. Behaves like a regular dictionary but includes additional methods to load configuration from files.

### secret_key
A string used to encrypt sensitive user data such as sessions and cookies. Must be set for session functionality to work. Can be configured using the SECRET_KEY configuration key. Defaults to None.

### debug
Boolean flag indicating whether debug mode is enabled. When enabled, the development server provides an interactive debugger for unhandled exceptions and reloads when code changes. Maps to the DEBUG config key. Should not be enabled in production.

### testing
Boolean flag that enables test mode for Flask and its extensions. Activates test helpers that may have additional runtime cost. Can be configured using the TESTING configuration key. Defaults to False.

## Path and Folder Properties

### import_name
The name of the package or module that the application belongs to. Set by the constructor and should not be changed afterward.

### root_path
The absolute path to the application's root directory. Used to locate resources.

### instance_path
The absolute path to the instance folder. The instance folder can be used for configuration files that should not be in version control.

### static_folder
The absolute path to the folder containing static files. Returns None if no static folder is configured.

### static_url_path
The URL path where static files are served.

### template_folder
The absolute path to the folder containing templates.

### has_static_folder
Boolean property that returns True if a static folder is configured.

## Routing Methods

### route(rule, **options)
Decorator for registering a view function for a URL rule. This is the most common way to define routes in Flask.

**Parameters:**
- **rule** (str): The URL rule as a string.
- **options**: Additional options passed to the underlying Werkzeug routing system. Common options include methods (list of HTTP methods), defaults, subdomain, and endpoint.

**Example:**
```python
@app.route('/users/<int:user_id>')
def show_user(user_id):
    return f'User {user_id}'
```

### add_url_rule(rule, endpoint=None, view_func=None, **options)
Registers a URL rule without using a decorator. The route() decorator internally calls this method.

**Parameters:**
- **rule** (str): The URL rule as a string.
- **endpoint** (str, optional): The endpoint name for the rule. Defaults to the function name.
- **view_func** (callable, optional): The view function to call for this URL.
- **options**: Additional routing options.

**Example:**
```python
def show_user(user_id):
    return f'User {user_id}'

app.add_url_rule('/users/<int:user_id>', 'show_user', show_user)
```

### get(rule, **options)
Shortcut for route() with methods=["GET"]. Available since Flask 2.0.

### post(rule, **options)
Shortcut for route() with methods=["POST"]. Available since Flask 2.0.

### put(rule, **options)
Shortcut for route() with methods=["PUT"]. Available since Flask 2.0.

### patch(rule, **options)
Shortcut for route() with methods=["PATCH"]. Available since Flask 2.0.

### delete(rule, **options)
Shortcut for route() with methods=["DELETE"]. Available since Flask 2.0.

### endpoint(endpoint)
Decorator to register a function for a given endpoint. Used when a rule is added without a view function.

**Example:**
```python
app.add_url_rule('/example', endpoint='example')

@app.endpoint('example')
def example():
    return 'Example'
```

## Request Lifecycle Methods

### before_request(f)
Decorator that registers a function to run before each request. The function takes no arguments. If the function returns a non-None value, it is treated as the response and the view function is not called.

**Example:**
```python
@app.before_request
def load_user():
    g.user = get_current_user()
```

### after_request(f)
Decorator that registers a function to run after each request. The function must take one parameter (a response object) and return a response object. Not called if an unhandled exception occurs.

**Example:**
```python
@app.after_request
def add_header(response):
    response.headers['X-Custom-Header'] = 'value'
    return response
```

### teardown_request(f)
Decorator that registers a function to run when the request context is popped. Called even if an unhandled exception occurred. The function receives the exception object as an argument if an exception was raised.

**Example:**
```python
@app.teardown_request
def close_db(exception):
    db = getattr(g, 'db', None)
    if db is not None:
        db.close()
```

### teardown_appcontext(f)
Decorator that registers a function to run when the application context ends. Called after teardown_request functions. The function receives the exception object as an argument if an exception was raised.

**Example:**
```python
@app.teardown_appcontext
def cleanup(exception):
    # Cleanup code here
    pass
```

### before_first_request(f)
Decorator that registers a function to run before the first request to the application. Note: This decorator has been deprecated in recent Flask versions.

### context_processor(f)
Decorator that registers a template context processor function. The function should return a dictionary whose values are injected into the template context.

**Example:**
```python
@app.context_processor
def inject_user():
    return dict(user=g.user)
```

## Error Handling Methods

### errorhandler(code_or_exception)
Decorator that registers a function to handle errors by code or exception class.

**Parameters:**
- **code_or_exception**: An HTTP error code (like 404) or an exception class.

**Example:**
```python
@app.errorhandler(404)
def page_not_found(error):
    return 'This page does not exist', 404

@app.errorhandler(ValueError)
def handle_value_error(error):
    return 'Invalid value', 400
```

### register_error_handler(code_or_exception, f)
Non-decorator version of errorhandler(). Registers a function to handle errors.

## Response Methods

### make_response(*args)
Creates a response object from the return value of a view function. Handles various return types including strings, bytes, dictionaries, tuples, and response objects.

**Example:**
```python
response = app.make_response(('Hello', 200, {'X-Custom': 'value'}))
```

### make_default_options_response()
Creates a default OPTIONS response. Called automatically when OPTIONS requests are made to routes that do not explicitly handle them.

### make_null_session()
Creates a new instance of a null session (a session that does not store data). Used when no secret key is configured.

## Testing Methods

### test_client(use_cookies=True, **kwargs)
Creates a test client for the application. Used for testing routes without running a server.

**Example:**
```python
client = app.test_client()
response = client.get('/users')
assert response.status_code == 200
```

### test_cli_runner(**kwargs)
Creates a CLI runner for testing CLI commands. Returns an instance of test_cli_runner_class (by default FlaskCliRunner).

### test_request_context(*args, **kwargs)
Creates a request context for testing. Allows you to test code that depends on request context variables.

**Example:**
```python
with app.test_request_context('/users'):
    assert request.path == '/users'
```

## Context Methods

### app_context()
Creates an application context. Use as a context manager to manually push an application context.

**Example:**
```python
with app.app_context():
    # Code that needs app context
    db.create_all()
```

### request_context(environ)
Creates a request context from a WSGI environment dictionary.

## Template Methods

### create_jinja_environment()
Creates the Jinja2 environment for the application. Called when jinja_env is first accessed. Applies jinja_options and Flask-related globals and filters.

### select_jinja_autoescape(filename)
Returns True if autoescaping should be active for the given template name. Returns True by default.

### update_template_context(context)
Updates the template context with commonly used variables including request, session, config, and g.

### template_filter(name=None)
Decorator that registers a custom template filter function.

**Example:**
```python
@app.template_filter()
def reverse(s):
    return s[::-1]
```

### template_global(name=None)
Decorator that registers a custom template global function.

**Example:**
```python
@app.template_global()
def double(n):
    return 2 * n
```

### template_test(name=None)
Decorator that registers a custom template test function.

## Blueprint Methods

### register_blueprint(blueprint, **options)
Registers a Blueprint with the application. Blueprints allow you to organize your application into modular components.

**Parameters:**
- **blueprint**: The Blueprint object to register.
- **options**: Keyword arguments such as url_prefix, subdomain, or url_defaults.

**Example:**
```python
from flask import Blueprint

users_bp = Blueprint('users', __name__)

@users_bp.route('/')
def list_users():
    return 'User list'

app.register_blueprint(users_bp, url_prefix='/users')
```

## URL Building Methods

### url_for(endpoint, **values)
Generates a URL to the given endpoint with the given values.

**Parameters:**
- **endpoint** (str): The endpoint name (usually the function name).
- **values**: Variable values for the URL rule. Unknown keys are appended as query parameters.

**Example:**
```python
url = app.url_for('show_user', user_id=5)
# Generates: /users/5
```

### build_error_handler(error, endpoint, values)
Called when url_for() raises a BuildError. Can be overridden to customize error handling.

## Application Running Methods

### run(host=None, port=None, debug=None, load_dotenv=True, **options)
Runs the application on a local development server. Should not be used in production.

**Parameters:**
- **host** (str, optional): The hostname to listen on. Defaults to '127.0.0.1'.
- **port** (int, optional): The port to listen on. Defaults to 5000.
- **debug** (bool, optional): Enable debug mode.
- **load_dotenv** (bool, optional): Load environment variables from .env files.
- **options**: Additional options passed to the Werkzeug server.

**Example:**
```python
if __name__ == '__main__':
    app.run(debug=True, port=8000)
```

## Exception Handling Methods

### handle_exception(e)
Handles an exception that occurred during request processing. Returns a response value or re-raises the exception.

### handle_http_exception(e)
Handles HTTP exceptions. Invokes registered error handlers or returns the exception as a response.

### handle_user_exception(e)
Handles exceptions raised by user code during request processing.

### trap_http_exception(e)
Checks if an HTTP exception should be trapped for debugging purposes.

## Processing Methods

### preprocess_request()
Called before the request is dispatched. Runs url_value_preprocessor and before_request functions.

### process_response(response)
Can be overridden to modify the response object before it is sent to the WSGI server. By default, calls all after_request decorated functions.

### do_teardown_request(exc=None)
Called after the request is dispatched and the response is returned. Calls all teardown_request decorated functions.

### do_teardown_appcontext(exc=None)
Called right before the application context is popped. Calls all teardown_appcontext decorated functions.

### full_dispatch_request()
Dispatches the request and performs request preprocessing and postprocessing, as well as HTTP exception catching and error handling.

### dispatch_request()
Matches the URL and returns the return value of the view function or error handler.

## Resource Methods

### open_resource(resource, mode='rb')
Opens a resource from the application's resource folder. The resource path is relative to the root path.

**Example:**
```python
with app.open_resource('schema.sql') as f:
    schema = f.read()
```

### open_instance_resource(resource, mode='rb')
Opens a resource from the application's instance folder. Works like open_resource() but for the instance folder.

## Jinja Environment Properties

### jinja_env
The Jinja2 environment used to load templates. Created when first accessed. Changes to jinja_options after this point have no effect.

### jinja_loader
The Jinja loader for the application's templates. By default, uses FileSystemLoader pointing to template_folder.

### jinja_options
Dictionary of options passed to the Jinja environment in create_jinja_environment(). Changes after the environment is created have no effect.

## URL Map Properties

### url_map
The Map object containing all registered URL rules. This is the underlying Werkzeug routing system.

### url_rule_class
The class used to create URL rule objects. Defaults to Werkzeug's Rule class.

### url_value_preprocessors
Dictionary of functions that can modify URL values before they are passed to view functions.

### url_default_functions
Dictionary of functions that provide default values for URL generation.

## CLI Properties

### cli
The Click command group for registering CLI commands. Commands registered here are available from the flask command after application discovery.

## Extension Properties

### extensions
Dictionary where Flask extensions store application-specific state. Keys match extension module names.

## Session Properties

### session_interface
The session interface to use for creating and opening sessions. Defaults to SecureCookieSessionInterface.

### permanent_session_lifetime
A timedelta representing how long permanent sessions should last. Defaults to 31 days.

## Other Properties

### name
The name of the application. Usually the import name, but can be set to a different value.

### logger
The logger instance for the application. Used for logging application events.

### blueprints
Dictionary mapping registered blueprint names to blueprint objects. Maintains registration order.

### view_functions
Dictionary mapping endpoint names to view functions.

### got_first_request
Boolean indicating whether the application has handled its first request.

## Response Class Properties

### response_class
The class used for response objects. Defaults to Flask's Response class. Can be subclassed to customize response behavior.

### json
JSON provider instance used for handling JSON requests and responses. Can be customized by changing json_provider_class or by assigning a new provider.

## Helper Methods

### send_file(path_or_file, mimetype=None, as_attachment=False, download_name=None, conditional=True, etag=True, last_modified=None, max_age=None)
Sends a file to the client. Handles file serving with proper headers.

### send_static_file(filename)
Sends a file from the static folder to the browser. Used internally by Flask.

### get_send_file_max_age(filename)
Determines the max_age cache value for send_file(). Returns SEND_FILE_MAX_AGE_DEFAULT from configuration or None by default.

### shell_context_processor(f)
Decorator that registers a function to provide variables for the interactive shell context.

**Example:**
```python
@app.shell_context_processor
def make_shell_context():
    return {'db': db, 'User': User}
```

### make_shell_context()
Returns the shell context for an interactive shell. Runs all registered shell context processors.

### inject_url_defaults(endpoint, values)
Injects URL defaults for the given endpoint into the values dictionary. Called automatically during URL building.

### iter_blueprints()
Iterates over all blueprints in the order they were registered.

### create_url_adapter(request)
Creates a URL adapter for the given request. The URL adapter handles URL matching and building.

---
