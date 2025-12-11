# Universal Coding Style Guide (Language-Agnostic)

## 1. Naming Conventions
Use the same naming approach everywhere.

### Variables & functions -> `snake_case`
Works naturally in C, C++, Python, JS, shell, etc.
Java prefers camelCase, but snake_case still compiles and is readable.
```c
int max_value = 0;
void compute_hash();
```
```python
user_name = "John Doe"
def calculate_total(): ...
```

### Classes & Types -> `PascalCase`
Works for C++ classes, Python classes, Java classes, and even TypeScript/JS.
```cpp
class DataManager { ... };
```
```python
class UserProfile: ...
```

### Constants -> `SCREAMING_SNAKE_CASE`
Matches C/C++, Python, and Java static finals (converted automatically by Java style-checkers).
```c
#define MAX_BUFFER 1024
```
```python
PI_VALUE = 3.14159
```

### Filenames -> `snake_case`
Consistent across Linux/Windows/macOS, Python modules, C headers, etc.
```bash
data_manager.cpp
user_profile.py
index.html
style.css
```

## 2. Indentation & Formatting

### Use 4 spaces for indentation (no tabs)
It is recommended since spaces produce idential indentation for every viewer, in every editor, on every machine. Every language accepts it; avoids tab-width hell at cost of marginal project size increase. Enable `Tab inserts spaces` (a.k.a. soft tabs) in editor.

### Brace Style -> “One True Brace Style” (1TBS)
This is the most universal, readable choice:
```c
if (x > 0) {
    do_something();
} else {
    handle_error();
}
```
Works cleanly in C, C++, Java, JS, PHP, Rust.

### Line length: ~100-120 characters
Comfortable across editors, terminals, Git diffs.

### Put spaces around operators
```c
a = b + c;
```

## 3. Comments
Use consistent comment formatting.

### Use `//` (`#` for python) for single-line comments everywhere
Works in C, C++, Java, JS, PHP, Rust, Go.

### Use `/* … */` only for block comments
```c
/*
 * Multi-line comment block
 */
```

### Docstrings/Javadoc for functions & classes
All languages support a variant:
```python
def add(x, y):
    """Return sum of x and y."""
    return x + y
```
```cpp
/// C/C++ (Doxygen style)
```

## 4. General Structure and Patterns

### One responsibility per file
Avoid God-objects and God-files.

### Functions: keep them small
A universal standard across all languages.

### Prefer descriptive naming over brevity
```python
def load_user_settings()
```

### Use a consistent project layout
- `src/` -> main code (core application logic)
- `include/` -> headers
- `tests/` -> unit tests
- `docs/` -> documentation
- `scripts/` -> Python/shell tools (automating and running tasks)
Works for C, C++, Python, Java, etc.
```graphql
my_python_project/
│
├── src/                       # Core Python code
│   ├── __init__.py            # Marks this folder as a package
│   ├── app.py                 # Main entry point of the app
│   └── user.py                # User model or logic
│   └── utils/                 # Any helper code
│       ├── __init__.py
│       └── validators.py      # Code to validate user input
│
├── scripts/                   # Utility, one-off scripts
│   ├── run_app.py             # Entry point to run the app
│   ├── deploy.py              # Deployment automation
│   ├── generate_reports.py    # Tool to generate reports
│   └── setup_env.py           # Environment setup automation
│
└── tests/                     # Tests for your app's logic
    ├── test_app.py
    └── test_user.py
```
```python
# src/user.py
class User:
    def __init__(self, username, password):
        self.username = username
        self.password = password

    def authenticate(self):
        # Logic to authenticate the user
        pass
```
```python
# scripts/run_server.py
from src.app import start_server

if __name__ == "__main__":
    start_server()
```
In this case, `scripts/` is about running or interacting with `src/` code but isn’t core to the app’s functionality.