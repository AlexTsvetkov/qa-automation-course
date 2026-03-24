# Final Project

**Overview:** This is where everything comes together. You will build a complete, professional test automation project from scratch — combining Python, Pytest, Playwright, Page Object Model, API testing, Git, and CI/CD. This project demonstrates that you can work as a QA Automation Engineer.

---

## Project Brief

**Application Under Test:** SauceDemo (https://www.saucedemo.com)

**Your mission:** Build a production-ready test automation framework that covers the main user flows of the SauceDemo e-commerce application.

---

## Requirements

### 1. Project Structure

```
final-project/
├── .github/
│   └── workflows/
│       └── tests.yml            ← CI/CD pipeline
├── pages/
│   ├── __init__.py
│   ├── base_page.py             ← Base class for all pages
│   ├── login_page.py            ← Login page object
│   ├── products_page.py         ← Products page object
│   ├── cart_page.py             ← Cart page object
│   └── checkout_page.py         ← Checkout page object
├── tests/
│   ├── conftest.py              ← Shared fixtures
│   ├── test_login.py            ← Login tests (UI)
│   ├── test_products.py         ← Products page tests (UI)
│   ├── test_cart.py             ← Cart tests (UI)
│   ├── test_checkout.py         ← E2E checkout tests (UI)
│   └── test_api.py              ← API tests
├── test_data/
│   └── users.json               ← Test data file
├── .gitignore
├── pytest.ini
├── requirements.txt
└── README.md
```

### 2. Page Objects (minimum 4)

Each Page Object must have:
- Locators stored in `__init__`
- Action methods (one action per method)
- Return `self` for method chaining
- No assertions inside page objects

### 3. UI Tests (minimum 15)

| Area | Tests Required |
|------|----------------|
| Login | Valid login, invalid credentials, empty fields, locked user |
| Products | Page loads, correct count, add to cart, remove from cart |
| Cart | Item appears, correct quantity, remove item |
| Checkout | Complete flow, missing info validation, order confirmation |
| Cross-cutting | Sort products, cart badge updates, navigation |

### 4. API Tests (minimum 5)

Using any public API (JSONPlaceholder recommended):
- GET single resource
- GET list of resources
- POST create resource
- Response validation (status, structure, types)
- Parametrized test with multiple inputs

### 5. Test Data

- Store credentials in `test_data/users.json`
- Use fixtures to load test data
- Use `@pytest.mark.parametrize` for data-driven tests

### 6. Configuration

`pytest.ini`:

```ini
[pytest]
markers =
    smoke: Quick essential tests
    regression: Full test suite
    api: API tests
    ui: Browser tests
testpaths = tests
```

### 7. CI/CD

GitHub Actions workflow that:
- Runs on push to main and pull requests
- Installs Python and dependencies
- Installs Playwright browsers
- Runs all tests
- Uploads artifacts on failure

### 8. Git

- Meaningful commit history (minimum 10 commits)
- Feature branches for each module of tests
- At least 1 pull request
- Proper `.gitignore`

### 9. README.md

Must include:
- Project description
- How to install dependencies
- How to run tests
- Project structure explanation
- CI badge

---

## Step-by-Step Guide

### Step 1: Set Up the Project

```bash
mkdir final-project && cd final-project
git init
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install pytest playwright pytest-playwright requests
playwright install chromium
pip freeze > requirements.txt
```

Create `.gitignore`, `pytest.ini`, and empty folders. First commit.

### Step 2: Build Page Objects

Start with `BasePage`, then `LoginPage`, then the rest. Commit after each page object.

### Step 3: Write Login Tests

Write 4 login tests. Use fixtures from `conftest.py`. Commit.

### Step 4: Write Products & Cart Tests

Write tests for products page and cart. Commit.

### Step 5: Write E2E Checkout Test

Write the complete checkout flow test. Commit.

### Step 6: Write API Tests

Create `test_api.py` with 5+ API tests. Commit.

### Step 7: Add Test Data

Create `test_data/users.json`, load it in fixtures, use parametrize. Commit.

### Step 8: Add CI/CD

Create `.github/workflows/tests.yml`. Push to GitHub. Verify it runs.

### Step 9: Write README

Document everything. Add CI badge. Final commit.

---

## Sample Code — Key Files

### `test_data/users.json`:

```json
{
    "valid_user": {
        "username": "standard_user",
        "password": "secret_sauce"
    },
    "locked_user": {
        "username": "locked_out_user",
        "password": "secret_sauce"
    },
    "invalid_user": {
        "username": "wrong_user",
        "password": "wrong_pass"
    }
}
```

### `tests/conftest.py`:

```python
import json
import pytest
from pathlib import Path
from pages.login_page import LoginPage
from pages.products_page import ProductsPage

@pytest.fixture
def test_data():
    data_path = Path(__file__).parent.parent / "test_data" / "users.json"
    with open(data_path) as f:
        return json.load(f)

@pytest.fixture
def login_page(page):
    lp = LoginPage(page)
    lp.open()
    return lp

@pytest.fixture
def logged_in_user(page, test_data):
    lp = LoginPage(page)
    lp.open()
    creds = test_data["valid_user"]
    lp.login(creds["username"], creds["password"])
    return ProductsPage(page)
```

### `README.md` template:

```markdown
# SauceDemo Test Automation

![Tests](https://github.com/YOUR-USERNAME/final-project/actions/workflows/tests.yml/badge.svg)

Automated test suite for [SauceDemo](https://www.saucedemo.com) built with Python, Pytest, and Playwright.

## Setup

\```bash
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
playwright install chromium
\```

## Run Tests

\```bash
# All tests
pytest tests/ -v

# UI tests only (with browser visible)
pytest tests/ -v --headed -m ui

# API tests only
pytest tests/ -v -m api

# Smoke tests
pytest tests/ -v -m smoke
\```

## Project Structure

- `pages/` — Page Object classes
- `tests/` — Test files
- `test_data/` — Test data (JSON)
- `.github/workflows/` — CI/CD pipeline

## Technologies

- Python 3.11
- Pytest
- Playwright
- GitHub Actions
```

---

## Grading Criteria

| Criteria | Points |
|----------|--------|
| Project structure is clean and organized | 10 |
| 4+ Page Objects with proper design | 15 |
| 15+ UI tests covering all required areas | 25 |
| 5+ API tests with validation | 10 |
| Test data externalized, fixtures used | 10 |
| CI/CD pipeline works | 10 |
| Git history is clean (10+ commits, branches) | 10 |
| README is complete | 5 |
| All tests pass | 5 |
| **Total** | **100** |

---

## 🎉 Congratulations!

If you've completed this project, you have built a real-world test automation framework from scratch. You now have:

- **Python** skills for scripting and automation
- **Pytest** expertise for test organization
- **Playwright** ability for browser automation
- **Page Object Model** understanding for maintainable code
- **API testing** skills with the requests library
- **AI tool** experience for accelerating your work
- **Git** knowledge for version control
- **CI/CD** setup for automated test execution

You are ready to start working as a **QA Automation Engineer**. 🚀

[↑ Back to Table of Contents](#table-of-contents)