## Headers Basics

HTTP headers are **small pieces of information** sent together with an HTTP request or response.

They are not the main data body. They are not the URL. They are not the HTTP method.

Headers give extra context about how the request or response should be understood.

---

### The Simple Mental Model

Imagine sending a package.

- The **URL** is the delivery address.
- The **HTTP method** is the action you want to perform.
- The **body** is the content inside the package.
- The **headers** are the labels attached to the package.

Those labels can say things like:

- “This package contains JSON.”
- “I want the response as JSON.”
- “Here is my access token.”
- “This request comes from a browser, cURL, or Postman.”
- “This response can or cannot be cached.”

---

### What Does a Header Look Like?

A header usually has a simple format:

```
Header-Name: Header Value
```

Example:

```
Content-Type: application/json
```

This means:

**The data being sent or received is JSON.**

---

### Headers Are Metadata

Headers are often called **metadata**.

Metadata means information about the message, not always the main message itself.

Example:

```
POST /login HTTP/1.1
Content-Type: application/json
Accept: application/json
```

Body:

```
{
  "email": "user@example.com",
  "password": "secret"
}
```

The body contains the login data.

The headers explain how that data should be handled.

---

### Request Headers vs Response Headers

There are two major places where you will see headers:

- **Request headers** - sent by the client to the server.
- **Response headers** - sent by the server back to the client.

#### Request Headers

Request headers travel from the client to the server.

The client can be:

- A browser
- Postman
- Insomnia
- cURL
- A mobile app
- A frontend application

Example request:

```
GET /products HTTP/1.1
Accept: application/json
Authorization: Bearer fake-token
User-Agent: curl/8.0
```

This tells the server:

- The client wants product data.
- The client prefers JSON as the response format.
- The client is sending an access token.
- The request is coming from cURL.

#### Response Headers

Response headers travel from the server back to the client.

Example response:

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-cache
```

This tells the client:

- The request was successful.
- The response body is JSON.
- The response has cache instructions.

---

### Important Header: Content-Type

`Content-Type` explains the format of the data being sent.

When you send JSON, you commonly use:

```
Content-Type: application/json
```

Example:

```
POST /users HTTP/1.1
Content-Type: application/json
```

Body:

```
{
  "name": "John Doe",
  "email": "john@example.com"
}
```

This tells the server:

**“Please read this request body as JSON.”**

#### Common Content-Type Values

| Content-Type | Meaning |
| --- | --- |
| `application/json` | The body contains JSON data. |
| `text/html` | The body contains HTML. |
| `text/plain` | The body contains plain text. |
| `multipart/form-data` | The body may contain form fields and files. |
| `application/x-www-form-urlencoded` | The body contains form data encoded like query parameters. |

#### Common Beginner Mistake

Sending JSON body without telling the server it is JSON.

Problem:

```
POST /users HTTP/1.1
```

Body:

```
{
  "name": "John Doe"
}
```

The server may not understand how to parse the body.

Better:

```
POST /users HTTP/1.1
Content-Type: application/json
```

---

### Important Header: Accept

`Accept` tells the server what response format the client prefers.

Example:

```
Accept: application/json
```

This means:

**“Please send the response as JSON if possible.”**

#### Content-Type vs Accept

Beginners often confuse `Content-Type` and `Accept`.

| Header | Simple Meaning |
| --- | --- |
| `Content-Type` | “This is the format of the data I am sending.” |
| `Accept` | “This is the format of the data I want to receive.” |

Example:

```
POST /users HTTP/1.1
Content-Type: application/json
Accept: application/json
```

This means:

- The request body is JSON.
- The client wants the response as JSON.

---

### Important Header: Authorization

`Authorization` is used to send access credentials to an API.

A common format is:

```
Authorization: Bearer <token>
```

Example:

```
GET /profile HTTP/1.1
Authorization: Bearer fake-token-123
```

This tells the server:

**“Here is my access token. Please check whether I am allowed to access this resource.”**

#### Common Authorization Mistakes

Wrong:

```
Authorization: fake-token-123
```

Better:

```
Authorization: Bearer fake-token-123
```

Wrong:

```
Bearer fake-token-123
```

Better:

```
Authorization: Bearer fake-token-123
```

#### Greybox Debugging Tip

If an API returns:

```
401 Unauthorized
```

Check the `Authorization` header first.

Ask:

- Is the header present?
- Is the token empty?
- Is the word `Bearer` included?
- Is there an extra space or typo?
- Is the token expired?

---

### Important Header: User-Agent

`User-Agent` tells the server what client is making the request.

Examples:

```
User-Agent: curl/8.0
User-Agent: PostmanRuntime/7.39.0
User-Agent: Mozilla/5.0
```

This can help you understand whether the request came from:

- A browser
- A command-line tool
- An API client
- A script

#### Greybox Debugging Tip

If an API behaves differently in browser, Postman, and cURL, compare the request headers.

One of the differences may be the `User-Agent`.

---

### Important Header: Cache-Control

`Cache-Control` gives caching instructions.

It can appear in request headers or response headers.

Example:

```
Cache-Control: no-cache
```

Simple meaning:

**“Do not blindly reuse old data. Revalidate it first.”**

Another example:

```
Cache-Control: max-age=3600
```

Simple meaning:

**“This response may be treated as fresh for 3600 seconds.”**

#### Greybox Debugging Tip

If an API response looks outdated, check cache-related headers.

Useful headers to notice:

- `Cache-Control`
- `ETag`
- `Last-Modified`

You do not need to master caching yet. For now, just know that headers can explain why old data appears.

---

### Important Header: Cookie

`Cookie` sends stored cookie data from the client to the server.

Example:

```
Cookie: session_id=abc123
```

Simple meaning:

**“This request belongs to a client that has this stored session value.”**

Browsers often send cookies automatically when a site has already stored them.

---

### Important Header: Set-Cookie

`Set-Cookie` is sent by the server to ask the client to store a cookie.

Example response:

```
HTTP/1.1 200 OK
Set-Cookie: session_id=abc123; Path=/
```

Simple meaning:

**“Client, please store this cookie and send it again in future requests when needed.”**

#### Login Example

A login flow may look like this:

1. The client sends username and password.
2. The server responds with `Set-Cookie`.
3. The browser stores the cookie.
4. The next request includes `Cookie`.

---

### Important Header: Referer

`Referer` can show where a request came from.

Example:

```
Referer: https://example.com/dashboard
```

Simple meaning:

**“This request was triggered from this previous page or location.”**

Small detail: the header name is spelled `Referer`, not `Referrer`.

---

### Quick Header Cheat Sheet

| Header | Used For | Beginner Meaning |
| --- | --- | --- |
| `Content-Type` | Request or response body format | “This data is JSON, HTML, text, form-data, etc.” |
| `Accept` | Preferred response format | “Please send me JSON if possible.” |
| `Authorization` | Access credential | “Here is my token or credential.” |
| `User-Agent` | Client identity | “This request comes from browser, cURL, Postman, etc.” |
| `Cache-Control` | Cache behavior | “Can this response be reused or should it be revalidated?” |
| `Cookie` | Client sends stored cookie | “Here is my stored session or state value.” |
| `Set-Cookie` | Server asks client to store cookie | “Please save this cookie for later requests.” |
| `Referer` | Previous page or origin context | “This request came from this page.” |

---

### How to See Headers with cURL

You can use `curl -i` to show response headers together with the response body.

Example:

```
curl -i https://jsonplaceholder.typicode.com/posts/1
```

You may see something like:

```
HTTP/2 200
content-type: application/json; charset=utf-8
cache-control: max-age=43200
```

After the headers, you will see the body:

```
{
  "userId": 1,
  "id": 1,
  "title": "...",
  "body": "..."
}
```

---

### How to Send Headers with cURL

Use `-H` to add a request header.

Example:

```
curl -i \
  -H "Accept: application/json" \
  https://jsonplaceholder.typicode.com/posts/1
```

Example with JSON body:

```
curl -i \
  -X POST \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"title":"Hello","body":"World","userId":1}' \
  https://jsonplaceholder.typicode.com/posts
```

Example with authorization:

```
curl -i \
  -H "Authorization: Bearer fake-token-123" \
  https://api.example.com/profile
```

---

### How to Read Headers in Browser DevTools

In a browser, you can inspect headers from the Network tab.

1. Open the website or web app.
2. Open Developer Tools.
3. Go to the **Network** tab.
4. Reload the page.
5. Click one request.
6. Look for **Request Headers** and **Response Headers**.

This helps you see what your browser actually sends and receives.

---

### Greybox Debugging Scenario 1: 401 Unauthorized

Problem:

```
HTTP/1.1 401 Unauthorized
```

First header to check:

```
Authorization: Bearer <token>
```

Possible causes:

- No `Authorization` header.
- Missing `Bearer` prefix.
- Invalid token.
- Expired token.
- Token copied with extra spaces.

---

### Greybox Debugging Scenario 2: 415 Unsupported Media Type

Problem:

```
HTTP/1.1 415 Unsupported Media Type
```

First header to check:

```
Content-Type: application/json
```

This error often means:

- The server does not understand the format you sent.
- You sent JSON but forgot `Content-Type: application/json`.
- You sent form-data when the endpoint expected JSON.
- Your API client selected the wrong body format.

---

### Greybox Debugging Scenario 3: Expected JSON, Got HTML

Problem:

You expected JSON, but the response body looks like an HTML page.

Headers to check:

```
Accept: application/json
Content-Type: text/html
```

Possible causes:

- You accessed the wrong URL.
- The server returned an HTML error page.
- The API route redirected to a login page.
- The server did not return JSON for this request.

---

### Greybox Debugging Scenario 4: Data Looks Old

Problem:

You called the API, but the data looks outdated.

Headers to check:

```
Cache-Control
ETag
Last-Modified
```

Possible causes:

- The response was cached.
- The browser reused stored data.
- An intermediate cache returned an older response.

At beginner level, the main lesson is simple:

**Headers can explain why the response behaves differently than expected.**

---

### Practice: Inspect a Real Response

Run this command:

```
curl -i https://jsonplaceholder.typicode.com/posts/1
```

Then answer:

- Where do the headers start?
- Where does the body start?
- What is the response `Content-Type`?
- Is there a cache-related header?
- Does the response body match the `Content-Type`?

---

### Practice: Send Your Own Header

Run this command:

```
curl -i \
  -H "Accept: application/json" \
  https://jsonplaceholder.typicode.com/posts/1
```

You are now manually sending a request header.

This is the same idea used by API tools like Postman and Insomnia.

---

### Mini Quiz

1. Which header tells the server that your request body is JSON?
2. Which header tells the server what response format you prefer?
3. Which header commonly carries a Bearer token?
4. If you get `401 Unauthorized`, which header should you check first?
5. If you get `415 Unsupported Media Type`, which header should you check first?

#### Answers

1. `Content-Type: application/json`
2. `Accept: application/json`
3. `Authorization`
4. `Authorization`
5. `Content-Type`

---

## Final Summary

Headers are the **context layer** of HTTP communication.

They help clients and servers understand:

- What format is being sent
- What format is expected
- Whether access credentials are included
- Whether cookies or sessions are involved
- Whether data may be cached
- What tool or client made the request

As a greybox API learner, your habit should be:

**Before blaming the API, inspect the headers.**
