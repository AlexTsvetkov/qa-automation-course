# Module 7: Git Basics

**Overview:** Git is a version control system — it tracks every change you make to your code. If you break something, you can go back. If you work in a team, everyone can work on different features without overwriting each other's work. Every professional developer and QA engineer uses Git daily. This module covers the essentials.

---

## Lesson 7.1 — What Is Git, Init, Commit

🎯 **What you will learn:** What Git is, how to create a repository, and how to save your work with commits.

💡 **Simple explanation:** Imagine writing an essay but saving a new copy every time you make changes: `essay_v1.doc`, `essay_v2.doc`, `essay_final.doc`, `essay_final_FINAL.doc`. Git does this automatically and intelligently. Every "save point" is called a **commit**. You can see all past versions, compare changes, and go back to any point in history.

📋 **Code example:**

```bash
# Create a new project folder
mkdir my-test-project
cd my-test-project

# Initialize Git (creates a hidden .git folder)
git init
# Output: Initialized empty Git repository in .../my-test-project/.git/

# Check status — what files have changed?
git status
# Output: On branch main, No commits yet, nothing to commit

# Create a file
echo "# My Test Project" > README.md
echo "pytest==7.4.0" > requirements.txt

# Check status again
git status
# Output: Untracked files: README.md, requirements.txt

# Stage files (tell Git which files to include in the next commit)
git add README.md requirements.txt
# Or add everything: git add .

# Commit — save a snapshot with a message
git commit -m "Initial commit: add README and requirements"
# Output: [main (root-commit) abc1234] Initial commit: add README and requirements

# View commit history
git log --oneline
# Output: abc1234 Initial commit: add README and requirements
```

The Git workflow:

```
Working Directory → git add → Staging Area → git commit → Repository
  (your files)                (ready to save)              (saved history)
```

📋 **Making more commits:**

```bash
# Create a test file
cat > test_example.py << 'EOF'
def test_addition():
    assert 1 + 1 == 2

def test_subtraction():
    assert 5 - 3 == 2
EOF

# Stage and commit
git add test_example.py
git commit -m "Add basic math tests"

# Modify the file
cat >> test_example.py << 'EOF'

def test_multiplication():
    assert 3 * 4 == 12
EOF

# See what changed
git diff
# Shows the added lines in green with +

# Stage and commit
git add test_example.py
git commit -m "Add multiplication test"

# View history
git log --oneline
# abc3456 Add multiplication test
# abc2345 Add basic math tests
# abc1234 Initial commit: add README and requirements
```

✅ **Expected result:** You have a Git repository with 3 commits.

📝 **Task:** Create a new Git repository. Add a `conftest.py` file with a base URL fixture and a `test_smoke.py` file with one simple test. Make 3 separate commits: one for the initial setup, one for conftest, and one for the test file.

⚠️ **Common mistakes:**

1. **Forgetting `git add` before `git commit`.** Commit only saves what's staged.
2. **Writing vague commit messages like "update" or "fix."** Write what you changed: "Add login page tests."
3. **Committing everything at once.** Make small, focused commits — one logical change per commit.

---

## Lesson 7.2 — Branches, Merge

🎯 **What you will learn:** How to create branches to work on features without affecting the main code, and how to merge them back.

💡 **Simple explanation:** A branch is like a parallel universe for your code. You create a branch, make changes there, and the `main` branch stays untouched. When your feature is ready and tested, you merge it back into main. This way, you can work on multiple things at once without breaking anything.

📋 **Code example:**

```bash
# See current branch
git branch
# Output: * main

# Create and switch to a new branch
git checkout -b feature/login-tests
# Output: Switched to a new branch 'feature/login-tests'

# Now create some files on this branch
cat > test_login.py << 'EOF'
from playwright.sync_api import Page, expect

def test_successful_login(page: Page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("standard_user")
    page.locator("#password").fill("secret_sauce")
    page.get_by_role("button", name="Login").click()
    expect(page).to_have_url("https://www.saucedemo.com/inventory.html")

def test_failed_login(page: Page):
    page.goto("https://www.saucedemo.com")
    page.locator("#user-name").fill("wrong_user")
    page.locator("#password").fill("wrong_pass")
    page.get_by_role("button", name="Login").click()
    error = page.locator("[data-test='error']")
    expect(error).to_be_visible()
EOF

git add test_login.py
git commit -m "Add login tests"

# See all branches
git branch
#   main
# * feature/login-tests

# Switch back to main
git checkout main

# Notice: test_login.py doesn't exist on main!
ls test_login.py
# ls: test_login.py: No such file or directory

# Merge the feature branch into main
git merge feature/login-tests
# Output: Fast-forward... test_login.py | 15 +++++++++++++++

# Now test_login.py exists on main too!
ls test_login.py
# test_login.py

# Delete the feature branch (it's merged, no longer needed)
git branch -d feature/login-tests
```

📋 **Handling merge conflicts:**

```bash
# If two branches change the same line, Git can't auto-merge
# You'll see something like:
# CONFLICT (content): Merge conflict in test_login.py
# Fix the conflict manually, then:
git add test_login.py
git commit -m "Resolve merge conflict in login tests"
```

✅ **Expected result:** Feature developed on branch, merged to main cleanly.

📝 **Task:** Create a branch called `feature/cart-tests`. Add a test file with 2 cart tests. Commit. Switch to main and merge. Verify the tests are now on main.

⚠️ **Common mistakes:**

1. **Working directly on main.** Always create a branch for new work.
2. **Not committing before switching branches.** Uncommitted changes follow you to the other branch (or get lost).
3. **Panicking at merge conflicts.** They're normal. Open the file, pick the right version, save, commit.

---

## Lesson 7.3 — GitHub: Push, Pull, Clone

🎯 **What you will learn:** How to use GitHub to store your code online, collaborate with others, and create pull requests.

💡 **Simple explanation:** Git is local (on your computer). GitHub is Git in the cloud. You **push** your code to GitHub so others can see it and so you have a backup. You **pull** other people's changes. You **clone** to get a copy of an existing project. A **pull request** (PR) is how you propose changes — others review your code before it gets merged.

📋 **Code example:**

```bash
# 1. Create a repository on GitHub (via github.com → New Repository)
#    Name it: qa-automation-tests

# 2. Connect your local repo to GitHub
git remote add origin https://github.com/YOUR-USERNAME/qa-automation-tests.git

# 3. Push your code to GitHub
git push -u origin main
# Your code is now on GitHub!

# 4. Clone someone else's project
git clone https://github.com/someone/their-project.git
cd their-project

# 5. Create a branch, make changes, push
git checkout -b feature/my-new-tests
# ... make changes and commit ...
git push origin feature/my-new-tests
# Now go to GitHub and create a Pull Request!

# 6. Pull latest changes from GitHub
git pull origin main
```

📋 **Typical team workflow:**

```
1. git pull origin main              ← Get latest code
2. git checkout -b feature/my-task   ← Create feature branch
3. ... write code, run tests ...     ← Do your work
4. git add . && git commit -m "..."  ← Save your work
5. git push origin feature/my-task   ← Upload to GitHub
6. Open Pull Request on GitHub       ← Ask for review
7. Team reviews & approves           ← Code review
8. Merge PR on GitHub                ← Merge to main
9. git checkout main && git pull     ← Update your local main
```

📋 **.gitignore — Files Git should ignore:**

```bash
# .gitignore
__pycache__/
*.pyc
.pytest_cache/
videos/
screenshots/
traces/
.env
node_modules/
```

✅ **Expected result:** Code is on GitHub, accessible to the team.

📝 **Task:** Create a GitHub repository. Push your test project from Module 4. Create a branch, add a new test, push, and create a Pull Request on GitHub.

⚠️ **Common mistakes:**

1. **Committing passwords or API keys.** Use `.gitignore` and `.env` files. Never push secrets.
2. **Force-pushing to main.** `git push --force` on main can destroy team members' work.
3. **Not pulling before pushing.** Always `git pull` first to avoid conflicts.

---

**Module 7 Checklist:**
- [ ] I can create a Git repository and make commits
- [ ] I can create branches and merge them
- [ ] I can push to GitHub and create Pull Requests
- [ ] I have a proper .gitignore file

[↑ Back to Table of Contents](#table-of-contents)