# HTTP Methods – A Beginner-Friendly Guide

HTTP methods are like **actions** you ask a server to do.  
Every time you visit a website, submit a form, or use an app, your browser or app sends a request to a server using one of these methods.  
They tell the server: “Give me this page,” “Save this data,” “Update that,” or “Delete that.”

---

## Why Do We Need HTTP Methods?

Without clear actions, communication between your browser/app and a server would be confusing and unpredictable.  
HTTP methods give **a shared set of rules** so everyone knows what’s going on.

They handle four main data operations:
- **Getting** information (reading)
- **Creating** new things
- **Updating** existing things
- **Deleting** things

---

## The Main Idea: Each Method Has a Meaning

Every method says **what you want to happen**.

| Method  | Simple meaning                              |
|---------|---------------------------------------------|
| GET     | “Show me this information”                  |
| POST    | “Create something new with this data”       |
| PUT     | “Replace the whole thing with my new data”  |
| PATCH   | “Only update the parts I’m sending”         |
| DELETE  | “Remove this”                               |

Using the right method makes your APIs and websites easier to understand and work with.

---

## Safe vs. Unsafe Methods

### Safe Methods
Safe methods **don’t change anything** on the server. They’re like reading a book – you only look at the information.

Examples: `GET`, `HEAD`, `OPTIONS`

### Unsafe Methods
Unsafe methods **may change data** on the server. They’re like writing in a notebook.

Examples: `POST`, `PUT`, `PATCH`, `DELETE`

---

## Idempotency: Does Repeating the Request Matter?

An **idempotent** method is one that you can call many times and the **end result stays the same** as if you only called it once.

| Method  | Idempotent? | Why?                                                                |
|---------|-------------|---------------------------------------------------------------------|
| GET     | ✅ Yes      | Reading the same thing again doesn’t change it.                     |
| POST    | ❌ No       | Creating a user twice could make two copies (duplicates).           |
| PUT     | ✅ Yes      | Replacing something with the same data gives the same final state.  |
| PATCH   | ⚠️ Depends  | Some partial updates are safe to repeat, some are not.              |
| DELETE  | ✅ Yes      | Deleting something that’s already deleted has no extra side effect. |

Example:  
If you send `DELETE /users/10` twice, the user is deleted after the first call and nothing extra happens on the second one.

---

## Quick Overview of All Common Methods

| Method   | Purpose                            | Safe? | Idempotent? |
|----------|------------------------------------|-------|-------------|
| GET      | Retrieve data                      | Yes   | Yes         |
| POST     | Create new resources               | No    | No          |
| PUT      | Replace whole resource             | No    | Yes         |
| PATCH    | Update parts of a resource         | No    | Depends     |
| DELETE   | Remove a resource                  | No    | Yes         |
| HEAD     | Get only headers (no body)         | Yes   | Yes         |
| OPTIONS  | Find out what methods are allowed  | Yes   | Yes         |

---

## Which Method Should I Use?

| What you want to do                     | Use this method |
|-----------------------------------------|-----------------|
| Fetch data / load a page                | GET             |
| Create a new resource (signup, upload)  | POST            |
| Replace a whole resource                | PUT             |
| Update only some fields                 | PATCH           |
| Delete a resource                       | DELETE          |
| Check file info without downloading it  | HEAD            |
| Ask what operations a URL supports      | OPTIONS         |

---

## GET – Ask for Information

`GET` is used whenever you want to **read** data from the server.  
It should **never change** anything on the server.

Example:  
```http
GET /products/10
```
Response (the product info):
```json
{
  "id": 10,
  "name": "Laptop"
}
```

GET is:
- **Safe** (doesn’t change server state)
- **Cacheable** (browser can save a copy)
- **Bookmarkable** (you can save the link)
- **Linkable** (you can share it)

### Common Query Parameters

```
GET /products?page=1&limit=20
GET /search?q=laptop
```

Used for:
- Pagination
- Filtering
- Sorting
- Searching

---

## POST – Send Data to Create Something

`POST` is for **submitting data** and usually **creating a new resource**.

Example:  
```http
POST /users
Content-Type: application/json
```
Body:
```json
{
  "name": "John Doe"
}
```

POST is typically used for:
- Form submissions (login, signup)
- Creating a new user, order, post
- File uploads
- Any action where you’re adding something new

---

## PUT – Replace the Whole Thing

`PUT` **replaces an entire resource** with the data you send.  
You must send the **complete** updated version.

Example:  
```http
PUT /users/10
```
Body:
```json
{
  "name": "John",
  "email": "john@example.com"
}
```

If you only send the email, the `name` may be erased because PUT expects the **full resource**.

---

## PATCH – Update Only Certain Fields

`PATCH` makes a **partial update**. You only send the fields you want to change.

Example:  
```http
PATCH /users/10
```
Body:
```json
{
  "email": "new@example.com"
}
```
Only the email is changed; the rest of the user data stays as it was.

---

## DELETE – Remove Something

`DELETE` removes a resource permanently.

Example:  
```http
DELETE /posts/15
```

---

## HEAD – Get Only the Headers

`HEAD` works exactly like `GET` but **does not return the body** (the actual content).  
It only sends back the headers (information *about* the resource).

Useful for:
- Checking file size before downloading
- Verifying if a page has changed (cache validation)
- Seeing metadata

Example:  
```http
HEAD /video.mp4
```

---

## OPTIONS – Ask What’s Allowed

`OPTIONS` asks the server: “What methods can I use on this URL?”

Example:  
```http
OPTIONS /users
```
Response header:
```http
Allow: GET, POST, PUT, DELETE
```

Commonly used for:
- Discovering what operations an API supports
- Browser **preflight** requests (CORS checks)

---

## How Browsers Behave with Each Method

| Method   | Can I bookmark it? | Does the browser cache it? |
|----------|--------------------|----------------------------|
| GET      | ✅ Yes             | Usually yes                |
| POST     | ❌ No              | Usually no                 |
| PUT      | ❌ No              | Usually no                 |
| PATCH    | ❌ No              | Usually no                 |
| DELETE   | ❌ No              | Usually no                 |
| HEAD     | ❌ No              | Yes (headers)              |
| OPTIONS  | ❌ No              | Usually no                 |

---

## HTML Forms – Which Methods Can They Use?

Standard HTML forms **only support** `GET` and `POST`.

Example:
```html
<form method="POST" action="/login">
```

To use `PUT`, `PATCH` or `DELETE`, you usually need JavaScript or a special API tool.

---

## Do I Need to Send a Request Body?

| Method   | Body included?    |
|----------|-------------------|
| GET      | Usually no        |
| POST     | Yes               |
| PUT      | Yes               |
| PATCH    | Yes               |
| DELETE   | Sometimes         |
| HEAD     | No                |
| OPTIONS  | Usually no        |

---

## Retry Behaviour – Is It Safe to Try Again?

| Method   | Safe to retry?     | Explanation                                     |
|----------|--------------------|-------------------------------------------------|
| GET      | Usually safe       | No changes happen                               |
| POST     | Risky              | Might create duplicates (e.g., two orders)      |
| PUT      | Usually safe       | Same data → same final state                    |
| PATCH    | Depends            | Some updates are safe, some are not             |
| DELETE   | Usually safe       | Deleting again does nothing extra               |
| HEAD     | Safe               | No body or changes                              |
| OPTIONS  | Safe               | Just a question, no changes                     |

---

## Common Response Codes (What the Server Answers)

| Method   | Typical status codes | What they mean                                     |
|----------|----------------------|----------------------------------------------------|
| GET      | 200, 304, 404        | 200 OK, 304 Not Modified (cached), 404 Not Found   |
| POST     | 201, 400, 422        | 201 Created, 400 Bad Request, 422 Unprocessable    |
| PUT      | 200, 204             | 200 OK (with body), 204 No Content (success)       |
| PATCH    | 200, 204             | Same as PUT                                        |
| DELETE   | 204, 404             | 204 Deleted, 404 Already gone or not found         |
| HEAD     | 200, 304             | 200 OK (headers only), 304 Not Modified            |
| OPTIONS  | 200, 204             | 200 OK, 204 No Content                             |

---

## Common Mistakes (and How to Fix Them)

❌ **Using GET to delete something**  
`GET /delete-user/10`  
**Why it’s wrong:** GET should never change server state. A browser might accidentally trigger it again.  

✅ **Correct:**  
`DELETE /users/10`

❌ **Using POST just to fetch data**  
`POST /get-products`  
**Why it’s wrong:** POST is for creating/submitting, not for reading. It’s not cacheable and breaks expectations.  

✅ **Correct:**  
`GET /products`

---

## Real-Life Browser Examples

- Opening a webpage:  
  `GET /home`
- Submitting a login form:  
  `POST /login`
- Updating your profile (with JavaScript):  
  `PATCH /profile`
- Deleting a comment:  
  `DELETE /comments/10`

---

# In-Depth Reference Tables

## HTTP Methods Comparison Matrix

| Method  | Primary purpose        | Safe? | Idempotent? | Common usage              | Has body?  | Typical codes | Cacheable? | Bookmarkable? | HTML form |
|---------|------------------------|-------|-------------|---------------------------|------------|---------------|------------|---------------|-----------|
| GET     | Retrieve data          | Yes   | Yes         | Reading resources         | Usually no | 200, 304, 404 | Usually    | Yes           | Yes       |
| POST    | Create / submit data   | No    | No          | Forms, resource creation  | Yes        | 201, 400, 422 | Usually no | No            | Yes       |
| PUT     | Replace full resource  | No    | Yes         | Full update               | Yes        | 200, 204      | Usually no | No            | No        |
| PATCH   | Partial update         | No    | Depends     | Update specific fields    | Yes        | 200, 204      | Usually no | No            | No        |
| DELETE  | Remove resource        | No    | Yes         | Deletion                  | Sometimes  | 204, 404      | Usually no | No            | No        |
| HEAD    | Headers only           | Yes   | Yes         | Metadata, file size check | No         | 200, 304      | Usually    | No            | No        |
| OPTIONS | Discover allowed methods | Yes | Yes        | Capability, preflight     | Usually no | 200, 204      | Usually no | No            | No        |

---

## Usage Matrix – What Does Each Method Do?

| Feature                  | GET | POST  | PUT  | PATCH | DELETE | HEAD | OPTIONS |
|--------------------------|-----|-------|------|-------|--------|------|---------|
| Retrieves data           | ✅  | Sometimes | Sometimes | Sometimes | ❌  | ✅ (metadata) | ❌  |
| Creates new resources    | ❌  | ✅    | Sometimes | ❌  | ❌    | ❌  | ❌  |
| Updates resources        | ❌  | Sometimes | ✅  | ✅   | ❌    | ❌  | ❌  |
| Deletes resources        | ❌  | ❌    | ❌  | ❌   | ✅    | ❌  | ❌  |
| Changes server state     | ❌  | ✅    | ✅  | ✅   | ✅    | ❌  | ❌  |
| Usually has request body | ❌  | ✅    | ✅  | ✅   | Sometimes | ❌  | Usually ❌ |
| Safe to retry            | ✅  | ❌ (risky) | ✅ | Depends | ✅ | ✅ | ✅ |
| Bookmarkable             | ✅  | ❌    | ❌  | ❌   | ❌    | ❌  | ❌  |
| Common browser use       | Very common | Very common | Via JS | Via JS | Via JS | Less common | Automatic (preflight) |
| Checks metadata          | ❌  | ❌    | ❌  | ❌   | ❌    | ✅  | Sometimes |
| Discovers allowed ops    | ❌  | ❌    | ❌  | ❌   | ❌    | ❌  | ✅ |

---

## Retry Behaviour Matrix

| Method   | Safe to retry? | Why?                                          |
|----------|----------------|-----------------------------------------------|
| GET      | ✅ Safe        | Doesn’t change anything                       |
| POST     | ⚠️ Risky       | Could create duplicates                       |
| PUT      | ✅ Safe        | Same replacement = same end state             |
| PATCH    | ⚠️ Depends     | Some partial updates are repeatable, some not |
| DELETE   | ✅ Safe        | Deleting again has no extra effect            |
| HEAD     | ✅ Safe        | Only reads headers                            |
| OPTIONS  | ✅ Safe        | Just asks a question                          |

---

## Request Body Matrix – What Goes in the Request?

| Method   | Body?       | Typical content                     |
|----------|-------------|-------------------------------------|
| GET      | Usually no  | Only query parameters in the URL    |
| POST     | Yes         | Form data, JSON, files              |
| PUT      | Yes         | Full resource (all fields)          |
| PATCH    | Yes         | Only the fields to update           |
| DELETE   | Sometimes   | Some APIs accept extra metadata     |
| HEAD     | No          | Nothing                             |
| OPTIONS  | Usually no  | Sometimes capability details        |

---

## Browser Behaviour Matrix

| Method   | Cacheable? | Bookmarkable? | Appears in history? | Common warning?            |
|----------|------------|---------------|---------------------|----------------------------|
| GET      | Yes        | Yes           | Yes                 | Usually none               |
| POST     | Usually no | No            | Sometimes           | Form resubmission warning  |
| PUT      | Usually no | No            | Sometimes           | None                       |
| PATCH    | Usually no | No            | Sometimes           | None                       |
| DELETE   | Usually no | No            | Sometimes           | None                       |
| HEAD     | Yes        | No            | Rarely              | None                       |
| OPTIONS  | Usually no | No            | Rarely              | None                       |

---

## Common Use Case Cheat Sheet

| Scenario                     | Method   |
|------------------------------|----------|
| Load a webpage               | GET      |
| Submit login form            | POST     |
| Upload a profile picture     | POST     |
| Replace all profile settings | PUT      |
| Change only your email       | PATCH    |
| Delete a notification        | DELETE   |
| Check file size (no download)| HEAD     |
| Find out what an API can do  | OPTIONS  |

---

# Final Summary

HTTP methods are the **basic language** that browsers, apps, and servers use to talk to each other.  
Each method has a specific job and expected behaviour.

Learning them properly helps you:
- Build predictable and easy-to-use APIs
- Use the right action for the right task
- Avoid confusing or broken web applications

Think of them as the verbs of the web – once you know what each one does, writing and understanding web requests becomes much simpler.
