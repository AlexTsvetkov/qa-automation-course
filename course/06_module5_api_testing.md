# Module 5: API Testing

**Overview:** Not all testing happens in a browser. APIs (Application Programming Interfaces) are how systems talk to each other behind the scenes. When you click "Login," the browser sends an API request to the server. API testing lets you test this directly — faster and more reliably than browser tests. This module teaches you to test APIs using Python's `requests` library.

---

## Lesson 5.1 — What Is an API

🎯 **What you will learn:** What an API is, how HTTP requests work, and how to read API documentation.

💡 **Simple explanation:** Think of a restaurant. You (the client) don't go into the kitchen. You give your order to a waiter (the API), who takes it to the kitchen (the server) and brings back your food (the response). An API works the same way — your browser sends a request, and the server sends back data. HTTP is the language they use. The main request types are: GET (give me data), POST (create something), PUT (update something), DELETE (remove something).

📋 **Code example:**

```python
# api_basics.py
import requests

# GET — fetch data
response = requests.get("https://jsonplaceholder.typicode.com/users/1")

print(f"Status Code: {response.status_code}")
print(f"Content Type: {response.headers['Content-Type']}")

data = response.json()
print(f"\nUser name: {data['name']}")
print(f"Email: {data['email']}")
print(f"City: {data['address']['city']}")
```

✅ **Expected result:**

```
Status Code: 200
Content Type: application/json; charset=utf-8

User name: Leanne Graham
Email: Sincere@april.biz
City: Gwenborough
```

📝 **Task:** Use `requests.get()` to fetch all users from `https://jsonplaceholder.typicode.com/users`. Print the total number of users and the name + email of each one.

✅ **Expected result:** 10 users listed with their names and emails.

⚠️ **Common mistakes:**

1. **Forgetting `.json()`.** `response` is an object. Call `.json()` to get the actual data as a dict/list.
2. **Not checking status code.** Always verify `response.status_code == 200` before using the data.
3. **Confusing GET and POST.** GET fetches data. POST sends data to create something.

---

## Lesson 5.2 — requests Library — GET, POST

🎯 **What you will learn:** How to send GET and POST requests, pass parameters, send JSON bodies, and handle responses.

💡 **Simple explanation:** GET is like asking a question: "Give me user #5." POST is like filling out a form: "Here's the data for a new user — please create it." With `requests`, you just call `requests.get()` or `requests.post()` and Python handles all the technical details.

📋 **Code example:**

```python
# requests_demo.py
import requests

BASE_URL = "https://jsonplaceholder.typicode.com"

# GET with query parameters
print("=== GET: Search posts by userId ===")
response = requests.get(f"{BASE_URL}/posts", params={"userId": 1})
posts = response.json()
print(f"Found {len(posts)} posts for user 1")
print(f"First post title: {posts[0]['title']}")

# POST — create a new resource
print("\n=== POST: Create a new post ===")
new_post = {
    "title": "My Test Post",
    "body": "This post was created by an API test",
    "userId": 1
}
response = requests.post(f"{BASE_URL}/posts", json=new_post)
print(f"Status: {response.status_code}")
created = response.json()
print(f"Created post ID: {created['id']}")
print(f"Title: {created['title']}")

# PUT — update a resource
print("\n=== PUT: Update a post ===")
updated_data = {
    "id": 1,
    "title": "Updated Title",
    "body": "Updated body content",
    "userId": 1
}
response = requests.put(f"{BASE_URL}/posts/1", json=updated_data)
print(f"Status: {response.status_code}")
print(f"Updated title: {response.json()['title']}")

# DELETE — remove a resource
print("\n=== DELETE: Delete a post ===")
response = requests.delete(f"{BASE_URL}/posts/1")
print(f"Status: {response.status_code}")
```

✅ **Expected result:**

```
=== GET: Search posts by userId ===
Found 10 posts for user 1
First post title: sunt aut facere repellat provident occaecati excepturi optio reprehenderit

=== POST: Create a new post ===
Status: 201
Created post ID: 101
Title: My Test Post

=== PUT: Update a post ===
Status: 200
Updated title: Updated Title

=== DELETE: Delete a post ===
Status: 200
```

📝 **Task:** Create a new post with POST, then immediately fetch it with GET using the returned ID. Print both responses to verify consistency.

⚠️ **Common mistakes:**

1. **Using `data=` instead of `json=` for JSON bodies.** `json=` automatically sets the Content-Type header and serializes.
2. **Assuming POST returns 200.** Successful creation typically returns **201** (Created).
3. **Not handling errors.** What if the server returns 500? Always check status first.

---

## Lesson 5.3 — Pytest + API Tests

🎯 **What you will learn:** How to write structured API tests using Pytest with fixtures, assertions, and parametrize.

💡 **Simple explanation:** API tests follow the same pattern as any test: arrange (set up data), act (send the request), assert (check the response). With Pytest, you can use fixtures for the base URL and shared setup, parametrize for testing multiple endpoints, and clear assertions for status codes and response bodies.

📋 **Code example:**

`conftest.py`:

```python
# conftest.py
import pytest
import requests

BASE_URL = "https://jsonplaceholder.typicode.com"

@pytest.fixture
def base_url():
    return BASE_URL

@pytest.fixture
def sample_post():
    return {
        "title": "Test Post",
        "body": "This is a test post body",
        "userId": 1
    }

@pytest.fixture
def api_session():
    session = requests.Session()
    session.headers.update({"Content-Type": "application/json"})
    yield session
    session.close()
```

`test_api_users.py`:

```python
# test_api_users.py
import requests
import pytest

def test_get_all_users(base_url):
    response = requests.get(f"{base_url}/users")
    assert response.status_code == 200
    users = response.json()
    assert len(users) == 10, f"Expected 10 users, got {len(users)}"

def test_get_single_user(base_url):
    response = requests.get(f"{base_url}/users/1")
    assert response.status_code == 200
    user = response.json()
    assert user["id"] == 1
    assert user["name"] == "Leanne Graham"
    assert "email" in user

def test_get_nonexistent_user(base_url):
    response = requests.get(f"{base_url}/users/999")
    assert response.status_code == 404

@pytest.mark.parametrize("user_id, expected_name", [
    (1, "Leanne Graham"),
    (2, "Ervin Howell"),
    (3, "Clementine Bauch"),
])
def test_user_names(base_url, user_id, expected_name):
    response = requests.get(f"{base_url}/users/{user_id}")
    assert response.status_code == 200
    assert response.json()["name"] == expected_name
```

`test_api_posts.py`:

```python
# test_api_posts.py
import requests

def test_create_post(base_url, sample_post):
    response = requests.post(f"{base_url}/posts", json=sample_post)
    assert response.status_code == 201
    data = response.json()
    assert data["title"] == sample_post["title"]
    assert data["body"] == sample_post["body"]
    assert "id" in data

def test_update_post(base_url):
    updated = {"title": "Updated Title", "body": "Updated body", "userId": 1}
    response = requests.put(f"{base_url}/posts/1", json=updated)
    assert response.status_code == 200
    assert response.json()["title"] == "Updated Title"

def test_delete_post(base_url):
    response = requests.delete(f"{base_url}/posts/1")
    assert response.status_code == 200
```

✅ **Expected result:** `pytest test_api_users.py test_api_posts.py -v` — all tests pass.

📝 **Task:** Write a test that creates a post, then fetches all posts for that userId, and verifies the post count increased.

⚠️ **Common mistakes:**

1. **Not using fixtures for shared values.** Don't repeat the base URL in every test.
2. **Only checking status code.** Also verify the response body — correct status with wrong data is still a bug.
3. **Forgetting parametrize for similar tests.** If 3 tests differ only by input, use parametrize.

---

## Lesson 5.4 — Response Validation

🎯 **What you will learn:** How to thoroughly validate API responses — status codes, headers, body structure, data types, and response time.

💡 **Simple explanation:** A proper API test doesn't just check "did it return 200?" It also checks: Is the data structure correct? Are all required fields present? Are the data types right? Is the response fast enough? Think of it as checking a delivered package — correct address (status), correct items (body), not damaged (structure), delivered on time (speed).

📋 **Code example:**

```python
# test_response_validation.py
import requests
import pytest

BASE_URL = "https://jsonplaceholder.typicode.com"

def test_response_status_and_headers():
    response = requests.get(f"{BASE_URL}/users/1")

    # Status code
    assert response.status_code == 200

    # Headers
    assert "application/json" in response.headers["Content-Type"]

    # Response time (in seconds)
    assert response.elapsed.total_seconds() < 5, "Response took too long!"

def test_user_response_structure():
    response = requests.get(f"{BASE_URL}/users/1")
    user = response.json()

    # Required fields exist
    required_fields = ["id", "name", "username", "email", "phone", "website"]
    for field in required_fields:
        assert field in user, f"Missing required field: {field}"

    # Data types
    assert isinstance(user["id"], int), "ID should be an integer"
    assert isinstance(user["name"], str), "Name should be a string"
    assert isinstance(user["email"], str), "Email should be a string"

    # Nested structure
    assert "address" in user
    assert "street" in user["address"]
    assert "city" in user["address"]

def test_list_response_structure():
    response = requests.get(f"{BASE_URL}/posts")
    posts = response.json()

    # Is a list
    assert isinstance(posts, list)
    assert len(posts) > 0

    # Each item has required fields
    for post in posts[:3]:
        assert "id" in post
        assert "title" in post
        assert "body" in post
        assert "userId" in post

def test_error_response():
    response = requests.get(f"{BASE_URL}/users/99999")
    assert response.status_code == 404
```

✅ **Expected result:** All validation tests pass.

📝 **Task:** Write a comprehensive validation test for the `/posts/1` endpoint that checks: status code, content type, response time under 3 seconds, all required fields present, correct data types for each field, and that `userId` equals 1.

⚠️ **Common mistakes:**

1. **Only testing the happy path.** Test error responses (404, 400, 500) too.
2. **Skipping type checks.** An API returning `"123"` instead of `123` is a real bug.
3. **Ignoring response time.** A working but slow API is still broken for users.

---

## Mini-Project 5

**Task:** Build a complete API test suite for JSONPlaceholder.

```python
# test_jsonplaceholder.py — Mini-Project 5
import requests
import pytest

BASE_URL = "https://jsonplaceholder.typicode.com"

class TestUsers:
    """API tests for /users endpoint."""

    def test_get_all_users(self):
        response = requests.get(f"{BASE_URL}/users")
        assert response.status_code == 200
        users = response.json()
        assert len(users) == 10

    def test_get_user_by_id(self):
        response = requests.get(f"{BASE_URL}/users/1")
        assert response.status_code == 200
        user = response.json()
        assert user["name"] == "Leanne Graham"

    def test_user_has_required_fields(self):
        response = requests.get(f"{BASE_URL}/users/1")
        user = response.json()
        for field in ["id", "name", "username", "email"]:
            assert field in user

    def test_nonexistent_user_returns_404(self):
        response = requests.get(f"{BASE_URL}/users/0")
        assert response.status_code == 404

class TestPosts:
    """API tests for /posts endpoint."""

    def test_get_all_posts(self):
        response = requests.get(f"{BASE_URL}/posts")
        assert response.status_code == 200
        assert len(response.json()) == 100

    def test_create_post(self):
        payload = {"title": "Test", "body": "Content", "userId": 1}
        response = requests.post(f"{BASE_URL}/posts", json=payload)
        assert response.status_code == 201
        assert response.json()["title"] == "Test"

    def test_update_post(self):
        payload = {"title": "Updated"}
        response = requests.patch(f"{BASE_URL}/posts/1", json=payload)
        assert response.status_code == 200
        assert response.json()["title"] == "Updated"

    def test_delete_post(self):
        response = requests.delete(f"{BASE_URL}/posts/1")
        assert response.status_code == 200

    @pytest.mark.parametrize("user_id, expected_count", [
        (1, 10),
        (2, 10),
        (3, 10),
    ])
    def test_posts_per_user(self, user_id, expected_count):
        response = requests.get(f"{BASE_URL}/posts", params={"userId": user_id})
        assert response.status_code == 200
        assert len(response.json()) == expected_count

class TestResponseQuality:
    """Tests for response quality metrics."""

    def test_response_time(self):
        response = requests.get(f"{BASE_URL}/users")
        assert response.elapsed.total_seconds() < 5

    def test_content_type_is_json(self):
        response = requests.get(f"{BASE_URL}/users")
        assert "application/json" in response.headers["Content-Type"]
```

✅ **Expected result:** `pytest test_jsonplaceholder.py -v` — all 12+ tests pass.

**Module 5 Checklist:**
- [ ] I understand what APIs are and how HTTP works
- [ ] I can send GET, POST, PUT, DELETE requests with Python
- [ ] I can write API tests with Pytest
- [ ] I can validate response status, headers, body, and timing

[↑ Back to Table of Contents](#table-of-contents)