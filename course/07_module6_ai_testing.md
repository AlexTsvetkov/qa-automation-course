# Module 6: AI-Powered Testing

**Overview:** AI tools like ChatGPT, GitHub Copilot, and Claude can dramatically speed up your test automation work. They can generate test code, explain errors, create test data, and suggest what to test. This module teaches you how to use AI as your assistant — not a replacement — by writing effective prompts and validating the output.

---

## Lesson 6.1 — AI Tools for QA

🎯 **What you will learn:** What AI tools are available for QA engineers and what each one is best at.

💡 **Simple explanation:** AI tools are like very fast, very knowledgeable junior colleagues. They can write code, answer questions, and suggest ideas — but they sometimes make mistakes and need supervision. The key is knowing what to ask for and how to verify the results.

📋 **Overview of tools:**

| Tool | Best For | Free? |
|------|----------|-------|
| ChatGPT (chat.openai.com) | Generating test code, explaining errors, brainstorming test cases | Free tier available |
| GitHub Copilot | Autocomplete in your editor, writing code as you type | Free for students/OSS |
| Claude (claude.ai) | Long code analysis, detailed explanations | Free tier available |
| Cline (VS Code extension) | AI agent that can read/write files, run commands | Free with own API key |

**What AI can do for QA:**
- Generate test cases from requirements
- Write boilerplate code (page objects, fixtures, config)
- Explain error messages and stack traces
- Suggest edge cases you might have missed
- Convert manual test cases to automated code
- Generate test data (usernames, emails, addresses)

**What AI cannot do (reliably):**
- Know your specific application's behavior
- Guarantee the generated code actually works
- Replace understanding of what you're testing
- Make decisions about what's important to test

📝 **Task:** Open ChatGPT or Claude. Ask it: "I'm testing a login page with username and password fields. List 15 test cases I should cover, including edge cases." Review the list and compare it with test cases you would write manually.

⚠️ **Common mistakes:**

1. **Trusting AI output without testing it.** Always run the code. AI makes plausible-looking errors.
2. **Using AI without understanding the code.** If you can't read the code, you can't fix it when it breaks.
3. **Giving vague prompts.** "Write me tests" is too vague. Be specific about what, how, and for what application.

---

## Lesson 6.2 — Prompt Engineering for Test Generation

🎯 **What you will learn:** How to write effective prompts that produce useful, accurate test code from AI tools.

💡 **Simple explanation:** A "prompt" is what you type to the AI. Garbage in = garbage out. A vague prompt gives vague results. A detailed prompt with context, constraints, and examples gives you code you can actually use. This lesson teaches you how to craft prompts that work.

📋 **Prompt templates:**

**Template 1: Generate test cases**

```
I'm testing [feature] of [application].
Technology stack: Python, Pytest, Playwright.
Target URL: [URL]

Generate [number] test cases covering:
- Happy path (valid scenarios)
- Negative cases (invalid input)
- Edge cases (boundary values, empty fields, special characters)
- Security cases (SQL injection, XSS)

For each test case, provide:
1. Test name (descriptive, starts with test_)
2. Steps
3. Expected result
```

**Template 2: Generate Page Object**

```
Create a Page Object class for the [page name] page.
URL: [URL]
Framework: Python + Playwright (sync API)

The page has these elements:
- [element 1]: [description] (selector: [CSS/ID])
- [element 2]: [description] (selector: [CSS/ID])

The user can perform these actions:
- [action 1]
- [action 2]

Follow these rules:
- Use Playwright sync API
- Store locators in __init__
- One method per action
- Return self for chaining
- Do NOT put assertions in the page object
```

**Template 3: Convert manual test to automated**

```
Convert this manual test case to an automated Pytest + Playwright test:

Manual Test Case:
Title: [title]
Preconditions: [preconditions]
Steps:
1. [step 1]
2. [step 2]
3. [step 3]
Expected Result: [expected result]

Use:
- Playwright sync API with page fixture
- expect() for assertions
- Descriptive test function name
```

📋 **Example — Real prompt and result:**

**Prompt:**

```
Create a Page Object class for the SauceDemo login page.
URL: https://www.saucedemo.com
Framework: Python + Playwright (sync API)

Elements:
- Username field: input with id "user-name"
- Password field: input with id "password"
- Login button: button with text "Login"
- Error message: element with data-test="error"

Actions:
- Open the page
- Login with username and password
- Get error message text

Rules:
- Playwright sync API
- Locators in __init__
- Return self for chaining
- No assertions in page object
```

**AI-generated result (verify before using!):**

```python
# pages/login_page.py — AI-generated, verified by human
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

📝 **Task:** Use Template 1 to generate 10 test cases for the SauceDemo products page (sorting, adding to cart, product details). Review the AI output — mark which test cases are good, which need adjustment, and which are wrong.

⚠️ **Common mistakes:**

1. **Not specifying the technology stack.** AI might generate Selenium code when you need Playwright.
2. **Not giving selectors/IDs.** AI will guess selectors, and they'll probably be wrong.
3. **Accepting the first output.** Ask AI to refine: "This is good, but change X and add Y."

---

## Lesson 6.3 — AI-Generated Code Review

🎯 **What you will learn:** How to review AI-generated code, spot common errors, and fix them.

💡 **Simple explanation:** AI is a fast coder but a careless one. It generates code that *looks* right but may have subtle bugs: wrong selectors, missing waits, incorrect assertions, outdated API syntax. Your job is to review every line like a code reviewer. This lesson teaches you what to look for.

📋 **Checklist for reviewing AI-generated test code:**

```
□ Does it import the correct modules?
□ Does it use the right API (sync vs async)?
□ Are the selectors correct for the target page?
□ Does it use expect() for Playwright assertions (not bare assert)?
□ Are there proper waits (or does it rely on auto-wait correctly)?
□ Does the test clean up after itself?
□ Does it actually test what it claims to test?
□ Does it run without errors?
□ Does it pass for the right reasons (not a false positive)?
□ Is the test name descriptive?
```

📋 **Example — Spotting AI mistakes:**

AI generated this code:

```python
# AI-generated — contains errors!
from playwright.sync_api import Page

def test_add_to_cart(page: Page):
    page.goto("https://www.saucedemo.com")
    page.fill("#user-name", "standard_user")     # ❌ old API, use locator().fill()
    page.fill("#password", "secret_sauce")         # ❌ same issue
    page.click("input[type='submit']")             # ❌ old API + wrong selector
    page.wait_for_timeout(2000)                    # ❌ hard-coded wait
    page.click(".btn_inventory")                   # ⚠️ clicks first match, might be wrong item
    assert page.inner_text(".shopping_cart_badge") == "1"  # ❌ use expect() instead
```

**Corrected version:**

```python
# Human-reviewed and corrected
from playwright.sync_api import Page, expect

def test_add_to_cart(page: Page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")           # ✅ modern API
    page.locator("#password").fill("secret_sauce")              # ✅ modern API
    page.get_by_role("button", name="Login").click()            # ✅ reliable locator
    # No hard-coded wait needed — Playwright auto-waits         # ✅
    page.locator("[data-testid='add-to-cart-sauce-labs-backpack']").click()  # ✅ specific
    expect(page.locator(".shopping_cart_badge")).to_have_text("1")          # ✅ proper assertion
```

📝 **Task:** Ask AI to generate 3 tests for the SauceDemo cart page. Review the code using the checklist above. Fix all errors you find. Run the corrected tests.

⚠️ **Common mistakes:**

1. **Assuming AI code is correct because it looks professional.** Always run and verify.
2. **Not checking selectors against the actual page.** Open the page, use DevTools (F12), verify selectors exist.
3. **Skipping the "does it pass for the right reason" check.** A test that always passes is useless.

---

## Lesson 6.4 — AI for Test Data & Edge Cases

🎯 **What you will learn:** How to use AI to generate test data, discover edge cases, and create comprehensive test coverage.

💡 **Simple explanation:** AI is excellent at brainstorming. Ask it "What could go wrong?" and it'll list dozens of scenarios you might not have thought of. It can also generate realistic test data — names, emails, addresses — faster than you can type them.

📋 **Code example — AI-assisted test data generation:**

**Prompt to AI:**

```
Generate a Python list of 10 test data entries for testing a registration form.
Each entry should be a dict with: first_name, last_name, email, password, expected_valid (bool).
Include: valid data, empty fields, invalid emails, short passwords, special characters.
Format as a Python list I can use with @pytest.mark.parametrize.
```

**Result (reviewed and adjusted):**

```python
# test_data/registration_data.py — AI-generated, human-reviewed

REGISTRATION_TEST_DATA = [
    # Valid cases
    {"first_name": "John", "last_name": "Doe", "email": "john@example.com",
     "password": "Secure123", "expected_valid": True},
    {"first_name": "María", "last_name": "García", "email": "maria@test.com",
     "password": "Pass1234", "expected_valid": True},

    # Empty fields
    {"first_name": "", "last_name": "Doe", "email": "john@example.com",
     "password": "Secure123", "expected_valid": False},
    {"first_name": "John", "last_name": "", "email": "john@example.com",
     "password": "Secure123", "expected_valid": False},

    # Invalid email
    {"first_name": "John", "last_name": "Doe", "email": "not-an-email",
     "password": "Secure123", "expected_valid": False},
    {"first_name": "John", "last_name": "Doe", "email": "@example.com",
     "password": "Secure123", "expected_valid": False},

    # Short password
    {"first_name": "John", "last_name": "Doe", "email": "john@example.com",
     "password": "Ab1", "expected_valid": False},

    # Special characters
    {"first_name": "O'Brien", "last_name": "Smith-Jones", "email": "obrien@test.com",
     "password": "P@ss1234!", "expected_valid": True},

    # Very long input
    {"first_name": "A" * 256, "last_name": "Doe", "email": "long@test.com",
     "password": "Secure123", "expected_valid": False},

    # SQL injection attempt
    {"first_name": "'; DROP TABLE users; --", "last_name": "Test",
     "email": "hack@test.com", "password": "Secure123", "expected_valid": False},
]
```

📋 **Using with Pytest:**

```python
# test_registration.py
import pytest
from test_data.registration_data import REGISTRATION_TEST_DATA

@pytest.mark.parametrize("data", REGISTRATION_TEST_DATA,
    ids=[f"{d['email']}-valid={d['expected_valid']}" for d in REGISTRATION_TEST_DATA])
def test_registration(data):
    # This would call your registration function or API
    # For now, just demonstrate the structure
    print(f"Testing: {data['first_name']} {data['last_name']} ({data['email']})")
    print(f"  Expected valid: {data['expected_valid']}")
```

📋 **Prompt for edge case discovery:**

```
I'm testing a shopping cart feature. The cart allows:
- Adding items (max 10)
- Removing items
- Changing quantity (1-99)
- Applying discount codes
- Calculating total with tax

List 20 edge cases that could cause bugs. Focus on:
- Boundary values
- Invalid input
- Race conditions
- State transitions
- Calculations
```

📝 **Task:** Use AI to generate test data for the SauceDemo login page: at least 8 entries covering valid user, locked user, empty fields, special characters, very long strings, and SQL injection. Use the data in a parametrized Pytest test.

⚠️ **Common mistakes:**

1. **Using AI-generated test data without reviewing it.** Check that the data actually tests what you intend.
2. **Not including edge cases.** AI tends to generate "normal" data unless you specifically ask for edge cases.
3. **Hardcoding AI-generated data in test files.** Put it in separate data files for maintainability.

---

## Practice Block

**Exercise 1:** Use AI to generate a complete Page Object for a page you haven't automated yet (e.g., SauceDemo product detail page). Review, fix, and test it.

**Exercise 2:** Ask AI to convert 5 manual test cases (that you write) into automated Playwright + Pytest tests. Track how many work without modification, how many need minor fixes, and how many need major rewriting.

**Exercise 3:** Use AI to generate a comprehensive edge-case list for the SauceDemo checkout flow. Pick the 5 most interesting cases and automate them.

**Module 6 Checklist:**
- [ ] I know which AI tools are useful for QA
- [ ] I can write effective prompts for test generation
- [ ] I can review and fix AI-generated test code
- [ ] I can use AI to generate test data and discover edge cases

[↑ Back to Table of Contents](#table-of-contents)