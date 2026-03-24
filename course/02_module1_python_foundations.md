# Module 1: Python Foundations

**Overview:** Python is the programming language we will use throughout this entire course. This module teaches you only the Python you need for test automation — nothing extra. By the end, you will be comfortable writing small scripts, working with data, and reading/writing files.

---

## Lesson 1.1 — Python Setup & First Script

🎯 **What you will learn:** How to install Python, open a terminal, create a Python file, and run your very first script.

💡 **Simple explanation:** Think of Python as a language that lets you give instructions to your computer. Instead of clicking buttons, you type commands in a text file, and Python reads and executes them line by line. A "terminal" is just a text window where you type commands. A "script" is a file with instructions. That's it — nothing more complex than that.

📋 **Code example:**

Create a file called `hello.py` and type this:

```python
# hello.py — My very first Python script
print("Hello! I am learning test automation.")
print("My name is QA Engineer.")
print("2 + 3 =", 2 + 3)
```

Open your terminal, navigate to the folder where you saved the file, and run:

```bash
python hello.py
```

✅ **Expected result:**

```
Hello! I am learning test automation.
My name is QA Engineer.
2 + 3 = 5
```

📝 **Task:** Create a file called `about_me.py` that prints your name, your job title, and how many years you have been in QA. Run it in the terminal.

✅ **Expected result (example):**

```
Name: Alex
Role: QA Engineer
Years in QA: 3
```

⚠️ **Common mistakes:**

1. **Running `python` instead of `python3` on Mac/Linux.** If `python --version` shows an error, try `python3 --version` and use `python3` instead.
2. **Not saving the file before running it.** Always press Ctrl+S (Cmd+S on Mac) before running.
3. **Creating the file in a different folder than the terminal is in.** Use `cd` to navigate to the right folder first.

---

## Lesson 1.2 — Variables, Data Types, type()

🎯 **What you will learn:** How to store information in variables and understand the basic data types Python uses.

💡 **Simple explanation:** A variable is like a labeled box. You give the box a name (like `username`) and put a value inside it (like `"admin"`). Python has a few types of boxes: text (called "strings"), whole numbers (called "integers"), decimal numbers (called "floats"), and true/false values (called "booleans"). The `type()` function tells you what kind of box you are looking at.

📋 **Code example:**

```python
# variables.py — Understanding data types
username = "admin"
password = "secret123"
login_attempts = 3
is_active = True
response_time = 1.45

print("Username:", username)
print("Password:", password)
print("Login attempts:", login_attempts)
print("Is active:", is_active)
print("Response time:", response_time)

# Check what type each variable is
print("\n--- Data Types ---")
print("username is:", type(username))
print("login_attempts is:", type(login_attempts))
print("is_active is:", type(is_active))
print("response_time is:", type(response_time))
```

✅ **Expected result:**

```
Username: admin
Password: secret123
Login attempts: 3
Is active: True
Response time: 1.45

--- Data Types ---
username is: <class 'str'>
login_attempts is: <class 'int'>
is_active is: <class 'bool'>
response_time is: <class 'float'>
```

📝 **Task:** Create a file called `test_case_info.py`. Define variables for a test case: `test_id` (integer), `test_name` (string), `is_automated` (boolean), and `execution_time` (float). Print all values and their types.

✅ **Expected result:**

```
Test ID: 101
Test Name: Login with valid credentials
Is Automated: False
Execution Time: 2.5
Type of test_id: <class 'int'>
Type of test_name: <class 'str'>
Type of is_automated: <class 'bool'>
Type of execution_time: <class 'float'>
```

⚠️ **Common mistakes:**

1. **Using spaces in variable names.** `test name = "Login"` is wrong. Use underscores: `test_name = "Login"`.
2. **Forgetting quotes around strings.** `name = Admin` will cause an error. Use `name = "Admin"`.
3. **Confusing `=` (assign a value) with `==` (compare two values).** We will cover `==` in Lesson 1.5.

---

## Lesson 1.3 — Lists, Dicts, Loops

🎯 **What you will learn:** How to store multiple values in lists and dictionaries, and how to repeat actions using loops.

💡 **Simple explanation:** A list is like a numbered checklist — it holds multiple items in order. A dictionary (dict) is like a form with labels and values (e.g., "Name: Alex", "Role: QA"). A loop is a way to go through each item one by one automatically, so you don't have to write the same code over and over.

📋 **Code example:**

```python
# collections_and_loops.py

# A list of test statuses
test_results = ["PASS", "FAIL", "PASS", "PASS", "FAIL"]

print("All results:", test_results)
print("First result:", test_results[0])
print("Total tests:", len(test_results))

# Count passes and fails
pass_count = 0
fail_count = 0
for result in test_results:
    if result == "PASS":
        pass_count = pass_count + 1
    else:
        fail_count = fail_count + 1

print(f"Passed: {pass_count}, Failed: {fail_count}")

# A dictionary with test case details
test_case = {
    "id": 1,
    "name": "Login with valid credentials",
    "status": "PASS",
    "duration": 1.2
}

print("\n--- Test Case Details ---")
for key, value in test_case.items():
    print(f"  {key}: {value}")
```

✅ **Expected result:**

```
All results: ['PASS', 'FAIL', 'PASS', 'PASS', 'FAIL']
First result: PASS
Total tests: 5
Passed: 3, Failed: 2

--- Test Case Details ---
  id: 1
  name: Login with valid credentials
  status: PASS
  duration: 1.2
```

📝 **Task:** Create a list of 5 browser names. Use a `for` loop to print each one with its number (starting from 1). Then create a dict representing a bug report with keys: `title`, `severity`, `status`, `assignee`. Print all keys and values.

✅ **Expected result:**

```
Browser 1: Chrome
Browser 2: Firefox
Browser 3: Safari
Browser 4: Edge
Browser 5: Opera

Bug Report:
  title: Button not clickable
  severity: High
  status: Open
  assignee: Alex
```

⚠️ **Common mistakes:**

1. **Forgetting that list indexes start at 0.** `test_results[1]` gives the second item, not the first.
2. **Using parentheses `()` instead of square brackets `[]` for lists.** Lists use `[]`, function calls use `()`.
3. **Misspelling dict keys.** `test_case["naem"]` will crash. Keys must match exactly.

---

## Lesson 1.4 — Functions, Parameters, Return Values

🎯 **What you will learn:** How to create reusable blocks of code called functions, pass information into them, and get results back.

💡 **Simple explanation:** A function is like a recipe. You give it a name (like "make_coffee"), list what ingredients it needs (parameters), and describe the steps. Whenever you want coffee, you just call the recipe by name instead of rewriting all the steps. Functions can also give something back (a "return value") — like the finished coffee.

📋 **Code example:**

```python
# functions.py

def calculate_pass_rate(passed, total):
    """Calculate the pass rate as a percentage."""
    if total == 0:
        return 0.0
    rate = (passed / total) * 100
    return round(rate, 1)


def print_test_summary(suite_name, passed, failed):
    """Print a formatted test summary."""
    total = passed + failed
    rate = calculate_pass_rate(passed, total)
    print(f"\n=== {suite_name} ===")
    print(f"  Total tests: {total}")
    print(f"  Passed: {passed}")
    print(f"  Failed: {failed}")
    print(f"  Pass rate: {rate}%")


# Use the functions
print_test_summary("Login Tests", 8, 2)
print_test_summary("Cart Tests", 5, 0)
print_test_summary("Checkout Tests", 3, 3)
```

✅ **Expected result:**

```
=== Login Tests ===
  Total tests: 10
  Passed: 8
  Failed: 2
  Pass rate: 80.0%

=== Cart Tests ===
  Total tests: 5
  Passed: 5
  Failed: 0
  Pass rate: 100.0%

=== Checkout Tests ===
  Total tests: 6
  Passed: 3
  Failed: 3
  Pass rate: 50.0%
```

📝 **Task:** Write a function called `is_valid_status_code(code)` that takes an HTTP status code (an integer) and returns `True` if it is between 200 and 299, or `False` otherwise. Test it with codes 200, 301, 404, and 500.

✅ **Expected result:**

```
200 -> True
301 -> False
404 -> False
500 -> False
```

⚠️ **Common mistakes:**

1. **Forgetting the `return` statement.** Without `return`, your function gives back `None` instead of the result.
2. **Calling a function without parentheses.** `calculate_pass_rate` refers to the function object. `calculate_pass_rate(8, 10)` actually runs it.
3. **Wrong indentation.** Everything inside a function must be indented by 4 spaces. Python uses indentation to know what belongs to the function.

---

## Lesson 1.5 — if/else, Assertions, Exceptions

🎯 **What you will learn:** How to make decisions in code, verify that something is true, and handle errors gracefully.

💡 **Simple explanation:** `if/else` is how your code makes decisions — like a fork in the road. An "assertion" is a bold statement: "I assert this is true!" If it's not, the program stops and tells you what went wrong — very useful for testing. An "exception" is when something goes wrong at runtime (like dividing by zero). `try/except` lets you catch errors instead of crashing.

📋 **Code example:**

```python
# decisions_and_errors.py

# if/else: making decisions
status_code = 404

if status_code == 200:
    print("Success!")
elif status_code == 404:
    print("Page not found!")
elif status_code >= 500:
    print("Server error!")
else:
    print(f"Unexpected status: {status_code}")

# Assertions: verifying expected results
expected_title = "Dashboard"
actual_title = "Dashboard"

assert actual_title == expected_title, f"Expected '{expected_title}', got '{actual_title}'"
print("Title assertion passed!")

# try/except: handling errors gracefully
def safe_divide(a, b):
    try:
        result = a / b
        return result
    except ZeroDivisionError:
        print(f"Error: Cannot divide {a} by zero!")
        return None

print(safe_divide(10, 3))
print(safe_divide(10, 0))
```

✅ **Expected result:**

```
Page not found!
Title assertion passed!
3.3333333333333335
Error: Cannot divide 10 by zero!
None
```

📝 **Task:** Write a function `validate_login(username, password)` that asserts the username is not empty and the password is at least 6 characters long. Call it with valid data (should pass silently) and with an empty username (should show an AssertionError). Wrap the failing call in try/except to catch and print the error.

✅ **Expected result:**

```
Valid login: OK
Error caught: Username must not be empty!
```

⚠️ **Common mistakes:**

1. **Using `=` instead of `==` in if statements.** `if x = 5` is assignment (and an error). Use `if x == 5` for comparison.
2. **Forgetting the colon `:` after `if`, `elif`, `else`.** `if x == 5` ← missing colon will crash.
3. **Catching too broad an exception.** `except:` catches everything, which hides real bugs. Be specific: `except ValueError:`.

---

## Lesson 1.6 — Files & Folders

🎯 **What you will learn:** How to read data from files, write results to files, and work with JSON — the most common data format in testing.

💡 **Simple explanation:** Files are how your computer stores information permanently. Reading a file means opening it and looking at what's inside. Writing to a file means saving new data. JSON (JavaScript Object Notation) is a popular format that looks like Python dictionaries — it's used everywhere in APIs and test data. Think of it as a universal language for sharing structured data.

📋 **Code example:**

```python
# files_and_json.py
import json

# Writing to a text file
with open("test_log.txt", "w") as file:
    file.write("Test Run: 2024-01-15\n")
    file.write("Status: PASS\n")
    file.write("Duration: 4.5 seconds\n")

print("Log file created!")

# Reading from a text file
with open("test_log.txt", "r") as file:
    content = file.read()
    print("--- Log Contents ---")
    print(content)

# Working with JSON
test_data = {
    "test_suite": "Login Tests",
    "tests": [
        {"name": "Valid login", "status": "PASS"},
        {"name": "Invalid password", "status": "PASS"},
        {"name": "Empty username", "status": "FAIL"}
    ]
}

# Write JSON to file
with open("test_results.json", "w") as file:
    json.dump(test_data, file, indent=2)

print("JSON file created!")

# Read JSON from file
with open("test_results.json", "r") as file:
    loaded_data = json.load(file)

print(f"\nSuite: {loaded_data['test_suite']}")
for test in loaded_data["tests"]:
    print(f"  {test['name']}: {test['status']}")
```

✅ **Expected result:**

```
Log file created!
--- Log Contents ---
Test Run: 2024-01-15
Status: PASS
Duration: 4.5 seconds

JSON file created!

Suite: Login Tests
  Valid login: PASS
  Invalid password: PASS
  Empty username: FAIL
```

📝 **Task:** Create a JSON file called `bugs.json` manually with 3 bug entries (each having `id`, `title`, `severity`). Write a Python script that reads the file and prints each bug in a formatted way.

✅ **Expected result:**

```
Bug #1: Login button missing (Severity: Critical)
Bug #2: Typo in header (Severity: Low)
Bug #3: Slow page load (Severity: Medium)
```

⚠️ **Common mistakes:**

1. **Forgetting `import json`.** You need this line at the top to use `json.load()` and `json.dump()`.
2. **Using `json.load()` vs `json.loads()`.** `load()` reads from a file. `loads()` reads from a string. They are different.
3. **Not using `with open(...)` pattern.** Always use `with` — it automatically closes the file when done.

---

## Mini-Project 1

**Task:** Write a script called `test_report.py` that reads test data from a JSON file and prints a formatted test report.

Create `test_data.json`:

```json
{
  "suite_name": "Smoke Tests",
  "run_date": "2024-01-15",
  "tests": [
    {"id": 1, "name": "Homepage loads", "status": "PASS", "duration": 1.2},
    {"id": 2, "name": "Login works", "status": "PASS", "duration": 2.3},
    {"id": 3, "name": "Search returns results", "status": "FAIL", "duration": 5.1},
    {"id": 4, "name": "Cart adds item", "status": "PASS", "duration": 1.8},
    {"id": 5, "name": "Checkout completes", "status": "FAIL", "duration": 8.0}
  ]
}
```

Create `test_report.py`:

```python
# test_report.py — Mini-Project 1
import json

def load_test_data(filepath):
    with open(filepath, "r") as file:
        return json.load(file)

def calculate_stats(tests):
    total = len(tests)
    passed = sum(1 for t in tests if t["status"] == "PASS")
    failed = total - passed
    total_duration = sum(t["duration"] for t in tests)
    pass_rate = round((passed / total) * 100, 1) if total > 0 else 0
    return {
        "total": total,
        "passed": passed,
        "failed": failed,
        "pass_rate": pass_rate,
        "total_duration": round(total_duration, 1)
    }

def print_report(data, stats):
    print("=" * 50)
    print(f"  TEST REPORT: {data['suite_name']}")
    print(f"  Run Date: {data['run_date']}")
    print("=" * 50)
    print(f"\n{'ID':<6}{'Test Name':<30}{'Status':<10}{'Time(s)':<8}")
    print("-" * 50)
    for test in data["tests"]:
        print(f"{test['id']:<6}{test['name']:<30}{test['status']:<10}{test['duration']:<8}")
    print("-" * 50)
    print(f"\nTotal: {stats['total']} | Passed: {stats['passed']} | Failed: {stats['failed']}")
    print(f"Pass Rate: {stats['pass_rate']}% | Duration: {stats['total_duration']}s")
    print("=" * 50)

# Main execution
data = load_test_data("test_data.json")
stats = calculate_stats(data["tests"])
print_report(data, stats)
```

✅ **Expected result:**

```
==================================================
  TEST REPORT: Smoke Tests
  Run Date: 2024-01-15
==================================================

ID    Test Name                     Status    Time(s)
--------------------------------------------------
1     Homepage loads                PASS      1.2
2     Login works                   PASS      2.3
3     Search returns results        FAIL      5.1
4     Cart adds item                PASS      1.8
5     Checkout completes            FAIL      8.0
--------------------------------------------------

Total: 5 | Passed: 3 | Failed: 2
Pass Rate: 60.0% | Duration: 18.4s
==================================================
```

**Module 1 Checklist:**
- [ ] I can create and run a Python file
- [ ] I understand variables and data types
- [ ] I can use lists, dicts, and loops
- [ ] I can write and call functions
- [ ] I understand if/else, assert, and try/except
- [ ] I can read and write JSON files

[↑ Back to Table of Contents](#table-of-contents)