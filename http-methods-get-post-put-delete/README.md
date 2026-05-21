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

# Final Conclusion

HTTP methods define how clients and servers communicate.

Each method has a specific purpose and expected behavior.

Understanding HTTP methods correctly helps developers:

* Build predictable APIs
* Use request semantics correctly
* Avoid incorrect API behavior
* Improve client-server communication

HTTP methods are communication rules that standardize web interactions.


