# Headers Basics

HTTP headers are **small pieces of information** sent together with an HTTP request or response.

Headers are not the main content. They are not the URL. They are not the HTTP method.

Headers give extra context so the client and server understand how to handle the message.

For beginner learners, the most important mindset is:

> Headers answer questions like:  
> **What format is this data?**  
> **What format do I want back?**  
> **Am I sending a token?**  
> **Is the browser sending a cookie?**  
> **Is this response fresh or cached?**

This lesson focuses only on **usage**: how to read, send, and debug headers as an API user.

It does not cover API design, server configuration, or advanced security policy.

---

## 1. The Simple Mental Model

Imagine ordering food through an app.

- The **URL** is the restaurant address.
- The **HTTP method** is the action: view menu, create order, update order, cancel order.
- The **body** is the actual order details.
- The **headers** are the extra instructions: “I am sending JSON”, “I want JSON back”, “Here is my login token”, or “This request came from a browser.”

Headers are like labels attached to the request or response.

They do not usually contain the main data. They help explain how the main data should be handled.

---

## 2. What Does a Header Look Like?

A header usually has this format:

```http
Header-Name: Header Value
```

Beginner example:

```http
Content-Type: application/json
```

### Situation

You submit a signup form from a web app.

### Why this header matters

The app may send your form data as JSON. The server needs to know that the request body should be read as JSON.

### Beginner translation

```http
Content-Type: application/json
```

means:

> “The data I am sending is JSON. Please read it as JSON.”

---

## 3. Request Headers vs Response Headers

There are two places where you will commonly see headers.

| Header Type | Direction | Beginner Meaning |
| --- | --- | --- |
| Request headers | Client → Server | “Here is extra information about my request.” |
| Response headers | Server → Client | “Here is extra information about my response.” |

---

## 4. Request Header Example: Opening a Product List

### Situation

You open a product list page in an app.

The app needs to fetch products from an API.

### Request

```http
GET /products HTTP/1.1
Accept: application/json
User-Agent: Mozilla/5.0
```

### What to notice

| Part | Meaning |
| --- | --- |
| `GET /products` | The client wants to read product data. |
| `Accept: application/json` | The client prefers JSON response. |
| `User-Agent: Mozilla/5.0` | The request came from a browser-like client. |

### Beginner relevance

This example is relevant because beginners often think API requests are only about the URL.

But even a simple product list request may include headers that explain:

- what response format the client wants
- what tool or browser made the request
- how the server may choose the response

When an API gives a strange response, headers can help explain why.

---

## 5. Response Header Example: Receiving Product Data

### Situation

The server responds to the product list request.

### Response

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=60
```

Body:

```json
[
  {
    "id": 1,
    "name": "Keyboard"
  },
  {
    "id": 2,
    "name": "Mouse"
  }
]
```

### What to notice

| Part | Meaning |
| --- | --- |
| `HTTP/1.1 200 OK` | The request succeeded. |
| `Content-Type: application/json` | The response body is JSON. |
| `Cache-Control: max-age=60` | The response may be treated as fresh for a short time. |

### Beginner relevance

This example matters because the response body only shows the data.

The response headers explain how to interpret that data.

If the response body looks like JSON, you can confirm it by checking:

```http
Content-Type: application/json
```

If the data looks old, you can check cache-related headers like:

```http
Cache-Control: max-age=60
```

---

# Important Headers You Must Recognize

---

## 6. `Content-Type`

`Content-Type` explains the format of the body being sent or received.

Beginner translation:

> “This data is in this format.”

Common values:

| Content-Type | Beginner Meaning | Common Situation |
| --- | --- | --- |
| `application/json` | The body is JSON. | API request/response |
| `text/html` | The body is HTML. | Web page response |
| `text/plain` | The body is plain text. | Simple text response |
| `multipart/form-data` | The body may contain files and form fields. | Upload form |
| `application/x-www-form-urlencoded` | The body is form-like key-value data. | Traditional form submit |

---

## 7. `Content-Type` Example: Sending a Signup Form as JSON

### Situation

A beginner clicks a “Register” button in a frontend app.

The frontend sends the signup data to the API.

### Request

```http
POST /register HTTP/1.1
Content-Type: application/json
```

Body:

```json
{
  "name": "Alya",
  "email": "alya@example.com",
  "password": "secret123"
}
```

### What to notice

The body is JSON:

```json
{
  "name": "Alya"
}
```

So the request should include:

```http
Content-Type: application/json
```

### Beginner relevance

If the body is JSON but the request does not say it is JSON, the server may not read the data correctly.

This is why `Content-Type` is important when sending a request body.

### Debugging question

If a POST request fails even though the JSON body looks correct, ask:

> “Did I send `Content-Type: application/json`?”

---

## 8. `Accept`

`Accept` tells the server what response format the client prefers.

Beginner translation:

> “Please send the response in this format if possible.”

Example:

```http
Accept: application/json
```

means:

> “Please send me JSON.”

---

## 9. `Accept` Example: Asking for JSON Instead of HTML

### Situation

A beginner calls an API endpoint and expects JSON.

### Request

```http
GET /profile HTTP/1.1
Accept: application/json
```

### Expected Response

```http
HTTP/1.1 200 OK
Content-Type: application/json
```

Body:

```json
{
  "id": 10,
  "name": "Alya"
}
```

### What to notice

The request says:

```http
Accept: application/json
```

The response says:

```http
Content-Type: application/json
```

### Beginner relevance

This example matters because many beginners only look at the body.

But the headers explain the expectation and result:

- `Accept` = what the client wants
- `Content-Type` = what the server actually sends

### Debugging question

If you expected JSON but got an HTML page, check:

```http
Accept: application/json
Content-Type: text/html
```

This may mean you hit the wrong URL, got redirected, or received an HTML error page.

---

## 10. `Content-Type` vs `Accept`

This is one of the most important beginner distinctions.

| Header | Beginner Meaning | Direction |
| --- | --- | --- |
| `Content-Type` | “This is the format of the data I am sending or receiving.” | Request or response |
| `Accept` | “This is the format I want to receive.” | Usually request |

### Simple memory rule

- `Content-Type` = **what this message contains**
- `Accept` = **what I want back**

### Combined Example

```http
POST /login HTTP/1.1
Content-Type: application/json
Accept: application/json
```

Body:

```json
{
  "email": "alya@example.com",
  "password": "secret123"
}
```

### Beginner translation

This request says:

> “I am sending JSON, and I want JSON back.”

---

## 11. `Authorization`

`Authorization` is used to send access credentials to an API.

A common format is:

```http
Authorization: Bearer <token>
```

Beginner translation:

> “Here is my access token. Please check whether I am allowed to access this.”

---

## 12. `Authorization` Example: Opening a Profile Page

### Situation

A beginner logs in to an app.

After login, the app tries to open the user profile page.

Profile data is private, so the API needs proof that the user is allowed to access it.

### Request

```http
GET /profile HTTP/1.1
Accept: application/json
Authorization: Bearer fake-token-123
```

### What to notice

| Part | Meaning |
| --- | --- |
| `GET /profile` | The client wants to read profile data. |
| `Accept: application/json` | The client wants JSON response. |
| `Authorization: Bearer fake-token-123` | The client sends an access token. |

### Beginner relevance

Without the `Authorization` header, the server may not know who the user is.

If the endpoint is protected, the server may respond with:

```http
HTTP/1.1 401 Unauthorized
```

### Common beginner mistakes

Wrong:

```http
Authorization: fake-token-123
```

Why wrong:

The server may expect the `Bearer` format.

Better:

```http
Authorization: Bearer fake-token-123
```

Wrong:

```http
Bearer fake-token-123
```

Why wrong:

This is only the value. The header name is missing.

Better:

```http
Authorization: Bearer fake-token-123
```

### Debugging question

If a protected API returns `401 Unauthorized`, ask:

> “Did my request actually include `Authorization: Bearer <token>`?”

---

## 13. `User-Agent`

`User-Agent` tells the server what client is making the request.

Beginner translation:

> “This request came from this browser, tool, or client.”

Examples:

```http
User-Agent: Mozilla/5.0
User-Agent: PostmanRuntime/7.39.0
User-Agent: curl/8.0
```

---

## 14. `User-Agent` Example: Browser vs cURL

### Situation

A beginner opens the same API URL in the browser and also tests it with cURL.

The response looks slightly different.

### Browser-like request

```http
GET /public-posts HTTP/1.1
User-Agent: Mozilla/5.0
Accept: text/html,application/xhtml+xml,application/xml
```

### cURL request

```http
GET /public-posts HTTP/1.1
User-Agent: curl/8.0
Accept: */*
```

### What to notice

The URL can be the same, but the headers may be different.

### Beginner relevance

This matters because beginners often say:

> “But I opened the same URL.”

In API debugging, same URL does not always mean identical request.

The headers can be different depending on whether you use:

- browser
- Postman
- Insomnia
- cURL
- frontend app

### Debugging question

If a request works in Postman but not in browser, ask:

> “Are the request headers actually the same?”

---

## 15. `Cache-Control`

`Cache-Control` gives caching instructions.

Beginner translation:

> “Can this response be reused, or should the client check again?”

Examples:

```http
Cache-Control: no-cache
```

Beginner meaning:

> “Do not blindly reuse old data. Check with the server first.”

```http
Cache-Control: max-age=3600
```

Beginner meaning:

> “This response may be considered fresh for 3600 seconds.”

---

## 16. `Cache-Control` Example: Product Price Looks Old

### Situation

A beginner opens a product page.

The product price should have changed, but the old price still appears.

### Response

```http
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: max-age=3600
```

Body:

```json
{
  "id": 1,
  "name": "Keyboard",
  "price": 100000
}
```

### What to notice

```http
Cache-Control: max-age=3600
```

This means the response may be treated as fresh for a period of time.

### Beginner relevance

The API may not be “broken.”

The client, browser, or intermediate layer may be reusing a cached response.

At beginner level, you do not need to master caching yet.

You only need this habit:

> If data looks old, inspect cache-related headers.

Useful headers to notice:

```http
Cache-Control
ETag
Last-Modified
```

---

## 17. `Cookie`

`Cookie` sends stored cookie data from the client to the server.

Beginner translation:

> “Here is a stored value from the browser, often used to remember a session.”

Example:

```http
Cookie: session_id=abc123
```

---

## 18. `Set-Cookie`

`Set-Cookie` is sent by the server to ask the client to store a cookie.

Beginner translation:

> “Client, please save this cookie for future requests.”

Example:

```http
Set-Cookie: session_id=abc123; Path=/
```

---

## 19. Cookie Example: Login Session in a Browser

### Situation

A beginner logs in through a website.

The browser needs a way to remember that the user has logged in.

### Step 1: Login Response

```http
HTTP/1.1 200 OK
Set-Cookie: session_id=abc123; Path=/
Content-Type: application/json
```

Body:

```json
{
  "message": "Login successful"
}
```

### Step 2: Next Request

```http
GET /dashboard HTTP/1.1
Cookie: session_id=abc123
```

### What to notice

| Header | Direction | Meaning |
| --- | --- | --- |
| `Set-Cookie` | Server → Client | Server asks browser to store a cookie. |
| `Cookie` | Client → Server | Browser sends stored cookie back later. |

### Beginner relevance

This matters because login behavior may depend on headers.

If login works but the next page still acts like you are logged out, check whether:

- the response included `Set-Cookie`
- the next request included `Cookie`

You do not need to understand advanced cookie security yet.

For now, understand the usage flow:

> Server sets cookie. Browser stores cookie. Browser sends cookie later.

---

## 20. `Referer`

`Referer` can show the page that triggered a request.

Beginner translation:

> “This request came from this previous page.”

Example:

```http
Referer: https://example.com/dashboard
```

Small detail: the header name is spelled `Referer`, not `Referrer`.

---

## 21. `Referer` Example: Image Loaded from a Page

### Situation

A beginner opens a dashboard page.

The dashboard loads an image or API request in the background.

### Request

```http
GET /images/banner.png HTTP/1.1
Referer: https://example.com/dashboard
```

### What to notice

```http
Referer: https://example.com/dashboard
```

This tells you the request was triggered from the dashboard page.

### Beginner relevance

When many requests appear in the Network tab, beginners often feel lost.

`Referer` can help answer:

> “Which page caused this request?”

This is useful when inspecting browser Network activity.

---

# Quick Header Cheat Sheet

| Header | Beginner Question It Answers | Example |
| --- | --- | --- |
| `Content-Type` | What format is this body? | `Content-Type: application/json` |
| `Accept` | What format does the client want back? | `Accept: application/json` |
| `Authorization` | Is the client sending access credentials? | `Authorization: Bearer <token>` |
| `User-Agent` | What tool or browser made this request? | `User-Agent: curl/8.0` |
| `Cache-Control` | Can this response be reused from cache? | `Cache-Control: max-age=3600` |
| `Cookie` | Is the client sending stored cookie data? | `Cookie: session_id=abc123` |
| `Set-Cookie` | Is the server asking the client to save a cookie? | `Set-Cookie: session_id=abc123` |
| `Referer` | Which page triggered this request? | `Referer: https://example.com/dashboard` |

---

# How to Inspect Headers

---

## 22. Inspect Headers with cURL

### Situation

You want to see both the response headers and the response body from a public API.

### Command

```bash
curl -i https://jsonplaceholder.typicode.com/posts/1
```

### What `-i` means

`-i` means:

> “Show me the response headers too, not only the body.”

### What to look for

You may see headers like:

```http
HTTP/2 200
content-type: application/json; charset=utf-8
cache-control: max-age=43200
```

Then you may see the body:

```json
{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

### Beginner relevance

This is your first practical skill:

> Learn to separate headers from body.

Headers usually appear first.

The body appears after the headers.

---

## 23. Send Headers with cURL

### Situation

You want to manually tell the server that you prefer JSON.

### Command

```bash
curl -i \
  -H "Accept: application/json" \
  https://jsonplaceholder.typicode.com/posts/1
```

### What `-H` means

`-H` means:

> “Add this header to my request.”

### Beginner relevance

This is the same idea used by Postman, Insomnia, browsers, and frontend apps.

They all send headers. cURL just makes it visible.

---

## 24. Send JSON with cURL

### Situation

You want to send JSON data to an API.

### Command

```bash
curl -i \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title":"Hello","body":"World","userId":1}' \
  https://jsonplaceholder.typicode.com/posts
```

### What to notice

| Part | Meaning |
| --- | --- |
| `-X POST` | Use POST method. |
| `-H "Content-Type: application/json"` | The request body is JSON. |
| `-H "Accept: application/json"` | The client wants JSON response. |
| `-d '{...}'` | The data being sent in the body. |

### Beginner relevance

This example connects three lesson concepts:

- method: `POST`
- header: `Content-Type`
- body: JSON data

This helps beginners understand that headers do not replace the body.

Headers explain the body.

---

## 25. Read Headers in Browser DevTools

### Situation

You are using a website, and something does not work.

Maybe login fails. Maybe product data does not load. Maybe the API returns HTML instead of JSON.

### Steps

1. Open the website or web app.
2. Open Developer Tools.
3. Go to the **Network** tab.
4. Reload the page.
5. Click one request.
6. Look for **Request Headers** and **Response Headers**.

### What to look for as a beginner

| Problem | Header to Check |
| --- | --- |
| Login or profile request fails | `Authorization`, `Cookie`, `Set-Cookie` |
| JSON body not accepted | `Content-Type` |
| Expected JSON but got HTML | `Accept`, response `Content-Type` |
| Data looks old | `Cache-Control`, `ETag`, `Last-Modified` |
| Browser and Postman behave differently | `User-Agent`, `Accept`, `Authorization`, `Cookie` |

---

# Greybox Debugging Scenarios

---

## 26. Scenario: `401 Unauthorized`

### Situation

You try to access a private profile endpoint.

### Response

```http
HTTP/1.1 401 Unauthorized
```

### Header to check first

```http
Authorization: Bearer <token>
```

### Beginner checklist

Ask:

- Is the `Authorization` header present?
- Is the value empty?
- Is the `Bearer` word included?
- Is there an extra space?
- Is the token expired or copied incorrectly?

### Beginner meaning

`401 Unauthorized` often means:

> “The server did not accept your identity proof.”

Do not check the body first.

Check the access header first.

---

## 27. Scenario: `415 Unsupported Media Type`

### Situation

You send JSON to an API, but the API rejects it.

### Response

```http
HTTP/1.1 415 Unsupported Media Type
```

### Header to check first

```http
Content-Type: application/json
```

### Beginner checklist

Ask:

- Did I send JSON?
- Did I include `Content-Type: application/json`?
- Did my API tool send the body as raw JSON?
- Did I accidentally send form-data instead of JSON?

### Beginner meaning

`415 Unsupported Media Type` often means:

> “The server does not understand the format you sent.”

---

## 28. Scenario: Expected JSON, Got HTML

### Situation

You call an endpoint expecting JSON.

Instead, the response body looks like a web page.

### Headers to compare

```http
Accept: application/json
Content-Type: text/html
```

### Beginner checklist

Ask:

- Did I request JSON using `Accept: application/json`?
- Did the server send HTML instead?
- Did I hit a web page route instead of an API route?
- Did the request redirect to a login page?

### Beginner meaning

This problem often means:

> “You expected API data, but the server returned a page.”

The response `Content-Type` gives the clue.

---

## 29. Scenario: Data Looks Old

### Situation

You call an API, but the data looks outdated.

### Headers to check

```http
Cache-Control
ETag
Last-Modified
```

### Beginner checklist

Ask:

- Is the response cached?
- Does `Cache-Control` allow reuse?
- Am I seeing a browser-cached response?
- Does refreshing or disabling cache change the result?

### Beginner meaning

Old-looking data is not always a database problem.

Sometimes headers explain the behavior.

---

# Practice

---

## 30. Practice 1: Inspect a Real Response

Run:

```bash
curl -i https://jsonplaceholder.typicode.com/posts/1
```

Answer:

1. Where do the headers start?
2. Where does the body start?
3. What is the response `Content-Type`?
4. Is there a cache-related header?
5. Does the response body match the `Content-Type`?

### Beginner goal

Do not try to memorize all headers.

Just practice seeing the difference between:

- status line
- response headers
- response body

---

## 31. Practice 2: Send Your Own Header

Run:

```bash
curl -i \
  -H "Accept: application/json" \
  https://jsonplaceholder.typicode.com/posts/1
```

Answer:

1. What header did you manually send?
2. What response format did you ask for?
3. What response `Content-Type` did the server return?

### Beginner goal

Understand that headers are not only something you read.

You can also send them.

---

## 32. Practice 3: Connect Header and Body

Run:

```bash
curl -i \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title":"Hello","body":"World","userId":1}' \
  https://jsonplaceholder.typicode.com/posts
```

Answer:

1. Which header explains the body format?
2. Which header explains the response format you want?
3. Which part is the actual body data?

### Beginner goal

Understand this separation:

| Part | Role |
| --- | --- |
| `POST` | The action |
| URL | The target |
| Headers | The context |
| Body | The data |

---

# Mini Quiz

1. Which header tells the server that your request body is JSON?
2. Which header tells the server what response format you prefer?
3. Which header commonly carries a Bearer token?
4. If you get `401 Unauthorized`, which header should you check first?
5. If you get `415 Unsupported Media Type`, which header should you check first?
6. If you expected JSON but got HTML, which response header should you inspect?
7. If login works but the next page still acts logged out, which headers may be relevant?

## Answers

1. `Content-Type: application/json`
2. `Accept: application/json`
3. `Authorization`
4. `Authorization`
5. `Content-Type`
6. Response `Content-Type`
7. `Set-Cookie` and `Cookie`

---

# Final Summary

Headers are the **context layer** of HTTP communication.

They help you understand:

- what format is being sent
- what format is expected
- whether access credentials are included
- whether cookies or sessions are involved
- whether the response may be cached
- what tool or client made the request
- which page triggered a request

For beginner greybox API debugging, the habit is simple:

> Before blaming the API, inspect the headers.

A strong beginner does not need to memorize every header.

A strong beginner needs to know which header to inspect when something fails.
