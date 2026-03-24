# Module 3: Playwright — Browser Automation

**Overview:** Playwright is a modern browser automation library that lets you control Chrome, Firefox, and Safari from Python code. It clicks buttons, fills forms, reads text, and takes screenshots — everything a manual tester does, but automated. This module is the heart of the course.

---

## Lesson 3.1 — What Playwright Is

🎯 **What you will learn:** What Playwright is, how to install it, and how to open a browser and visit a webpage using Python code.

💡 **Simple explanation:** Playwright is a robot that controls a web browser. You write Python commands like "go to this URL" and "click this button," and Playwright opens a real browser and does exactly that. It works with Chrome, Firefox, and Safari. It's made by Microsoft and is one of the most popular tools for browser automation today.

📋 **Code example:**

Install Playwright:

```bash
pip install playwright
playwright install
```

Create `first_browser.py`:

```python
# first_browser.py — Opening a browser with Playwright
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        await page.goto("https://www.saucedemo.com")

        title = await page.title()
        print(f"Page title: {title}")

        url = page.url
        print(f"Current URL: {url}")

        await page.screenshot(path="screenshot.png")
        print("Screenshot saved!")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

A Chrome browser opens, navigates to SauceDemo, then closes. Terminal shows:

```
Page title: Swag Labs
Current URL: https://www.saucedemo.com/
Screenshot saved!
```

📝 **Task:** Modify the script to visit `https://the-internet.herokuapp.com`, print the page title, and save a screenshot called `heroku_screenshot.png`.

✅ **Expected result:** Browser opens, you see the Heroku app page, and the screenshot is saved.

⚠️ **Common mistakes:**

1. **Forgetting `playwright install` after `pip install playwright`.** The pip package is the Python library. `playwright install` downloads the actual browsers.
2. **Missing `asyncio.run(main())`.** Async code needs this line to actually execute.
3. **Using `headless=True` and wondering why no browser appears.** Set `headless=False` during development to see the browser.

---

## Lesson 3.2 — Locators

🎯 **What you will learn:** How to find elements on a web page using different locator strategies — the foundation of all browser automation.

💡 **Simple explanation:** Before you can click a button or type in a field, you need to tell Playwright which element you mean. A "locator" is like giving directions: "the button with text 'Login'" or "the input field with id 'username'." Playwright offers several ways to find elements, and some are more reliable than others.

📋 **Code example:**

```python
# locators_demo.py
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        await page.goto("https://www.saucedemo.com")

        # Locator by ID (CSS selector)
        username_field = page.locator("#user-name")

        # Locator by CSS attribute selector
        password_field = page.locator("input[placeholder='Password']")

        # Locator by role + name (recommended by Playwright)
        login_button = page.get_by_role("button", name="Login")

        # Fill the fields
        await username_field.fill("standard_user")
        await password_field.fill("secret_sauce")
        print("Fields filled!")

        # Click the button
        await login_button.click()
        print("Login button clicked!")

        # Verify we are on the products page
        header = page.locator(".title")
        header_text = await header.text_content()
        print(f"Page header: {header_text}")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

```
Fields filled!
Login button clicked!
Page header: Products
```

📝 **Task:** After logging in, use `page.get_by_text()` to find the first product name and print it. Use `page.locator(".inventory_item")` to count how many products are displayed.

✅ **Expected result:** The first product name is printed, and the count is 6.

⚠️ **Common mistakes:**

1. **Using fragile locators like `div > div:nth-child(3) > a`.** These break when layout changes. Prefer `get_by_role`, `get_by_text`, or IDs.
2. **Not waiting for elements.** Playwright auto-waits, but be aware of loading spinners.
3. **Mixing up `locator()` and `query_selector()`.** Always use `locator()` — it's the modern, recommended API.

---

## Lesson 3.3 — Actions

🎯 **What you will learn:** How to interact with web page elements — clicking, typing, selecting dropdowns, hovering, and using keyboard shortcuts.

💡 **Simple explanation:** "Actions" are the things you do on a web page: clicking buttons, typing in fields, selecting options from dropdowns. In Playwright, every action is a simple command. `fill()` types text, `click()` clicks, `select_option()` picks from a dropdown. These are the same actions you do manually — just written as code.

📋 **Code example:**

```python
# actions_demo.py
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        await page.goto("https://www.saucedemo.com")

        # Type into fields
        await page.locator("#user-name").fill("standard_user")
        await page.locator("#password").fill("secret_sauce")

        # Click the login button
        await page.get_by_role("button", name="Login").click()
        print("Logged in!")

        # Click "Add to cart" for the first product
        await page.locator("[data-testid='add-to-cart-sauce-labs-backpack']").click()
        print("Added backpack to cart!")

        # Check cart badge shows "1"
        cart_badge = page.locator(".shopping_cart_badge")
        badge_text = await cart_badge.text_content()
        print(f"Cart badge: {badge_text}")

        # Click the cart icon
        await page.locator(".shopping_cart_link").click()
        print("Navigated to cart!")

        # Verify item is in cart
        item_name = await page.locator(".inventory_item_name").text_content()
        print(f"Item in cart: {item_name}")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

```
Logged in!
Added backpack to cart!
Cart badge: 1
Navigated to cart!
Item in cart: Sauce Labs Backpack
```

📝 **Task:** Extend the script: add both "Sauce Labs Backpack" and "Sauce Labs Bike Light" to cart. Verify badge shows "2". Navigate to cart and print both item names.

✅ **Expected result:** Cart badge shows "2" and both item names are printed.

⚠️ **Common mistakes:**

1. **Using `type()` instead of `fill()`.** `fill()` clears first then types. Usually `fill()` is what you want.
2. **Clicking an element that is not visible.** Playwright will throw an error if the element is off-screen.
3. **Not waiting after navigation.** After clicking a link that loads a new page, old locators won't work on the new page.

---

## Lesson 3.4 — Assertions

🎯 **What you will learn:** How to verify that a web page looks and behaves correctly using Playwright's built-in assertion methods.

💡 **Simple explanation:** Assertions are how your tests say "I expect this to be true." Playwright has a special `expect()` function that waits for conditions to become true (with a timeout). Instead of checking instantly and failing, it retries for a few seconds. This makes tests more reliable because web pages sometimes need a moment to update.

📋 **Code example:**

```python
# assertions_demo.py
import asyncio
from playwright.async_api import async_playwright, expect

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        await page.goto("https://www.saucedemo.com")

        # Assert the page title
        await expect(page).to_have_title("Swag Labs")
        print("Title assertion passed!")

        # Assert an element is visible
        login_button = page.get_by_role("button", name="Login")
        await expect(login_button).to_be_visible()
        print("Login button is visible!")

        # Login
        await page.locator("#user-name").fill("standard_user")
        await page.locator("#password").fill("secret_sauce")
        await login_button.click()

        # Assert URL changed
        await expect(page).to_have_url("https://www.saucedemo.com/inventory.html")
        print("URL assertion passed!")

        # Assert header text
        header = page.locator(".title")
        await expect(header).to_have_text("Products")
        print("Header text assertion passed!")

        # Assert number of products
        products = page.locator(".inventory_item")
        await expect(products).to_have_count(6)
        print("Product count assertion passed!")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

```
Title assertion passed!
Login button is visible!
URL assertion passed!
Header text assertion passed!
Product count assertion passed!
```

📝 **Task:** Write assertions for the login error flow: go to SauceDemo, type wrong credentials, click Login, assert the error message is visible and contains "Username and password do not match".

✅ **Expected result:** The error message assertion passes.

⚠️ **Common mistakes:**

1. **Using Python `assert` instead of Playwright `expect()`.** Python assert doesn't wait. Playwright `expect()` retries until condition is met.
2. **Forgetting `await` before `expect()`.** Every `expect()` call needs `await` in async code.
3. **Using `to_have_text()` for partial text.** Use `to_contain_text()` for substring matching.

---

## Lesson 3.5 — Screenshots, Video, Tracing

🎯 **What you will learn:** How to capture visual evidence of your tests — screenshots, videos, and trace files.

💡 **Simple explanation:** When a test fails, you need evidence. A screenshot captures the page at one moment. A video records the entire test. A trace file records everything (network requests, DOM snapshots) and lets you replay step-by-step. These are your best friends when debugging.

📋 **Code example:**

```python
# evidence_demo.py
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        context = await browser.new_context(record_video_dir="videos/")

        # Start tracing
        await context.tracing.start(screenshots=True, snapshots=True)

        page = await context.new_page()
        await page.goto("https://www.saucedemo.com")

        # Take a screenshot
        await page.screenshot(path="screenshots/login_page.png")
        print("Screenshot saved: login_page.png")

        # Perform login
        await page.locator("#user-name").fill("standard_user")
        await page.locator("#password").fill("secret_sauce")
        await page.get_by_role("button", name="Login").click()

        # Full-page screenshot after login
        await page.screenshot(path="screenshots/after_login.png", full_page=True)
        print("Screenshot saved: after_login.png")

        # Stop tracing and save
        await context.tracing.stop(path="traces/login_trace.zip")
        print("Trace saved: login_trace.zip")

        await context.close()
        await browser.close()

asyncio.run(main())
```

View the trace:

```bash
playwright show-trace traces/login_trace.zip
```

✅ **Expected result:** Screenshots saved, video recorded, trace file created.

📝 **Task:** Take a screenshot of a failed login (wrong password) showing the error message. Save as `screenshots/error_message.png`.

✅ **Expected result:** Screenshot clearly shows the error message.

⚠️ **Common mistakes:**

1. **Not closing the context when recording video.** Video is finalized only when context closes.
2. **Forgetting `full_page=True` for long pages.** Without it, only the visible viewport is captured.
3. **Not creating output directories.** Ensure `screenshots/` and `videos/` folders exist.

---

## Lesson 3.6 — Waits & Timeouts

🎯 **What you will learn:** How Playwright handles timing and how to control wait behavior.

💡 **Simple explanation:** Web pages are not instant. After clicking a button, the page might take a moment to load. Playwright has "auto-wait" — it automatically waits for elements to be ready. But sometimes you need to wait for something specific, like a loading spinner to disappear. That's when you use explicit waits.

📋 **Code example:**

```python
# waits_demo.py
import asyncio
from playwright.async_api import async_playwright, expect

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        page = await browser.new_page()
        page.set_default_timeout(30000)

        await page.goto("https://www.saucedemo.com")

        # Auto-wait: Playwright waits automatically
        await page.locator("#user-name").fill("standard_user")
        await page.locator("#password").fill("secret_sauce")
        await page.get_by_role("button", name="Login").click()

        # Wait for a specific element
        await page.wait_for_selector(".inventory_list")
        print("Inventory list is visible!")

        # Wait for URL to change
        await page.wait_for_url("**/inventory.html")
        print("URL changed to inventory page!")

        # Wait for network idle
        await page.wait_for_load_state("networkidle")
        print("Page fully loaded!")

        # Custom timeout for assertion
        header = page.locator(".title")
        await expect(header).to_have_text("Products", timeout=5000)
        print("Header verified!")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

```
Inventory list is visible!
URL changed to inventory page!
Page fully loaded!
Header verified!
```

📝 **Task:** Login to SauceDemo, use `page.wait_for_selector()` to wait for the first product image to load, then verify product count is 6.

✅ **Expected result:** Wait completes and count assertion passes.

⚠️ **Common mistakes:**

1. **Adding `time.sleep()` everywhere.** Never use `time.sleep()` in Playwright. Use built-in waits.
2. **Setting timeouts too short.** In CI, pages load slower. Use 10–30 seconds minimum.
3. **Not understanding auto-wait.** Playwright already waits before `click()`, `fill()`, etc.

---

## Lesson 3.7 — Multi-page & Iframes

🎯 **What you will learn:** How to handle pop-up windows, new tabs, and iframes (pages embedded inside other pages).

💡 **Simple explanation:** Sometimes clicking a link opens a new tab. Sometimes a page embeds another page inside a box (called an "iframe" — think of it as a picture frame containing a live web page). Both require special handling because Playwright focuses on one page at a time.

📋 **Code example:**

```python
# multipage_demo.py
import asyncio
from playwright.async_api import async_playwright

async def main():
    async with async_playwright() as p:
        browser = await p.chromium.launch(headless=False)
        context = await browser.new_context()
        page = await context.new_page()

        # Working with an iframe
        await page.goto("https://the-internet.herokuapp.com/iframe")
        frame = page.frame_locator("#mce_0_ifr")
        editor = frame.locator("#tinymce")
        await editor.clear()
        await editor.fill("Hello from Playwright!")
        text = await editor.text_content()
        print(f"Text in iframe: {text}")

        # Handling new tabs
        await page.goto("https://the-internet.herokuapp.com/windows")
        async with context.expect_page() as new_page_info:
            await page.get_by_text("Click Here").click()
        new_page = await new_page_info.value
        await new_page.wait_for_load_state()
        new_page_text = await new_page.locator("h3").text_content()
        print(f"New window text: {new_page_text}")

        await browser.close()

asyncio.run(main())
```

✅ **Expected result:**

```
Text in iframe: Hello from Playwright!
New window text: New Window
```

📝 **Task:** Go to `https://the-internet.herokuapp.com/nested_frames` and read the text from the middle frame. Print it.

✅ **Expected result:** The text "MIDDLE" is printed.

⚠️ **Common mistakes:**

1. **Trying to use `page.locator()` inside an iframe.** You must use `page.frame_locator()` first.
2. **Not waiting for new page to load.** Always call `await new_page.wait_for_load_state()`.
3. **Forgetting that frames are separate scopes.** Locators from the main page don't work in iframes.

---

## Lesson 3.8 — Playwright + Pytest Integration

🎯 **What you will learn:** How to combine Playwright with Pytest for organized, automatically discovered browser tests.

💡 **Simple explanation:** So far we used Playwright standalone. Now we combine it with Pytest. The `pytest-playwright` plugin gives you a ready-to-use `page` fixture — no need to manually launch and close the browser. Tests use the **sync** API (no `async/await` needed).

📋 **Code example:**

Install the plugin:

```bash
pip install pytest-playwright
```

Create `test_saucedemo.py`:

```python
# test_saucedemo.py — Playwright + Pytest integration
from playwright.sync_api import Page, expect

def test_page_title(page: Page):
    page.goto("https://www.saucedemo.com")
    expect(page).to_have_title("Swag Labs")

def test_successful_login(page: Page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")
    page.locator("#password").fill("secret_sauce")
    page.get_by_role("button", name="Login").click()
    expect(page).to_have_url("https://www.saucedemo.com/inventory.html")
    expect(page.locator(".title")).to_have_text("Products")

def test_failed_login_shows_error(page: Page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("wrong_user")
    page.locator("#password").fill("wrong_pass")
    page.get_by_role("button", name="Login").click()
    error = page.locator("[data-test='error']")
    expect(error).to_be_visible()
    expect(error).to_contain_text("Username and password do not match")
```

Run:

```bash
pytest test_saucedemo.py -v --headed
```

> **Note:** With `pytest-playwright`, use the **sync** API (`from playwright.sync_api`). No `async/await`. Add `--headed` to see the browser.

✅ **Expected result:**

```
test_saucedemo.py::test_page_title PASSED
test_saucedemo.py::test_successful_login PASSED
test_saucedemo.py::test_failed_login_shows_error PASSED
```

📝 **Task:** Add a test that logs in, adds a product to cart, goes to cart, and verifies the product name is displayed.

✅ **Expected result:** The test passes confirming the product appears in the cart.

⚠️ **Common mistakes:**

1. **Mixing async and sync API.** With `pytest-playwright`, use **sync** API only.
2. **Manually launching browsers.** The `page` fixture handles this. Just use `page` as a parameter.
3. **Forgetting `--headed`.** Tests run headless by default. Use `--headed` during development.

---

## Mini-Project 3

**Task:** Automate a complete shopping flow on SauceDemo: login → add item → checkout.

```python
# test_shopping_flow.py
from playwright.sync_api import Page, expect

def test_complete_shopping_flow(page: Page):
    # Step 1: Login
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")
    page.locator("#password").fill("secret_sauce")
    page.get_by_role("button", name="Login").click()
    expect(page.locator(".title")).to_have_text("Products")

    # Step 2: Add item to cart
    page.locator("[data-testid='add-to-cart-sauce-labs-backpack']").click()
    expect(page.locator(".shopping_cart_badge")).to_have_text("1")

    # Step 3: Go to cart
    page.locator(".shopping_cart_link").click()
    expect(page.locator(".inventory_item_name")).to_have_text("Sauce Labs Backpack")

    # Step 4: Checkout - Your Information
    page.get_by_role("button", name="Checkout").click()
    page.locator("#first-name").fill("John")
    page.locator("#last-name").fill("Doe")
    page.locator("#postal-code").fill("12345")
    page.get_by_role("button", name="Continue").click()

    # Step 5: Checkout - Overview
    expect(page.locator(".summary_total_label")).to_contain_text("Total:")
    page.get_by_role("button", name="Finish").click()

    # Step 6: Confirmation
    expect(page.locator(".complete-header")).to_have_text("Thank you for your order!")
```

✅ **Expected result:** `pytest test_shopping_flow.py -v --headed` — 1 test passed.

**Module 3 Checklist:**
- [ ] I can install and launch Playwright
- [ ] I can find elements using locators
- [ ] I can perform actions (click, fill, select)
- [ ] I can write assertions with expect()
- [ ] I can capture screenshots, videos, and traces
- [ ] I understand auto-wait and explicit waits
- [ ] I can handle iframes and new tabs
- [ ] I can integrate Playwright with Pytest

[↑ Back to Table of Contents](#table-of-contents)