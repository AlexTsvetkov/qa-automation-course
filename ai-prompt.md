# ============================================================
# AI AGENT TASK: GENERATE A COMPLETE QA AUTOMATION COURSE
# ============================================================

## ROLE & CONTEXT
You are an expert technical educator and senior QA automation engineer with 10+ years of experience. Your task is to generate a complete, publication-ready course document on test automation for manual QA engineers transitioning to automation. The audience has ZERO programming experience. Write as a patient, encouraging mentor — never condescending, always practical.

## DELIVERABLE
Format:       PDF document, professionally formatted
Length:       35–45 pages (strict limit)
Language:     English, simple and clear — no jargon without explanation
Tone:         Beginner-friendly, practical, zero unnecessary theory
Code style:   Python 3.10+, Playwright (async preferred), Pytest

## DOCUMENT STRUCTURE

# Generate EXACTLY this structure in order:

COVER PAGE
- Title: "From Manual to Automation: Python + Playwright with AI"
- Subtitle: "A Complete Practical Guide for QA Engineers"
- Version 1.0 | Year 2024

TABLE OF CONTENTS
- Auto-linked, numbered, shows module + lesson titles
- Include page numbers
- Mark practice tasks with [TASK] indicator

INTRODUCTION (1 page)
- Who this course is for
- What you will be able to do after completing it
- How to use this course (recommended pace: 2–3 hrs/day, 4–6 weeks)
- Required tools list with install links
- "Before you start" checklist

## MODULE BREAKDOWN

# Each module must contain: overview, lessons, mini-project, checklist

MODULE 1: Python Foundations (6 lessons)
Lesson 1.1 — Python setup & first script (print, run, terminal basics)
Lesson 1.2 — Variables, data types, type() function
Lesson 1.3 — Lists, dicts, loops (for/while)
Lesson 1.4 — Functions, parameters, return values
Lesson 1.5 — if/else, assertions, exceptions (try/except)
Lesson 1.6 — Files & folders, reading/writing .txt and .json
Mini-project: Write a script that reads test data from a JSON file and prints a test report

MODULE 2: Pytest — Test Framework (5 lessons)
Lesson 2.1 — What is Pytest, why it matters, test discovery
Lesson 2.2 — Writing test functions, assertions, assert messages
Lesson 2.3 — Fixtures: setup/teardown, conftest.py
Lesson 2.4 — Parametrize: run one test with many data sets
Lesson 2.5 — Markers, groups, running subsets of tests
Mini-project: Write a parametrized test suite for a login form (valid/invalid cases)

MODULE 3: Playwright — Browser Automation (8 lessons)
Lesson 3.1 — What Playwright is, install, first browser launch
Lesson 3.2 — Locators: CSS, XPath, get_by_role, get_by_text
Lesson 3.3 — Actions: click, fill, select, hover, keyboard
Lesson 3.4 — Assertions: expect(), to_be_visible(), to_have_text()
Lesson 3.5 — Screenshots, video recording, tracing
Lesson 3.6 — Waits & timeouts: auto-wait vs manual wait
Lesson 3.7 — Multi-page & iframes handling
Lesson 3.8 — Playwright + Pytest integration (fixtures, conftest)
Mini-project: Automate login → add item to cart → checkout flow on a demo site

MODULE 4: End-to-End (E2E) Testing (5 lessons)
Lesson 4.1 — What E2E tests are vs unit/integration tests (visual comparison table)
Lesson 4.2 — Designing user flows: mapping manual test cases to automation
Lesson 4.3 — Page Object Model (POM): why and how, step-by-step
Lesson 4.4 — Test data management: hardcoded vs fixtures vs external files
Lesson 4.5 — Debugging failing E2E tests: trace viewer, screenshots, logs
Mini-project: Build a POM-based test suite for a 3-page checkout flow

MODULE 5: API Testing with requests (4 lessons)
Lesson 5.1 — What is an API, REST basics, status codes (for QAs)
Lesson 5.2 — GET, POST, PUT, DELETE with Python requests library
Lesson 5.3 — Asserting API responses: status, body, headers
Lesson 5.4 — Combining API + UI tests: seed data via API, verify via browser
Mini-project: Write API tests for a public REST API (reqres.in or JSONPlaceholder)

MODULE 6: AI-Powered Testing (4 lessons — PRACTICAL ONLY)
# No theory. Every lesson = real prompt + real AI output + explanation
Lesson 6.1 — Generate test cases from a feature description
Input: Feature spec text
Prompt template: included verbatim
Output: structured test case list
Explanation: what to review, what to fix

Lesson 6.2 — Generate Playwright test code from test cases
Input: test case list from 6.1
Prompt template: included verbatim
Output: working .py test file
Explanation: how to validate & run it

Lesson 6.3 — Refactor & improve existing tests with AI
Input: a "bad" test example (hardcoded, fragile)
Prompt template: included verbatim
Output: refactored test with POM + fixtures
Explanation: what changed and why

Lesson 6.4 — Debug a failing test using AI
Input: error message + test code
Prompt template: included verbatim
Output: root cause + fixed test
Explanation: how to read AI debugging output

Practice block (after all 4 lessons):
[TASK A] Generate test cases for a given login page spec
[TASK B] Create an automated Playwright test using AI
[TASK C] Refactor a provided bad test
[TASK D] Fix a provided failing test using AI debugging prompt

MODULE 7: Git Basics for QA (3 lessons)
Lesson 7.1 — Git concepts: repo, commit, branch, push/pull
Lesson 7.2 — Daily workflow: init, add, commit, push to GitHub
Lesson 7.3 — Working with branches: create, switch, merge, PR basics

MODULE 8: CI/CD Basics (3 lessons)
Lesson 8.1 — What CI/CD is and why QA engineers need it
Lesson 8.2 — GitHub Actions: create a workflow that runs Pytest on push
Lesson 8.3 — Reading pipeline results, artifacts, test reports

## FINAL PROJECT (2 pages)
Build a complete automation framework from scratch:
- Python + Playwright + Pytest
- Page Object Model structure
- At least 5 E2E test scenarios (login, search, cart, checkout, logout)
- 3 API tests
- 1 GitHub Actions CI workflow
- README with setup instructions
  Provide: folder structure diagram, all file stubs with comments, step-by-step build instructions

## LESSON FORMAT (STRICT TEMPLATE)

# Every single lesson must follow this exact structure:

🎯 What you will learn   (1–2 sentences, plain language)
💡 Simple explanation    (max 150 words, analogy encouraged, zero jargon)
📋 Code example          (complete, runnable Python + Playwright snippet)
📝 Task                   (1 practical exercise, clear expected output)
✅ Expected result        (exact output or behavior the learner should see)
⚠️ Common mistakes       (2–3 beginner errors for this topic)

## ADDITIONAL SECTIONS

APPENDIX A — Common Beginner Mistakes
- Top 15 mistakes new automation engineers make
- For each: mistake description, why it happens, how to fix it

APPENDIX B — Checklists
- Pre-commit checklist (before pushing tests)
- Test quality checklist (is this a good test?)
- Code review checklist (for automation PRs)
- "Am I ready to apply for junior automation QA?" checklist

APPENDIX C — Interview Preparation
- 20 most common interview questions for junior automation QA
- For each: question, good answer (2–4 sentences), what NOT to say
- 5 practical coding tasks with solutions

APPENDIX D — Tools & Resources
- Official docs links (Playwright, Pytest, requests, Git)
- Free practice sites (Selenium Practice, reqres.in, SauceDemo, The Internet)
- Recommended next learning steps after this course

## PDF FORMATTING REQUIREMENTS

Typography:
Headings: bold, clear hierarchy (H1 → H2 → H3)
Body: 11–12pt, readable line spacing (1.4–1.6)
Code blocks: monospace font, syntax-highlighted, line numbers, copy button

Layout:
Clean white background, generous margins (2cm)
Each module starts on a new page
Page numbers in footer (format: Module X | Page N)
Running header with module name

Visual elements:
Callout boxes for tips, warnings, notes (color-coded)
Tables for comparisons (E2E vs unit vs integration)
Folder structure shown as tree diagram
All code output shown in a distinct output block below code

Navigation:
Bookmarks/outlines panel in PDF
Clickable TOC entries
Back-to-TOC link at end of each module

## CONSTRAINTS & QUALITY RULES

# The AI must enforce these throughout generation:

- NEVER use jargon without immediately explaining it in plain English
- EVERY code example must be complete and runnable (no "..." placeholders)
- EVERY task must have a clear expected result the beginner can verify
- AI module (Module 6) must contain real, copy-pasteable prompt templates
- NO filler content — every sentence must serve the learner
- Total page count must not exceed 45 pages
- All code must be tested against Python 3.10+ and Playwright 1.40+
- Use demo sites only: playwright.dev/test, saucedemo.com, reqres.in
- Do not reference paid tools or services anywhere in the course

## OUTPUT INSTRUCTION

Generate the complete course document now. Start with the cover page and proceed sequentially through every module, lesson, appendix, and section defined above. Do not summarize or abbreviate — write the full content for every lesson following the strict lesson template. Format the entire output as a structured document ready for PDF conversion.
