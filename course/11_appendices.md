# Appendices

---

## Appendix A — Environment Setup Guide

### Step 1: Install Python 3.11+

**macOS:**

```bash
# Install Homebrew (if not installed)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Python
brew install python@3.11

# Verify
python3 --version
# Python 3.11.x
```

**Windows:**

1. Go to https://www.python.org/downloads/
2. Download Python 3.11.x
3. **Important:** Check "Add Python to PATH" during installation
4. Open Command Prompt and verify:

```cmd
python --version
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt update
sudo apt install python3.11 python3.11-venv python3-pip
python3.11 --version
```

### Step 2: Install VS Code

1. Download from https://code.visualstudio.com/
2. Install recommended extensions:
   - Python (by Microsoft)
   - Pylance
   - Playwright Test for VS Code
   - GitLens

### Step 3: Create a Virtual Environment

```bash
# Create project folder
mkdir qa-automation-course
cd qa-automation-course

# Create virtual environment
python3 -m venv venv

# Activate it
# macOS/Linux:
source venv/bin/activate
# Windows:
venv\Scripts\activate

# You should see (venv) in your terminal prompt
```

### Step 4: Install Packages

```bash
pip install pytest playwright pytest-playwright requests
playwright install
```

### Step 5: Install Git

**macOS:**

```bash
brew install git
```

**Windows:**

Download from https://git-scm.com/download/win

**Linux:**

```bash
sudo apt install git
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Step 6: Create a GitHub Account

1. Go to https://github.com
2. Sign up for a free account
3. Set up SSH key (recommended): https://docs.github.com/en/authentication/connecting-to-github-with-ssh

### Verification Checklist

Run these commands to verify everything works:

```bash
python3 --version          # Python 3.11+
pip --version              # pip installed
pytest --version           # pytest installed
playwright --version       # playwright installed
git --version              # git installed
code --version             # VS Code installed (optional)
```

---

## Appendix B — Cheat Sheets

### Python Cheat Sheet

```python
# Variables
name = "Alice"              # string
age = 25                    # integer
price = 19.99               # float
is_active = True            # boolean

# Strings
greeting = f"Hello, {name}!"           # f-string
upper = name.upper()                    # "ALICE"
contains = "Ali" in name                # True

# Lists
items = ["apple", "banana", "cherry"]
items.append("date")                    # add item
first = items[0]                        # "apple"
count = len(items)                      # 4

# Dictionaries
user = {"name": "Alice", "age": 25}
user["email"] = "alice@test.com"        # add key
name = user["name"]                     # "Alice"
has_key = "email" in user               # True

# Conditionals
if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")

# Loops
for item in items:
    print(item)

for i in range(5):        # 0, 1, 2, 3, 4
    print(i)

# Functions
def add(a, b):
    return a + b

result = add(3, 5)        # 8

# Classes
class Dog:
    def __init__(self, name):
        self.name = name

    def bark(self):
        return f"{self.name} says Woof!"

dog = Dog("Rex")
print(dog.bark())          # "Rex says Woof!"
```

### Pytest Cheat Sheet

```python
# Basic test
def test_example():
    assert 1 + 1 == 2

# Fixture
import pytest

@pytest.fixture
def user():
    return {"name": "Alice", "age": 25}

def test_user_name(user):
    assert user["name"] == "Alice"

# Parametrize
@pytest.mark.parametrize("a, b, expected", [
    (1, 2, 3),
    (5, 5, 10),
    (0, 0, 0),
])
def test_add(a, b, expected):
    assert a + b == expected

# Markers
@pytest.mark.smoke
def test_critical():
    assert True

# Expected exception
def test_division_by_zero():
    with pytest.raises(ZeroDivisionError):
        1 / 0
```

```bash
# Run commands
pytest                          # run all tests
pytest test_file.py             # run one file
pytest -v                       # verbose output
pytest -k "login"               # run tests matching "login"
pytest -m smoke                 # run tests with @pytest.mark.smoke
pytest --headed                 # show browser (Playwright)
pytest -x                       # stop on first failure
pytest --tb=short               # shorter tracebacks
```

### Playwright Cheat Sheet

```python
from playwright.sync_api import Page, expect

# Navigation
page.goto("https://example.com")
page.go_back()
page.reload()

# Locators
page.locator("#id")                          # by ID
page.locator(".class")                       # by class
page.locator("input[name='email']")          # by attribute
page.get_by_role("button", name="Submit")    # by role
page.get_by_text("Click me")                 # by text
page.get_by_placeholder("Enter email")       # by placeholder

# Actions
page.locator("#input").fill("text")          # type text
page.locator("#input").clear()               # clear field
page.locator("#btn").click()                 # click
page.locator("#link").hover()                # hover
page.locator("select").select_option("val")  # dropdown
page.keyboard.press("Enter")                 # keyboard

# Assertions
expect(page).to_have_title("Title")
expect(page).to_have_url("https://...")
expect(locator).to_be_visible()
expect(locator).to_have_text("text")
expect(locator).to_contain_text("partial")
expect(locator).to_have_count(5)
expect(locator).to_be_enabled()
expect(locator).to_be_checked()

# Screenshots & video
page.screenshot(path="shot.png")
page.screenshot(path="full.png", full_page=True)

# Waits
page.wait_for_selector(".loaded")
page.wait_for_url("**/dashboard")
page.wait_for_load_state("networkidle")
```

### Git Cheat Sheet

```bash
# Setup
git init                              # create repo
git clone URL                         # copy repo

# Daily workflow
git status                            # check changes
git add .                             # stage all
git add file.py                       # stage one file
git commit -m "message"               # save snapshot
git push origin branch-name           # upload

# Branches
git branch                            # list branches
git checkout -b feature/name          # create + switch
git checkout main                     # switch to main
git merge feature/name                # merge branch
git branch -d feature/name            # delete branch

# Remote
git remote add origin URL             # connect to GitHub
git push -u origin main               # first push
git pull origin main                  # download changes

# History
git log --oneline                     # short history
git diff                              # see changes
```

### Requests (API) Cheat Sheet

```python
import requests

# GET
response = requests.get("https://api.example.com/users")
response = requests.get(url, params={"page": 1})

# POST
response = requests.post(url, json={"name": "Alice"})

# PUT / PATCH / DELETE
response = requests.put(url, json=data)
response = requests.patch(url, json=data)
response = requests.delete(url)

# Response
response.status_code       # 200, 404, etc.
response.json()            # parse JSON body
response.headers           # response headers
response.elapsed           # response time
response.text              # raw text body
```

---

## Appendix C — Useful Links

### Official Documentation
- **Python:** https://docs.python.org/3/
- **Pytest:** https://docs.pytest.org/
- **Playwright for Python:** https://playwright.dev/python/
- **Requests:** https://requests.readthedocs.io/
- **Git:** https://git-scm.com/doc
- **GitHub Actions:** https://docs.github.com/en/actions

### Practice Websites (for test automation)
- **SauceDemo:** https://www.saucedemo.com — E-commerce demo (primary course app)
- **The Internet:** https://the-internet.herokuapp.com — Various UI testing scenarios
- **JSONPlaceholder:** https://jsonplaceholder.typicode.com — Fake REST API
- **Reqres:** https://reqres.in — Another fake REST API
- **DemoQA:** https://demoqa.com — UI elements practice
- **Automation Exercise:** https://automationexercise.com — Full e-commerce site

### Learning Resources
- **Python Tutorial:** https://www.learnpython.org/
- **Real Python:** https://realpython.com/
- **Playwright Getting Started:** https://playwright.dev/python/docs/intro
- **Git Tutorial:** https://learngitbranching.js.org/

### AI Tools
- **ChatGPT:** https://chat.openai.com
- **Claude:** https://claude.ai
- **GitHub Copilot:** https://github.com/features/copilot

---

## Appendix D — Glossary

| Term | Definition |
|------|-----------|
| **API** | Application Programming Interface — a way for programs to communicate with each other |
| **Assert** | A statement that checks if something is true; the test fails if it's false |
| **Async** | Asynchronous — code that can pause and resume, allowing other code to run in between |
| **Branch** | A parallel version of your code in Git, used for developing features independently |
| **CI/CD** | Continuous Integration / Continuous Delivery — automatically testing and deploying code |
| **CLI** | Command Line Interface — a text-based way to interact with your computer |
| **Clone** | Making a copy of a Git repository from a remote server to your local machine |
| **Commit** | A saved snapshot of your code at a point in time in Git |
| **CSS Selector** | A pattern used to select HTML elements (e.g., `#id`, `.class`, `tag`) |
| **Dictionary** | A Python data type that stores key-value pairs: `{"name": "Alice"}` |
| **E2E** | End-to-End — testing the complete user flow from start to finish |
| **Element** | A component on a web page (button, input field, link, text, image) |
| **Endpoint** | A specific URL in an API that performs a specific function |
| **Fixture** | In Pytest, a function that provides setup/teardown for tests |
| **Framework** | A structured set of tools and conventions for building something |
| **Function** | A reusable block of code that performs a specific task |
| **GET** | An HTTP method for requesting/reading data from a server |
| **Git** | A version control system that tracks changes to files |
| **GitHub** | A website for hosting Git repositories and collaborating on code |
| **GitHub Actions** | GitHub's built-in CI/CD service |
| **Headless** | Running a browser without showing its window (invisible, faster) |
| **HTTP** | HyperText Transfer Protocol — the language web browsers and servers use to communicate |
| **IDE** | Integrated Development Environment — a code editor with extra features (VS Code, PyCharm) |
| **iframe** | An HTML element that embeds another web page inside the current page |
| **JSON** | JavaScript Object Notation — a text format for storing data: `{"key": "value"}` |
| **Library** | A collection of pre-written code that you can use in your programs |
| **List** | A Python data type that stores ordered items: `[1, 2, 3]` |
| **Locator** | In Playwright, a way to find an element on a web page |
| **Merge** | Combining changes from one Git branch into another |
| **Method** | A function that belongs to a class |
| **Module** | A Python file containing functions, classes, or variables |
| **Page Object Model** | A design pattern where each web page is represented by a class |
| **Parametrize** | Running the same test with different input data |
| **pip** | Python's package installer — installs libraries from the internet |
| **Playwright** | A browser automation library by Microsoft |
| **POM** | Page Object Model (see above) |
| **POST** | An HTTP method for sending data to create a new resource |
| **Pull Request** | A request to merge your code changes into the main branch, allowing review |
| **Push** | Uploading your local Git commits to a remote repository (GitHub) |
| **Pytest** | A testing framework for Python |
| **Repository** | A Git project — a folder whose history is tracked by Git |
| **REST API** | A common style for building APIs using HTTP methods (GET, POST, PUT, DELETE) |
| **Selector** | A string pattern used to identify an element on a web page |
| **Status Code** | A number returned by an API indicating success (200), not found (404), error (500), etc. |
| **Sync** | Synchronous — code that runs line by line, each line waiting for the previous to finish |
| **Terminal** | A text-based interface for running commands on your computer |
| **Test Case** | A specific scenario to test, with steps and expected results |
| **Test Suite** | A collection of test cases |
| **Timeout** | The maximum time to wait for something before giving up |
| **Variable** | A named container that stores a value: `name = "Alice"` |
| **venv** | Virtual environment — an isolated Python installation for your project |
| **YAML** | A text format for configuration files, used by GitHub Actions |

[↑ Back to Table of Contents](#table-of-contents)