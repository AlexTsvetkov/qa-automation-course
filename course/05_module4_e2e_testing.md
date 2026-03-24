# Module 4: E2E Testing — Page Object Model

**Overview:** Real-world test automation needs structure. The Page Object Model (POM) is a design pattern that keeps your tests clean, readable, and maintainable. Instead of writing selectors directly in tests, you create "page" classes that represent each page of your application. This module teaches you how professionals organize test code.

---

## Lesson 4.1 — What Is POM and Why

🎯 **What you will learn:** What the Page Object Model pattern is, why it exists, and how it makes your life easier.

💡 **Simple explanation:** Imagine you have 50 tests that all type into the login form. If the login form changes (e.g., the ID changes from `#username` to `#user-name`), you would need to update all 50 tests. With POM, all login locators live in one file — `LoginPage`. You update one file, and all 50 tests are fixed. POM is a map of your website, one class per page.

📋 **Code example — Without POM (messy):**

```python
# test_without_pom.py — Hard to maintain
def test_login(page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")
    page.locator("#password").fill("secret_sauce")
    page.get_by_role("button", name="Login").click()

def test_add_to_cart(page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")  # duplicated!
    page.locator("#password").fill("secret_sauce")     # duplicated!
    page.get_by_role("button", name="Login").click()   # duplicated!
    page.locator("[data-testid='add-to-cart-sauce-labs-backpack']").click()
```

**With POM (clean):**

```python
# test_with_pom.py — Easy to maintain
def test_login(page):
    login_page = LoginPage(page)
    login_page.open()
    login_page.login("standard_user", "secret_sauce")

def test_add_to_cart(page):
    login_page = LoginPage(page)
    login_page.open()
    login_page.login("standard_user", "secret_sauce")
    products_page = ProductsPage(page)
    products_page.add_to_cart("Sauce Labs Backpack")
```

📝 **Task:** Look at 3 of your test files from Module 3. List all the duplicated selectors and actions.

⚠️ **Common mistakes:**

1. **Putting assertions inside Page Objects.** Page Objects perform actions. Tests make assertions.
2. **Creating one giant Page Object for the entire site.** One class per page — LoginPage, ProductsPage, CartPage.
3. **Skipping POM because tests are "simple."** Tests grow fast. Start with POM from the beginning.

---

## Lesson 4.2 — Creating Page Objects

🎯 **What you will learn:** How to create your first Page Object class with locators and action methods.

💡 **Simple explanation:** A Page Object is a Python class that represents one page of your website. It stores all the selectors (locators) for that page and has methods for each action a user can do on that page. The test calls these methods instead of using raw selectors.

📋 **Code example:**

Create the project structure:

```
project/
├── pages/
│   ├── __init__.py
│   ├── login_page.py
│   └── products_page.py
├── tests/
│   ├── conftest.py
│   └── test_login.py
└── pytest.ini
```

`pages/login_page.py`:

```python
# pages/login_page.py
from playwright.sync_api import Page

class LoginPage:
    URL = "https://www.saucedemo.com"

    def __init__(self, page: Page):
        self.page = page
        self.username_field = page.locator("#user-name")
        self.password_field = page.locator("#password")
        self.login_button = page.get_by_role("button", name="Login")
        self.error_message = page.locator("[data-test='error']")

    def open(self):
        self.page.goto(self.URL)
        return self

    def login(self, username: str, password: str):
        self.username_field.fill(username)
        self.password_field.fill(password)
        self.login_button.click()
        return self

    def get_error_text(self) -> str:
        return self.error_message.text_content()
```

`pages/products_page.py`:

```python
# pages/products_page.py
from playwright.sync_api import Page

class ProductsPage:
    def __init__(self, page: Page):
        self.page = page
        self.title = page.locator(".title")
        self.cart_badge = page.locator(".shopping_cart_badge")
        self.cart_link = page.locator(".shopping_cart_link")
        self.inventory_items = page.locator(".inventory_item")

    def get_title_text(self) -> str:
        return self.title.text_content()

    def add_to_cart(self, product_name: str):
        slug = product_name.lower().replace(" ", "-")
        self.page.locator(f"[data-testid='add-to-cart-{slug}']").click()
        return self

    def get_cart_count(self) -> str:
        return self.cart_badge.text_content()

    def go_to_cart(self):
        self.cart_link.click()
        return self

    def get_product_count(self) -> int:
        return self.inventory_items.count()
```

`tests/test_login.py`:

```python
# tests/test_login.py
from playwright.sync_api import Page, expect
from pages.login_page import LoginPage
from pages.products_page import ProductsPage

def test_successful_login(page: Page):
    login_page = LoginPage(page)
    login_page.open()
    login_page.login("standard_user", "secret_sauce")

    products_page = ProductsPage(page)
    expect(products_page.title).to_have_text("Products")

def test_login_with_invalid_credentials(page: Page):
    login_page = LoginPage(page)
    login_page.open()
    login_page.login("invalid_user", "invalid_pass")

    expect(login_page.error_message).to_be_visible()
    expect(login_page.error_message).to_contain_text("Username and password do not match")

def test_login_with_empty_username(page: Page):
    login_page = LoginPage(page)
    login_page.open()
    login_page.login("", "secret_sauce")

    expect(login_page.error_message).to_contain_text("Username is required")
```

✅ **Expected result:** `pytest tests/test_login.py -v --headed` — all 3 pass.

📝 **Task:** Create a `CartPage` class with locators for item names, checkout button, and remove button. Write a test that adds an item, goes to cart, and verifies the item is there.

⚠️ **Common mistakes:**

1. **Importing from wrong path.** Make sure `pages/` has an `__init__.py` file.
2. **Storing text content in `__init__`.** Store locators, not values. Get values in methods.
3. **Making Page Objects too complex.** Keep methods simple — one action per method.

---

## Lesson 4.3 — Test Data & conftest.py

🎯 **What you will learn:** How to manage test data separately and use Pytest fixtures in conftest.py for test setup.

💡 **Simple explanation:** Test data (usernames, passwords, URLs) should not be hardcoded in tests. Put them in fixtures or data files. `conftest.py` is shared setup — fixtures defined there are available to all test files in the same directory.

📋 **Code example:**

`tests/conftest.py`:

```python
# tests/conftest.py
import pytest
from pages.login_page import LoginPage
from pages.products_page import ProductsPage

@pytest.fixture
def login_page(page):
    lp = LoginPage(page)
    lp.open()
    return lp

@pytest.fixture
def logged_in_page(page):
    lp = LoginPage(page)
    lp.open()
    lp.login("standard_user", "secret_sauce")
    return ProductsPage(page)

@pytest.fixture
def test_credentials():
    return {
        "valid": {"username": "standard_user", "password": "secret_sauce"},
        "locked": {"username": "locked_out_user", "password": "secret_sauce"},
        "invalid": {"username": "wrong_user", "password": "wrong_pass"},
    }
```

`tests/test_products.py`:

```python
# tests/test_products.py
from playwright.sync_api import expect

def test_products_page_loaded(logged_in_page):
    expect(logged_in_page.title).to_have_text("Products")

def test_six_products_displayed(logged_in_page):
    assert logged_in_page.get_product_count() == 6

def test_add_item_to_cart(logged_in_page):
    logged_in_page.add_to_cart("Sauce Labs Backpack")
    assert logged_in_page.get_cart_count() == "1"
```

✅ **Expected result:** All tests pass, with clean, readable code.

📝 **Task:** Add a parametrized test that tries to log in with `valid`, `locked`, and `invalid` credentials from the `test_credentials` fixture.

⚠️ **Common mistakes:**

1. **Putting conftest.py in the wrong folder.** It must be in the same directory as (or parent of) your test files.
2. **Fixture scope issues.** Default scope is `function` (fresh for each test). Use `session` for expensive setup.
3. **Hardcoding data in fixtures.** Better to load from JSON/YAML files for complex data.

---

## Lesson 4.4 — Multi-step E2E Test

🎯 **What you will learn:** How to write a full end-to-end test covering a complete user workflow across multiple pages.

💡 **Simple explanation:** An E2E test simulates a real user journey: open app → login → browse → add to cart → checkout → confirm. Each step uses a different Page Object. The test reads like a story of what the user does.

📋 **Code example:**

`pages/cart_page.py`:

```python
# pages/cart_page.py
from playwright.sync_api import Page

class CartPage:
    def __init__(self, page: Page):
        self.page = page
        self.item_names = page.locator(".inventory_item_name")
        self.checkout_button = page.get_by_role("button", name="Checkout")

    def get_item_names(self) -> list:
        return self.item_names.all_text_contents()

    def checkout(self):
        self.checkout_button.click()
        return self
```

`pages/checkout_page.py`:

```python
# pages/checkout_page.py
from playwright.sync_api import Page

class CheckoutPage:
    def __init__(self, page: Page):
        self.page = page
        self.first_name = page.locator("#first-name")
        self.last_name = page.locator("#last-name")
        self.postal_code = page.locator("#postal-code")
        self.continue_button = page.get_by_role("button", name="Continue")
        self.finish_button = page.get_by_role("button", name="Finish")
        self.total_label = page.locator(".summary_total_label")
        self.complete_header = page.locator(".complete-header")

    def fill_info(self, first: str, last: str, postal: str):
        self.first_name.fill(first)
        self.last_name.fill(last)
        self.postal_code.fill(postal)
        self.continue_button.click()
        return self

    def finish(self):
        self.finish_button.click()
        return self
```

`tests/test_e2e_checkout.py`:

```python
# tests/test_e2e_checkout.py
from playwright.sync_api import Page, expect
from pages.login_page import LoginPage
from pages.products_page import ProductsPage
from pages.cart_page import CartPage
from pages.checkout_page import CheckoutPage

def test_full_checkout_flow(page: Page):
    # Login
    login_page = LoginPage(page)
    login_page.open().login("standard_user", "secret_sauce")

    # Add products
    products = ProductsPage(page)
    products.add_to_cart("Sauce Labs Backpack")
    products.add_to_cart("Sauce Labs Bike Light")
    assert products.get_cart_count() == "2"

    # Go to cart and verify
    products.go_to_cart()
    cart = CartPage(page)
    items = cart.get_item_names()
    assert "Sauce Labs Backpack" in items
    assert "Sauce Labs Bike Light" in items

    # Checkout
    cart.checkout()
    checkout = CheckoutPage(page)
    checkout.fill_info("Jane", "Smith", "90210")

    # Verify total and finish
    expect(checkout.total_label).to_contain_text("Total:")
    checkout.finish()

    # Confirm order
    expect(checkout.complete_header).to_have_text("Thank you for your order!")
```

✅ **Expected result:** `pytest tests/test_e2e_checkout.py -v --headed` — 1 test passed.

📝 **Task:** Write an E2E test for the "remove from cart" flow: login → add 2 items → go to cart → remove 1 → verify only 1 remains.

⚠️ **Common mistakes:**

1. **Tests that depend on each other.** Each test must be independent — set up its own state.
2. **Not using page transitions.** Return new Page Objects when navigation happens.
3. **Making tests too long.** If a test has 50+ lines, consider splitting into smaller focused tests.

---

## Lesson 4.5 — Project Structure

🎯 **What you will learn:** How to organize a complete test automation project with proper directory layout, configuration, and dependencies.

💡 **Simple explanation:** A well-organized project has separate folders for pages, tests, test data, and configuration. Anyone joining the team should be able to look at the folder structure and understand where everything lives.

📋 **Code example — Recommended structure:**

```
qa-automation-project/
├── pages/
│   ├── __init__.py
│   ├── base_page.py
│   ├── login_page.py
│   ├── products_page.py
│   ├── cart_page.py
│   └── checkout_page.py
├── tests/
│   ├── conftest.py
│   ├── test_login.py
│   ├── test_products.py
│   ├── test_cart.py
│   └── test_checkout.py
├── test_data/
│   ├── users.json
│   └── products.json
├── pytest.ini
├── requirements.txt
└── README.md
```

`pages/base_page.py`:

```python
# pages/base_page.py
from playwright.sync_api import Page

class BasePage:
    def __init__(self, page: Page):
        self.page = page

    def get_current_url(self) -> str:
        return self.page.url

    def get_page_title(self) -> str:
        return self.page.title()

    def take_screenshot(self, name: str):
        self.page.screenshot(path=f"screenshots/{name}.png")
```

`requirements.txt`:

```
pytest==7.4.0
playwright==1.40.0
pytest-playwright==0.4.3
```

`pytest.ini`:

```ini
[pytest]
markers =
    smoke: Quick essential tests
    regression: Full regression tests
    e2e: End-to-end tests
testpaths = tests
```

✅ **Expected result:** A clean, maintainable project that any team member can understand.

📝 **Task:** Refactor all your tests from this module into the recommended structure. Make sure all tests pass with `pytest tests/ -v --headed`.

⚠️ **Common mistakes:**

1. **Missing `__init__.py` in the pages folder.** Python needs this to treat the folder as a package.
2. **No `requirements.txt`.** Other team members won't know what to install.
3. **Tests outside the `tests/` folder.** Keep them organized.

---

## Mini-Project 4

**Task:** Build a complete POM test suite for SauceDemo covering: login (valid, invalid, locked), products page (count, names, sorting), cart (add, remove), and checkout (complete flow).

Create at least:
- 4 Page Object classes
- 10 tests across 3 test files
- A conftest.py with shared fixtures
- Proper project structure

✅ **Expected result:** `pytest tests/ -v --headed` — all 10+ tests pass.

**Module 4 Checklist:**
- [ ] I understand what POM is and why it helps
- [ ] I can create Page Object classes with locators and methods
- [ ] I use conftest.py for shared fixtures and test data
- [ ] I can write multi-step E2E tests across multiple pages
- [ ] I can organize a project with proper structure

[↑ Back to Table of Contents](#table-of-contents)