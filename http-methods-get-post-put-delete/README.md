# HTTP Methods 

HTTP Methods define what action a client wants to perform on a server resource.

They standardize communication behavior between clients and servers.

Without HTTP methods:

- Requests become ambiguous
- APIs become inconsistent
- Clients cannot predict behavior correctly

HTTP methods define how data is:

- Retrieved
- Created
- Updated
- Deleted

---

# Why HTTP Methods Exist

Clients and servers need predictable communication rules.

HTTP methods solve this problem by defining explicit request intent.

Examples:

- Retrieve data
- Create resources
- Update resources
- Delete resources

---

# Core HTTP Method Philosophy

Each HTTP method communicates a specific intention.

Example:

| Method | Meaning |
|---|---|
| GET | Retrieve data |
| POST | Create or submit |
| PUT | Replace resource |
| PATCH | Partially update |
| DELETE | Remove resource |

Correct method usage makes APIs easier to understand and use.

---

# Safe vs Unsafe Methods

## Safe Methods

Safe methods should not modify server state.

Examples:

- GET
- HEAD
- OPTIONS

---

## Unsafe Methods

Unsafe methods may modify server state.

Examples:

- POST
- PUT
- PATCH
- DELETE

---

# Idempotency

Idempotent methods produce the same final result when repeated multiple times.

| Method | Idempotent |
|---|---|
| GET | Yes |
| POST | No |
| PUT | Yes |
| PATCH | Depends |
| DELETE | Yes |

Example:

```http
DELETE /users/10
````

Repeating the request should not create additional side effects.

---

# HTTP Method Overview

| Method  | Purpose                  | Safe | Idempotent |
| ------- | ------------------------ | ---- | ---------- |
| GET     | Retrieve data            | Yes  | Yes        |
| POST    | Create resources         | No   | No         |
| PUT     | Replace resource         | No   | Yes        |
| PATCH   | Partial update           | No   | Depends    |
| DELETE  | Remove resource          | No   | Yes        |
| HEAD    | Retrieve headers only    | Yes  | Yes        |
| OPTIONS | Discover allowed methods | Yes  | Yes        |

---

# Method Selection Guide

| Scenario                  | Recommended Method |
| ------------------------- | ------------------ |
| Retrieve data             | GET                |
| Create resource           | POST               |
| Replace full resource     | PUT                |
| Partially update resource | PATCH              |
| Remove resource           | DELETE             |
| Retrieve headers only     | HEAD               |
| Discover allowed methods  | OPTIONS            |

---

# GET Method

GET retrieves data from a server.

Example:

```http
GET /products/10
```

Response:

```json
{
  "id": 10,
  "name": "Laptop"
}
```

GET should:

* Not modify server state
* Be safe
* Be cacheable
* Be bookmarkable

---

# Common GET Query Parameters

```http
GET /products?page=1&limit=20
```

```http
GET /search?q=laptop
```

Common usage:

* Pagination
* Filtering
* Sorting
* Searching

---

# POST Method

POST submits data or creates resources.

Example:

```http
POST /users
Content-Type: application/json
```

Request Body:

```json
{
  "name": "John Doe"
}
```

POST commonly handles:

* Form submissions
* Resource creation
* File uploads
* Login requests

---

# PUT Method

PUT replaces an entire resource.

Example:

```http
PUT /users/10
```

Request Body:

```json
{
  "name": "John",
  "email": "john@example.com"
}
```

PUT should send the complete resource representation.

---

# PATCH Method

PATCH partially updates a resource.

Example:

```http
PATCH /users/10
```

Request Body:

```json
{
  "email": "new@example.com"
}
```

PATCH updates only specified fields.

---

# DELETE Method

DELETE removes resources.

Example:

```http
DELETE /posts/15
```

DELETE is intended for destructive operations.

---

# HEAD Method

HEAD retrieves response headers without returning the response body.

Example:

```http
HEAD /video.mp4
```

HEAD is useful for:

* Metadata inspection
* File size checking
* Cache validation

---

# OPTIONS Method

OPTIONS discovers allowed operations for a resource.

Example:

```http
OPTIONS /users
```

Response:

```http
Allow: GET, POST, PUT, DELETE
```

OPTIONS is commonly used for:

* API capability discovery
* Browser preflight requests
* CORS negotiation

---

# Browser Behavior

| Method | Bookmarkable | Cached     |
| ------ | ------------ | ---------- |
| GET    | Yes          | Usually    |
| POST   | No           | Usually no |
| PUT    | No           | Usually no |
| PATCH  | No           | Usually no |
| DELETE | No           | Usually no |

---

# HTML Form Support

Standard HTML forms support only:

* GET
* POST

PUT, PATCH, and DELETE usually use JavaScript or API clients.

Example:

```html
<form method="POST">
```

---

# Request Body Rules

| Method  | Request Body |
| ------- | ------------ |
| GET     | Usually no   |
| POST    | Yes          |
| PUT     | Yes          |
| PATCH   | Yes          |
| DELETE  | Sometimes    |
| HEAD    | No           |
| OPTIONS | Usually no   |

---

# Retry Behavior

| Method | Retry Safety              |
| ------ | ------------------------- |
| GET    | Usually safe              |
| POST   | May create duplicates     |
| PUT    | Usually safe              |
| PATCH  | Depends on implementation |
| DELETE | Usually safe              |

---

# Common Status Codes

| Method  | Common Status Codes |
| ------- | ------------------- |
| GET     | 200, 304, 404       |
| POST    | 201, 400, 422       |
| PUT     | 200, 204            |
| PATCH   | 200, 204            |
| DELETE  | 204, 404            |
| HEAD    | 200, 304            |
| OPTIONS | 200, 204            |

---

# Common HTTP Method Mistakes

## Incorrect

```http
GET /delete-user/10
```

GET should not modify server state.

Correct:

```http
DELETE /users/10
```

---

## Incorrect

```http
POST /get-products
```

POST should not be used for simple retrieval.

Correct:

```http
GET /products
```

---

# Real Browser Examples

Opening a webpage:

```http
GET /home
```

Submitting a login form:

```http
POST /login
```

Updating a profile:

```http
PATCH /profile
```

Deleting a comment:

```http
DELETE /comments/10
```

---

# HTTP Methods Comparison Matrix

| Method | Primary Purpose | Safe | Idempotent | Common Usage | Request Body | Common Status Codes | Browser Cacheable | Bookmarkable | HTML Form Support | Typical Response | Common Example |
|---|---|---|---|---|---|---|---|---|---|---|---|
| GET | Retrieve data | Yes | Yes | Reading resources | Usually No | 200, 304, 404 | Usually Yes | Yes | Yes | Resource data | `GET /products/10` |
| POST | Create or submit data | No | No | Form submission, resource creation | Yes | 201, 400, 422 | Usually No | No | Yes | Created resource or result | `POST /users` |
| PUT | Replace entire resource | No | Yes | Full resource replacement | Yes | 200, 204 | Usually No | No | No | Updated resource | `PUT /users/10` |
| PATCH | Partially update resource | No | Depends | Partial field updates | Yes | 200, 204 | Usually No | No | No | Updated resource | `PATCH /users/10` |
| DELETE | Remove resource | No | Yes | Resource deletion | Sometimes | 200, 204, 404 | Usually No | No | No | Deletion confirmation | `DELETE /posts/15` |
| HEAD | Retrieve headers only | Yes | Yes | Metadata inspection | No | 200, 304, 404 | Usually Yes | No | No | Response headers only | `HEAD /video.mp4` |
| OPTIONS | Discover allowed operations | Yes | Yes | Capability discovery, preflight requests | Usually No | 200, 204 | Usually No | No | No | Allowed methods and headers | `OPTIONS /users` |

---

# HTTP Methods Usage Matrix

| Category | GET | POST | PUT | PATCH | DELETE | HEAD | OPTIONS |
|---|---|---|---|---|---|---|---|
| Retrieves Data | Yes | Sometimes | Sometimes | Sometimes | No | Metadata only | No |
| Creates Resources | No | Yes | Sometimes | No | No | No | No |
| Updates Resources | No | Sometimes | Yes | Yes | No | No | No |
| Removes Resources | No | No | No | No | Yes | No | No |
| Changes Server State | No | Yes | Yes | Yes | Yes | No | No |
| Usually Uses Request Body | No | Yes | Yes | Yes | Sometimes | No | Usually No |
| Safe to Refresh Repeatedly | Yes | Usually No | Yes | Depends | Yes | Yes | Yes |
| Suitable for Bookmarking | Yes | No | No | No | No | No | No |
| Commonly Used in Browsers | Very Common | Very Common | Common via JavaScript | Common via JavaScript | Common via JavaScript | Less Common | Automatic browser usage |
| Metadata Retrieval | No | No | No | No | No | Yes | Sometimes |
| Capability Discovery | No | No | No | No | No | No | Yes |
| Common Browser Example | Opening pages | Submitting forms | Saving settings | Updating profile | Deleting comments | Checking file metadata | Browser preflight request |
| Common API Example | `GET /products` | `POST /orders` | `PUT /users/10` | `PATCH /users/10` | `DELETE /posts/15` | `HEAD /report.pdf` | `OPTIONS /api/users` |

---

# HTTP Methods Retry Behavior Matrix

| Method | Retry Safety | Reason |
|---|---|---|
| GET | Usually Safe | Does not modify server state |
| POST | Risky | May create duplicate actions |
| PUT | Usually Safe | Same request produces same final state |
| PATCH | Depends | Depends on update implementation |
| DELETE | Usually Safe | Resource remains deleted |
| HEAD | Safe | Metadata retrieval only |
| OPTIONS | Safe | Capability inspection only |

---

# HTTP Methods Request Body Matrix

| Method | Request Body Usage | Typical Body Content |
|---|---|---|
| GET | Usually No | Query parameters in URL |
| POST | Yes | Form data, JSON, files |
| PUT | Yes | Full resource representation |
| PATCH | Yes | Partial field updates |
| DELETE | Sometimes | Optional deletion metadata |
| HEAD | No | None |
| OPTIONS | Usually No | Optional capability metadata |

---

# HTTP Methods Browser Behavior Matrix

| Method | Can Be Cached | Can Be Bookmarked | Can Appear in Browser History | Common Browser Warning |
|---|---|---|---|---|
| GET | Yes | Yes | Yes | Usually none |
| POST | Usually No | No | Sometimes | Form resubmission warning |
| PUT | Usually No | No | Sometimes | None |
| PATCH | Usually No | No | Sometimes | None |
| DELETE | Usually No | No | Sometimes | None |
| HEAD | Yes | No | Rarely | None |
| OPTIONS | Usually No | No | Rarely | None |

---

# HTTP Methods Common Use Case Matrix

| Scenario | Recommended Method |
|---|---|
| Load homepage | GET |
| Submit login form | POST |
| Upload profile image | POST |
| Replace profile settings | PUT |
| Update email address only | PATCH |
| Delete notification | DELETE |
| Check file metadata | HEAD |
| Discover supported API methods | OPTIONS |

---

# Final Conclusion

HTTP methods define how clients and servers communicate.

Each method has a specific purpose and expected behavior.

Understanding HTTP methods correctly helps developers:

* Build predictable APIs
* Use request semantics correctly
* Avoid incorrect API behavior
* Improve client-server communication

HTTP methods are communication rules that standardize web interactions.


