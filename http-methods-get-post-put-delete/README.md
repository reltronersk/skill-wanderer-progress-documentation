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

| Category | GET | POST | PUT | PATCH | DELETE | HEAD | OPTIONS |
|---|---|---|---|---|---|---|---|
| Primary Purpose | Retrieve data | Create or submit data | Replace entire resource | Partially update resource | Remove resource | Retrieve headers only | Discover supported operations |
| Safe Method | Yes | No | No | No | No | Yes | Yes |
| Idempotent | Yes | No | Yes | Depends on implementation | Yes | Yes | Yes |
| Changes Server State | No | Yes | Yes | Yes | Yes | No | No |
| Common Usage | Reading resources | Form submission | Full replacement | Partial update | Resource deletion | Metadata retrieval | Capability discovery |
| Typical Request Body | Usually none | Yes | Yes | Yes | Sometimes | No | Usually none |
| Typical Response Body | Resource data | Created resource or result | Updated resource | Updated resource | Confirmation or empty | Headers only | Allowed methods or headers |
| Browser Bookmarkable | Yes | No | No | No | No | No | No |
| Browser Cacheable | Usually yes | Usually no | Usually no | Usually no | Usually no | Usually yes | Usually no |
| Browser Refresh Safe | Yes | Usually no | Usually yes | Depends | Usually yes | Yes | Yes |
| Common HTML Form Support | Yes | Yes | No native support | No native support | No native support | No | No |
| Typical HTML Form Method | GET | POST | Requires JavaScript | Requires JavaScript | Requires JavaScript | Not used in forms | Not used in forms |
| Query Parameter Usage | Very common | Sometimes | Sometimes | Sometimes | Sometimes | Sometimes | Sometimes |
| Typical Payload Size | Small | Small to large | Medium to large | Usually small | Small | Very small | Very small |
| Resource Creation | No | Yes | Sometimes | No | No | No | No |
| Full Resource Replacement | No | No | Yes | No | No | No | No |
| Partial Resource Update | No | No | No | Yes | No | No | No |
| Resource Removal | No | No | No | No | Yes | No | No |
| Metadata Retrieval | No | No | No | No | No | Yes | Sometimes |
| Capability Discovery | No | No | No | No | No | No | Yes |
| Common Content Types | Query string | JSON, form-data | JSON | JSON | Usually none | None | None |
| Common Status Codes | 200, 304, 404 | 201, 400, 422 | 200, 204 | 200, 204 | 204, 404 | 200, 304 | 200, 204 |
| Retry Safety | Usually safe | May create duplicates | Usually safe | Depends | Usually safe | Usually safe | Usually safe |
| Typical URL Pattern | `/products/10` | `/users` | `/users/10` | `/users/10` | `/users/10` | `/files/report.pdf` | `/api/users` |
| Request Semantics | Read-only retrieval | Submission or creation | Full replacement | Partial modification | Deletion | Header inspection | Operation inspection |
| Side Effects Allowed | No | Yes | Yes | Yes | Yes | No | No |
| Common Frontend Usage | Loading pages | Login forms | Updating profiles | Updating settings | Delete buttons | File checking | CORS preflight |
| Common Backend Usage | Fetching records | Creating records | Synchronizing resources | Updating fields | Removing records | Metadata validation | Method validation |
| Typical API Example | `GET /products` | `POST /orders` | `PUT /users/10` | `PATCH /users/10` | `DELETE /posts/15` | `HEAD /video.mp4` | `OPTIONS /users` |
| URL Visibility | Visible in URL | Usually hidden in body | Usually hidden in body | Usually hidden in body | Usually hidden | Visible URL only | Visible URL only |
| Sensitive Data Suitability | Poor | Good | Good | Good | Acceptable | Poor | Acceptable |
| Common Mistake | Mutating state | Using for retrieval | Partial update misuse | Full replacement misuse | Missing authorization | Returning body accidentally | Missing CORS headers |
| Best Used When | Reading data | Sending new data | Replacing complete data | Updating specific fields | Removing resources | Checking metadata | Discovering allowed operations |
| Typical Client Behavior | Frequent retrieval | Interactive submission | Controlled updates | Lightweight edits | Explicit destructive action | Metadata checks | Browser negotiation |
| Common Real Example | Opening webpages | Registering accounts | Replacing profile data | Updating email only | Deleting comments | Checking file size | Browser preflight request |

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


