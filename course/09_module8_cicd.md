# Module 8: CI/CD Basics

**Overview:** CI/CD (Continuous Integration / Continuous Delivery) means running your tests automatically every time code changes. Instead of manually running tests, a server does it for you — every push, every pull request. If tests fail, the team knows immediately. This module teaches you to set up GitHub Actions to run your Playwright and API tests automatically.

---

## Lesson 8.1 — What Is CI/CD

🎯 **What you will learn:** What CI/CD is, why it matters for QA, and how it fits into the development workflow.

💡 **Simple explanation:** Imagine having an assistant who runs ALL your tests every time anyone on the team pushes code. If something breaks, the assistant immediately tells you. That's CI/CD. **CI** (Continuous Integration) means code is tested automatically when merged. **CD** (Continuous Delivery) means code is deployed automatically after tests pass. For QA, CI is the most important part.

📋 **How CI/CD works:**

```
Developer pushes code → CI server detects change → Runs all tests → Reports results
        ↓                        ↓                       ↓              ↓
    git push            GitHub Actions starts      pytest runs    ✅ Pass or ❌ Fail
```

**Why CI/CD matters for QA:**
- Tests run on every code change — nothing is missed
- Bugs are found within minutes, not days
- No "it works on my machine" — tests run in a clean environment
- Pull requests can't be merged if tests fail
- Test results are visible to the whole team

**Popular CI/CD tools:**

| Tool | Description | Free Tier |
|------|-------------|-----------|
| GitHub Actions | Built into GitHub, easy to set up | 2,000 min/month |
| Jenkins | Self-hosted, highly customizable | Free (open source) |
| GitLab CI | Built into GitLab | 400 min/month |
| CircleCI | Cloud-based, fast | 6,000 min/month |

We'll use **GitHub Actions** because it's built into GitHub and requires zero extra setup.

📝 **Task:** Look at any open-source project on GitHub. Click on the "Actions" tab. Explore what CI workflows they have and what they run.

⚠️ **Common mistakes:**

1. **Thinking CI replaces local testing.** Run tests locally first. CI is the safety net, not the first check.
2. **Ignoring CI failures.** A failing CI pipeline must be fixed immediately — it blocks the whole team.
3. **Making CI too slow.** If tests take 30 minutes, developers stop waiting. Keep CI under 10 minutes.

---

## Lesson 8.2 — GitHub Actions for Tests

🎯 **What you will learn:** How to create a GitHub Actions workflow that installs dependencies and runs your Pytest tests automatically.

💡 **Simple explanation:** GitHub Actions uses YAML files in `.github/workflows/` to define what to do. You describe: "When code is pushed, use Python 3.11, install my dependencies, and run pytest." GitHub does the rest — it spins up a virtual machine, runs everything, and shows you the results.

📋 **Code example:**

Create `.github/workflows/tests.yml`:

```yaml
# .github/workflows/tests.yml
name: Run Tests

# When to run: on every push and pull request to main
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    # Run on Ubuntu (Linux virtual machine)
    runs-on: ubuntu-latest

    steps:
      # Step 1: Get the code
      - name: Checkout code
        uses: actions/checkout@v4

      # Step 2: Set up Python
      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      # Step 3: Install dependencies
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      # Step 4: Install Playwright browsers
      - name: Install Playwright browsers
        run: playwright install --with-deps chromium

      # Step 5: Run tests
      - name: Run tests
        run: pytest tests/ -v --tb=short

      # Step 6: Upload test results (optional but useful)
      - name: Upload test artifacts
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: |
            screenshots/
            videos/
            traces/
```

📋 **The `requirements.txt` this workflow needs:**

```
pytest==7.4.0
playwright==1.40.0
pytest-playwright==0.4.3
requests==2.31.0
```

📋 **Understanding the YAML:**

```yaml
name: Run Tests              # Display name in GitHub
on: push / pull_request      # Trigger events
jobs:                         # What to do
  test:                       # Job name
    runs-on: ubuntu-latest    # Virtual machine type
    steps:                    # Sequential steps
      - name: Step name       # Human-readable label
        run: command          # Shell command to execute
```

✅ **Expected result:** After pushing this file to GitHub, go to the "Actions" tab — you'll see the workflow running.

📝 **Task:** Add the workflow file to your project, push to GitHub, and watch it run. Check the "Actions" tab for results.

⚠️ **Common mistakes:**

1. **Wrong indentation in YAML.** YAML is whitespace-sensitive. Use 2 spaces, no tabs.
2. **Forgetting `playwright install --with-deps`.** CI servers don't have browsers pre-installed.
3. **Not using `if: always()` for artifact upload.** Without it, artifacts won't upload when tests fail — exactly when you need them most.

---

## Lesson 8.3 — Reading CI Results & Artifacts

🎯 **What you will learn:** How to read CI output, debug failures, and use test artifacts (screenshots, videos, traces) from CI runs.

💡 **Simple explanation:** When CI fails, you need to figure out why. GitHub Actions shows you the full log — every command's output, every test result. If you set up artifacts (screenshots, videos), you can download them and see exactly what the browser showed during the test. This lesson teaches you to be a CI detective.

📋 **Reading CI results:**

```
Actions tab → Click on workflow run → Click on job → Expand steps

Step: Run tests
  tests/test_login.py::test_successful_login PASSED        ← ✅ Good
  tests/test_login.py::test_failed_login PASSED             ← ✅ Good
  tests/test_cart.py::test_add_to_cart FAILED               ← ❌ Investigate!

  FAILED tests/test_cart.py::test_add_to_cart
  > expect(page.locator(".shopping_cart_badge")).to_have_text("1")
  > AssertionError: Locator expected to have text "1"
  > Received: (element not found)
```

📋 **Enhanced workflow with screenshots on failure:**

```yaml
# .github/workflows/tests.yml — Enhanced version
name: Run Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          playwright install --with-deps chromium

      - name: Run tests
        run: |
          mkdir -p screenshots videos traces
          pytest tests/ -v --tb=short \
            --screenshot=on \
            --video=retain-on-failure \
            --tracing=retain-on-failure

      - name: Upload screenshots
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: failure-screenshots
          path: test-results/

      - name: Upload traces
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: failure-traces
          path: test-results/
```

📋 **Debugging CI failures — checklist:**

```
□ Read the error message carefully — what test failed?
□ Check the assertion — what was expected vs. what was received?
□ Download artifacts — do screenshots show the right page?
□ Check if the test passes locally — is it a CI-only issue?
□ Check timing — CI is slower, timeouts may need increasing
□ Check dependencies — is requirements.txt up to date?
□ Check the application — is the test site actually up?
```

📋 **Adding a status badge to README:**

```markdown
# My Test Project

![Tests](https://github.com/YOUR-USERNAME/YOUR-REPO/actions/workflows/tests.yml/badge.svg)

This project uses automated tests with Playwright and Pytest.
```

This adds a green ✅ or red ❌ badge to your README showing whether tests are passing.

✅ **Expected result:** You can read CI logs, download artifacts, and debug failures.

📝 **Task:** Intentionally break one of your tests (e.g., change an expected value). Push to GitHub. Watch the CI fail. Read the error in the Actions log. Download the screenshot artifact. Fix the test and push again — watch it turn green.

⚠️ **Common mistakes:**

1. **Not reading the full error message.** The answer is usually in the logs — read carefully.
2. **Assuming CI failure means your code is wrong.** It could be a flaky test, timeout, or the test site being down.
3. **Not downloading artifacts.** Screenshots and traces tell you more than log messages.

---

**Module 8 Checklist:**
- [ ] I understand what CI/CD is and why it matters
- [ ] I can create a GitHub Actions workflow
- [ ] I can read CI results and debug failures
- [ ] I can download and use test artifacts from CI

[↑ Back to Table of Contents](#table-of-contents)