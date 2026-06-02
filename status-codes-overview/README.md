# HTTP Status Codes Overview

HTTP status codes are short messages sent by the server to tell the client what happened after a request.

Whenever a browser, mobile app, or API sends a request, the server responds with a status code.

Think of status codes as the server's quick answer:

* "Everything worked."
* "You need to log in."
* "I can't find that resource."
* "Something went wrong on my side."

---

# Why Status Codes Matter

Without status codes, applications would have no standard way to understand whether a request succeeded or failed.

Status codes allow browsers, mobile apps, APIs, and servers to communicate using the same language.

They help developers quickly understand:

* Was the request successful?
* Is authentication required?
* Does the resource exist?
* Is the server experiencing problems?

---

# The Five Status Code Categories

Every HTTP status code belongs to one of five groups.

| Range | Category      | Meaning                    |
| ----- | ------------- | -------------------------- |
| 1xx   | Informational | Processing continues       |
| 2xx   | Success       | Request succeeded          |
| 3xx   | Redirection   | Additional action required |
| 4xx   | Client Error  | Problem with the request   |
| 5xx   | Server Error  | Problem on the server      |

The first digit immediately tells you the category.

Examples:

* 200 → Success
* 404 → Client Error
* 500 → Server Error

---

# Common Status Codes (Know These First)

These are the status codes developers encounter most often.

---

## 200 OK

Everything worked successfully.

Common situations:

* Loading user data
* Fetching products
* Viewing a page
* Running a search

---

## 201 Created

A new resource was successfully created.

Common situations:

* Creating a user account
* Creating a blog post
* Creating an order

---

## 204 No Content

The request succeeded, but there is nothing to return.

Common situations:

* Deleting a record
* Completing an action without returning data

---

## 400 Bad Request

The request is invalid.

Common situations:

* Missing required fields
* Invalid input format
* Invalid JSON data

---

## 401 Unauthorized

Authentication is required.

Common situations:

* Missing access token
* Expired login session
* Invalid token

Think:

> "Who are you?"

---

## 403 Forbidden

The user is authenticated but does not have permission.

Common situations:

* Admin-only pages
* Restricted resources
* Access control rules

Think:

> "I know who you are, but you can't do this."

---

## 404 Not Found

The requested resource does not exist.

Common situations:

* Wrong URL
* Deleted record
* Invalid ID

This is one of the most common status codes on the web.

---

## 409 Conflict

The request conflicts with existing data.

Common situations:

* Duplicate email
* Duplicate username
* Duplicate unique value

---

## 422 Unprocessable Entity

The request format is valid, but the data fails validation.

Common situations:

* Invalid email format
* Password too short
* Business rule validation failures

---

## 429 Too Many Requests

The client is sending too many requests.

Common situations:

* API rate limits
* Spam protection
* Excessive automated requests

---

## 500 Internal Server Error

The server encountered an unexpected problem.

Common situations:

* Programming errors
* Crashed services
* Database failures

---

## 503 Service Unavailable

The service is temporarily unavailable.

Common situations:

* Maintenance
* Server overload
* Temporary downtime

---

# Rare Status Codes (Good to Know)

These status codes exist but are encountered less frequently by most developers.

---

## 100 Continue

The server is ready to receive the rest of the request.

Usually seen during:

* Large uploads
* Streaming operations

---

## 101 Switching Protocols

The server agrees to switch communication protocols.

Usually seen during:

* WebSocket connections

---

## 202 Accepted

The request was accepted and will be processed later.

Usually seen during:

* Background jobs
* Queue processing
* Long-running tasks

---

## 301 Moved Permanently

The resource permanently moved to another location.

Usually seen during:

* Website migrations
* SEO redirects

---

## 302 Found

The resource temporarily moved elsewhere.

Usually seen during:

* Temporary redirects

---

## 304 Not Modified

The resource has not changed since the last request.

Usually seen during:

* Browser caching
* Performance optimization

---

## 405 Method Not Allowed

The endpoint exists, but the requested HTTP method is not supported.

Usually seen when:

* Using POST instead of GET
* Using DELETE on an endpoint that doesn't allow deletion

---

## 502 Bad Gateway

One server received an invalid response from another server.

Usually seen in:

* Reverse proxy setups
* Microservices architectures

---

## 504 Gateway Timeout

The server waited too long for another service to respond.

Usually seen when:

* Databases are slow
* External APIs are slow
* Backend services timeout

---

# Status Codes Every Beginner Should Memorize

If you're just starting web development, focus on these first:

* 200 OK
* 201 Created
* 400 Bad Request
* 401 Unauthorized
* 403 Forbidden
* 404 Not Found
* 422 Unprocessable Entity
* 429 Too Many Requests
* 500 Internal Server Error
* 503 Service Unavailable

These ten status codes cover the vast majority of situations you'll encounter when building APIs and web applications.

---

# Final Summary

HTTP status codes are the standard language servers use to explain what happened after a request.

For beginners, the most important thing is not memorizing every status code, but understanding what category the code belongs to and what action should be taken next.

When debugging APIs, always check the status code first before looking at anything else.
