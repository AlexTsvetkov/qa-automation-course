# Module 2: Pytest — Test Framework

**Overview:** Pytest is the most popular testing framework for Python. It finds your test files automatically, gives you powerful assertion messages, and has features like fixtures (setup/cleanup code) and parametrize (run one test with different data). This module teaches you how to write proper, organized tests.

---

## Lesson 2.1 — What Is Pytest

🎯 **What you will learn:** What Pytest is, how to install it, and how it automatically discovers and runs your test files.

💡 **Simple explanation:** Pytest is a tool that runs your test code and tells you what passed and what failed. Instead of running files manually with `python`, you run `pytest` and it automatically finds every file that starts with `test_` and every function that starts with `test_`. Think of it as an organized assistant that finds all your tests, runs them, and gives you a clean report.

📋 **Code example:**

First, install Pytest:

```bash
pip install pytest
```

Create a file called `test_math.py`:

```python
# test_math.py — My first Pytest file

def test_addition():
    assert 2 + 3 == 5

def test_subtraction():
    assert 10 - 4 == 6

def test_multiplication():
    assert 3 * 7 == 21

def test_division():
    assert 10 / 2 == 5.0
```

Run it:

```bash
pytest test_math.py -v
```

✅ **Expected result:**

```
test_math.py::test_addition PASSED
test_math.py::test_subtraction PASSED
test_math.py::test_multiplication PASSED
test_math.py::test_division PASSED

========================= 4 passed in 0.01s =========================
```

📝 **Task:** Create `test_strings.py` with 3 tests: one that checks `"hello".upper()` equals `"HELLO"`, one that checks `"Python".lower()` equals `"python"`, and one that checks `len("test")` equals `4`. Run with `pytest -v`.

✅ **Expected result:**

```
test_strings.py::test_upper PASSED
test_strings.py::test_lower PASSED
test_strings.py::test_length PASSED
```

⚠️ **Common mistakes:**

1. **File name doesn't start with `test_`.** Pytest won't find `my_tests.py`. Name it `test_my_tests.py`.
2. **Function name doesn't start with `test_`.** `def check_login():` won't be discovered. Use `def test_login():`.
3. **Forgetting to install Pytest.** Run `pip install pytest` first. Check with `pytest --version`.

---

## Lesson 2.2 — Writing Test Functions

🎯 **What you will learn:** How to write meaningful test assertions with clear error messages that help you debug failures.

💡 **Simple explanation:** A good test does three things: it sets up some data, it performs an action, and it checks the result. The `assert` statement is how you check. If the check fails, Pytest shows you exactly what went wrong — what you expected and what you actually got. Adding a custom message after the assert makes errors even clearer.

📋 **Code example:**

```python
# test_login_validation.py

def validate_email(email):
    """Simple email validation: must contain @ and a dot after @."""
    if "@" not in email:
        return False
    parts = email.split("@")
    if len(parts) != 2:
        return False
    if "." not in parts[1]:
        return False
    return True

def test_valid_email():
    result = validate_email("user@example.com")
    assert result is True, "Standard email should be valid"

def test_email_without_at():
    result = validate_email("userexample.com")
    assert result is False, "Email without @ should be invalid"

def test_email_without_domain_dot():
    result = validate_email("user@examplecom")
    assert result is False, "Email without dot in domain should be invalid"

def test_empty_email():
    result = validate_email("")
    assert result is False, "Empty email should be invalid"
```

✅ **Expected result (run `pytest test_login_validation.py -v`):**

```
test_login_validation.py::test_valid_email PASSED
test_login_validation.py::test_email_without_at PASSED
test_login_validation.py::test_email_without_domain_dot PASSED
test_login_validation.py::test_empty_email PASSED
```

📝 **Task:** Write a function `calculate_discount(price, percentage)` that returns the discounted price. Write 4 tests: 10% off $100, 50% off $200, 0% off $50, and 100% off $75. Include assertion messages.

✅ **Expected result:** All 4 tests pass.

⚠️ **Common mistakes:**

1. **Not including assertion messages.** `assert x == 5` is ok, but `assert x == 5, f"Expected 5, got {x}"` is much better for debugging.
2. **Testing multiple things in one test.** Each test function should verify one specific behavior.
3. **Comparing floats directly.** `assert 0.1 + 0.2 == 0.3` fails due to floating-point math. Use `assert abs(result - expected) < 0.01`.

---

## Lesson 2.3 — Fixtures

🎯 **What you will learn:** How to use fixtures to set up data or resources that your tests need, and clean them up afterwards.

💡 **Simple explanation:** Imagine you need to set a table before every meal and clear it afterwards. A "fixture" does exactly that for tests — it prepares something your test needs (like test data or a database connection) and cleans up when the test is done. `conftest.py` is a special file where you put fixtures that many test files can share.

📋 **Code example:**

Create `conftest.py`:

```python
# conftest.py — Shared fixtures
import pytest

@pytest.fixture
def sample_user():
    """Provides a sample user dict for tests."""
    return {
        "username": "testuser",
        "password": "SecurePass123",
        "email": "test@example.com"
    }

@pytest.fixture
def empty_cart():
    """Provides an empty shopping cart."""
    cart = []
    yield cart  # This is where the test runs
    cart.clear()  # Cleanup after test
    print("\nCart cleaned up!")
```

Create `test_user.py`:

```python
# test_user.py — Tests using fixtures

def test_username_is_set(sample_user):
    assert sample_user["username"] == "testuser"

def test_password_length(sample_user):
    assert len(sample_user["password"]) >= 8, "Password must be at least 8 characters"

def test_email_format(sample_user):
    assert "@" in sample_user["email"], "Email must contain @"

def test_add_to_cart(empty_cart):
    empty_cart.append({"item": "Laptop", "price": 999})
    assert len(empty_cart) == 1
    assert empty_cart[0]["item"] == "Laptop"
```

✅ **Expected result (run `pytest test_user.py -v`):**

```
test_user.py::test_username_is_set PASSED
test_user.py::test_password_length PASSED
test_user.py::test_email_format PASSED
test_user.py::test_add_to_cart PASSED
```

📝 **Task:** Create a fixture called `sample_product` that returns a dict with `name`, `price`, and `in_stock` (boolean). Write 3 tests that verify each field. Put the fixture in `conftest.py`.

✅ **Expected result:** All 3 tests pass.

⚠️ **Common mistakes:**

1. **Forgetting `@pytest.fixture` decorator.** Without it, Pytest treats it as a regular function.
2. **Naming the fixture parameter differently than the function.** The parameter name in your test must match the fixture function name exactly.
3. **Using `return` when you need cleanup.** Use `yield` instead of `return` if you need to run cleanup code after the test.

---

## Lesson 2.4 — Parametrize

🎯 **What you will learn:** How to run the same test with many different inputs using `@pytest.mark.parametrize`, avoiding duplicate test code.

💡 **Simple explanation:** Imagine testing a login form. You want to try "admin"/"password", "user"/"12345", and ""/"". Instead of writing 3 separate test functions (which would be mostly the same), you write ONE test and give it a list of inputs. Pytest runs the test once for each set of inputs. This is called "parametrization."

📋 **Code example:**

```python
# test_status_codes.py
import pytest

def get_status_message(code):
    """Return a human-readable message for HTTP status codes."""
    messages = {
        200: "OK",
        201: "Created",
        400: "Bad Request",
        401: "Unauthorized",
        404: "Not Found",
        500: "Internal Server Error"
    }
    return messages.get(code, "Unknown")

@pytest.mark.parametrize("code, expected_message", [
    (200, "OK"),
    (201, "Created"),
    (400, "Bad Request"),
    (401, "Unauthorized"),
    (404, "Not Found"),
    (500, "Internal Server Error"),
    (999, "Unknown"),
])
def test_status_message(code, expected_message):
    result = get_status_message(code)
    assert result == expected_message, f"Code {code}: expected '{expected_message}', got '{result}'"
```

✅ **Expected result (run `pytest test_status_codes.py -v`):**

```
test_status_codes.py::test_status_message[200-OK] PASSED
test_status_codes.py::test_status_message[201-Created] PASSED
test_status_codes.py::test_status_message[400-Bad Request] PASSED
test_status_codes.py::test_status_message[401-Unauthorized] PASSED
test_status_codes.py::test_status_message[404-Not Found] PASSED
test_status_codes.py::test_status_message[500-Internal Server Error] PASSED
test_status_codes.py::test_status_message[999-Unknown] PASSED
```

📝 **Task:** Write a parametrized test for a `is_valid_password(password)` function. Test at least 5 cases: valid password, too short, no digits, no letters, empty string.

✅ **Expected result:** 5 test cases, all passing.

⚠️ **Common mistakes:**

1. **Mismatching parameter names.** The names in the string `"code, expected_message"` must match the function parameter names exactly.
2. **Forgetting to import pytest.** You need `import pytest` to use `@pytest.mark.parametrize`.
3. **Wrong number of values in tuples.** If you declare 2 parameters, each tuple must have exactly 2 values.

---

## Lesson 2.5 — Markers & Groups

🎯 **What you will learn:** How to tag tests with markers so you can run specific groups of tests, like only smoke tests or only slow tests.

💡 **Simple explanation:** Markers are like labels or tags on your tests. You might label some tests "smoke" (quick essential tests), some "regression" (thorough tests), and some "slow." Then you can tell Pytest: "Only run the smoke tests" or "Skip the slow ones." This is very useful when you have hundreds of tests and don't want to run them all every time.

📋 **Code example:**

Create `pytest.ini`:

```ini
[pytest]
markers =
    smoke: Quick essential tests
    regression: Thorough regression tests
    slow: Tests that take a long time
```

Create `test_tagged.py`:

```python
# test_tagged.py
import pytest

@pytest.mark.smoke
def test_homepage_loads():
    assert True, "Homepage should load"

@pytest.mark.smoke
def test_login_page_loads():
    assert True, "Login page should load"

@pytest.mark.regression
def test_search_functionality():
    assert True, "Search should work"

@pytest.mark.regression
def test_filter_products():
    assert True, "Filters should work"

@pytest.mark.slow
def test_full_checkout_flow():
    assert True, "Full checkout should complete"
```

Run only smoke tests:

```bash
pytest test_tagged.py -v -m smoke
```

✅ **Expected result:**

```
test_tagged.py::test_homepage_loads PASSED
test_tagged.py::test_login_page_loads PASSED

========================= 2 passed, 3 deselected in 0.01s =========================
```

📝 **Task:** Add a `@pytest.mark.skip(reason="Not implemented yet")` marker to one test. Add a `@pytest.mark.xfail` marker to another (expected to fail). Run all tests and observe the output.

✅ **Expected result:** You see `s` for skipped and `x` for expected failure in the output.

⚠️ **Common mistakes:**

1. **Not registering markers in `pytest.ini`.** Unregistered markers generate warnings. Always register them.
2. **Using `-m` with wrong syntax.** `pytest -m "smoke and not slow"` is valid. `pytest -m smoke,regression` is not.
3. **Confusing `skip` and `xfail`.** `skip` means "don't run this." `xfail` means "run it, but I expect it to fail."

---

## Mini-Project 2

**Task:** Write a parametrized test suite for a login form validation function.

```python
# login_validator.py

def validate_login(username, password):
    """Validate login credentials. Returns (is_valid, error_message)."""
    if not username:
        return False, "Username is required"
    if len(username) < 3:
        return False, "Username must be at least 3 characters"
    if not password:
        return False, "Password is required"
    if len(password) < 6:
        return False, "Password must be at least 6 characters"
    if not any(c.isdigit() for c in password):
        return False, "Password must contain at least one digit"
    return True, "Login successful"
```

```python
# test_login_form.py
import pytest
from login_validator import validate_login

@pytest.mark.parametrize("username, password, expected_valid, expected_message", [
    ("admin", "Pass123", True, "Login successful"),
    ("", "Pass123", False, "Username is required"),
    ("ab", "Pass123", False, "Username must be at least 3 characters"),
    ("admin", "", False, "Password is required"),
    ("admin", "Pass", False, "Password must be at least 6 characters"),
    ("admin", "Password", False, "Password must contain at least one digit"),
    ("testuser", "Secure99", True, "Login successful"),
])
def test_login_validation(username, password, expected_valid, expected_message):
    is_valid, message = validate_login(username, password)
    assert is_valid == expected_valid, f"Expected valid={expected_valid} for ({username}, {password})"
    assert message == expected_message, f"Expected message='{expected_message}', got '{message}'"
```

✅ **Expected result:** All 7 parametrized test cases pass.

**Module 2 Checklist:**
- [ ] I can install and run Pytest
- [ ] I can write test functions with meaningful assertions
- [ ] I can create and use fixtures and conftest.py
- [ ] I can use parametrize to test multiple data sets
- [ ] I can tag tests with markers and run subsets

[↑ Back to Table of Contents](#table-of-contents)