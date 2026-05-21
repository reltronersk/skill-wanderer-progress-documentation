# HTTP Methods Deep Dive

HTTP Methods are one of the most fundamental building blocks in HTTP communication.

They define what kind of action a client wants to perform against a resource on a server.

HTTP methods help clients and servers communicate clearly and consistently.

Without HTTP methods:

- Requests become ambiguous
- APIs become inconsistent
- Clients cannot predict behavior correctly
- Data operations become confusing

HTTP methods standardize how data is:

- Retrieved
- Created
- Updated
- Deleted
- Validated

---

# Why HTTP Methods Exist

Before HTTP methods existed, systems had no standardized communication behavior.

A client could send data to a server, but the server would not clearly understand the intention.

For example:

- Is the client retrieving data?
- Creating new data?
- Updating existing data?
- Deleting data?
- Checking metadata only?

HTTP methods solve this ambiguity.

They establish predictable communication behavior between:

- Browsers
- Mobile applications
- APIs
- Backend servers
- Clients

---

# The Core Philosophy

HTTP methods are built around one core idea:

> The action should be explicit and predictable.

This predictability helps clients and servers communicate consistently and safely.

---

# Overview of Main HTTP Methods

| Method | Purpose | Safe | Idempotent |
|---|---|---|---|
| GET | Retrieve data | Yes | Yes |
| POST | Create new resource | No | No |
| PUT | Replace entire resource | No | Yes |
| PATCH | Partially update resource | No | Usually Yes |
| DELETE | Remove resource | No | Yes |
| HEAD | Retrieve headers only | Yes | Yes |
| OPTIONS | Discover allowed operations | Yes | Yes |

---

# Method Selection Basics

| Scenario | Recommended Method |
|---|---|
| Retrieve data | GET |
| Create new resource | POST |
| Replace full resource | PUT |
| Partially update resource | PATCH |
| Remove resource | DELETE |
| Retrieve headers only | HEAD |
| Discover allowed methods | OPTIONS |

---

# DELETE Method

# What is DELETE

DELETE is used to remove resources from a server.

Example:

```http
DELETE /posts/15
````

The targeted resource is removed or marked for removal.

---

# Why DELETE Exists

Applications need a standardized way to remove resources.

Without DELETE:

* Unused data accumulates continuously
* Systems become harder to maintain
* Storage usage increases unnecessarily

DELETE provides predictable resource lifecycle management.

---

# Characteristics of DELETE

DELETE is:

* State-changing
* Idempotent

Calling the same DELETE request multiple times should produce the same final result.

Example:

```http id="r3k8vm"
DELETE /users/10
```

After the resource is deleted, repeating the request should not create additional side effects.

---

# DELETE Request Example

Request:

```http id="u5q2nx"
DELETE /comments/88
```

Response:

```http id="g7m1yp"
204 No Content
```

This request removes the targeted comment resource.

---

# Browser Behavior with DELETE

DELETE requests:

* Usually are not cached
* Usually are not triggered directly by standard HTML forms
* Commonly use JavaScript or API clients
* May be repeated safely because DELETE is idempotent

Because DELETE is intended for destructive operations.

---

# Common DELETE Status Codes

| Status Code | Meaning                                   |
| ----------- | ----------------------------------------- |
| 200         | Successful deletion                       |
| 202         | Deletion accepted                         |
| 204         | Successful deletion with no response body |
| 400         | Bad request                               |
| 401         | Unauthorized                              |
| 403         | Forbidden                                 |
| 404         | Resource not found                        |

---

# Soft Delete vs Hard Delete

## Soft Delete

Soft delete marks data as deleted without permanently removing it.

Example:

```json id="x4p9tw"
{
  "deleted": true
}
```

Advantages:

* Recoverable
* Safer
* Easier auditing

Disadvantages:

* Data still occupies storage
* Queries become more complex

---

## Hard Delete

Hard delete permanently removes the resource.

Advantages:

* Frees storage space
* Cleaner database state

Disadvantages:

* Irreversible
* Riskier if performed accidentally

---

# Common DELETE Challenges

## Referential Integrity

Deleting one resource may affect related resources.

Example:

* Deleting a user account
* Existing orders still reference that user

Consequences:

* Broken relationships
* Orphaned records
* Inconsistent data

---

## Cascading Deletion

Deleting parent resources may require deleting child resources.

Example:

* Deleting a project
* Associated tasks may also need removal

Consequences:

* Unexpected data removal
* Large deletion chains
* Accidental data loss

---

## Accidental Deletion

DELETE operations are destructive.

Example:

```http id="n2m7vq"
DELETE /products/1
```

Consequences:

* Permanent data loss
* Broken workflows
* Operational mistakes

---

# Best Practices for DELETE

* Validate authorization carefully
* Confirm destructive actions
* Use soft delete when recovery is important
* Return meaningful status codes
* Document deletion behavior clearly
* Protect sensitive DELETE endpoints

---

# DELETE Method Comprehensive Matrix

| Category               | Explanation                                        | Example                         |
| ---------------------- | -------------------------------------------------- | ------------------------------- |
| Primary Purpose        | Remove resources from a server                     | `DELETE /posts/15`              |
| State Changes          | DELETE changes server state                        | Removing user accounts          |
| Idempotency            | Repeating DELETE produces same final state         | Retrying deletions              |
| Resource Removal       | DELETE removes target resources                    | Deleting comments               |
| Destructive Operation  | DELETE performs irreversible actions               | Removing products               |
| Browser Behavior       | Usually triggered through APIs or JavaScript       | Dashboard delete actions        |
| Authorization          | DELETE often requires strong permissions           | Admin-only deletion             |
| Status Codes           | Common DELETE responses                            | 200, 204, 404                   |
| Request Semantics      | DELETE represents resource removal                 | Removing notifications          |
| Soft Delete            | Resources marked deleted instead of removed        | Recoverable records             |
| Hard Delete            | Resources permanently removed                      | Permanent cleanup               |
| Referential Integrity  | Related resources may be affected                  | Deleting parent entities        |
| Cascading Deletion     | Child resources may also be deleted                | Project-task removal            |
| Data Consistency       | Deletion must preserve valid relationships         | Removing linked records safely  |
| Retry Safety           | DELETE can usually be retried safely               | Network retry handling          |
| Form Usage             | Standard HTML forms do not support DELETE directly | JavaScript API requests         |
| Common Mistake         | Deleting without validation                        | Unsafe admin endpoints          |
| Resource Lifecycle     | DELETE manages resource cleanup                    | Removing inactive resources     |
| Security Consideration | DELETE endpoints require protection                | Preventing unauthorized removal |
| Response Behavior      | Server confirms deletion result                    | 204 No Content                  |

---

# Common DELETE Mistakes

## Using GET for Deletion

Incorrect:

```http id="m9x4qr"
GET /delete-user/10
```

Why this is dangerous:

* GET should not modify state
* Browsers may accidentally trigger requests
* Crawlers may trigger deletions unintentionally

Correct:

```http id="f6w2lt"
DELETE /users/10
```

---

## Missing Authorization Checks

Incorrect behavior:

Any authenticated user can delete resources.

Consequences:

* Unauthorized deletion
* Data loss
* Security risks

Correct approach:

* Validate ownership
* Validate permissions
* Restrict sensitive deletion operations

---

# HTML Form Limitation

Standard HTML forms support only:

* GET
* POST

DELETE requests are commonly sent using:

* JavaScript fetch APIs
* AJAX requests
* API clients

Example:

```javascript id="k5v1np"
fetch('/posts/15', {
  method: 'DELETE'
})
```

---

# HEAD Method

# What is HEAD

HEAD retrieves response headers without returning the response body.

Example:

```http
HEAD /video.mp4
```

The server returns metadata only.

---

# Why HEAD Exists

Sometimes clients need information about a resource without downloading the full content.

Examples:

* File size
* Content type
* Last modified date
* Cache validation

HEAD avoids unnecessary data transfer.

---

# Characteristics of HEAD

HEAD is:

* Safe
* Idempotent
* Read-only

HEAD behaves similarly to GET except the response body is omitted.

---

# HEAD Request Example

Request:

```http id="t7p4mw"
HEAD /files/report.pdf
```

Response:

```http id="j8n2yv"
HTTP/1.1 200 OK
Content-Type: application/pdf
Content-Length: 204800
Last-Modified: Tue, 20 May 2026 10:00:00 GMT
```

The server returns metadata without sending the file itself.

---

# Browser Behavior with HEAD

HEAD requests:

* Usually do not download response bodies
* Are commonly used for metadata inspection
* May be cached similarly to GET
* Are commonly used by tools and APIs

Because HEAD is intended for lightweight resource inspection.

---

# Common HEAD Status Codes

| Status Code | Meaning                                  |
| ----------- | ---------------------------------------- |
| 200         | Resource metadata retrieved successfully |
| 304         | Resource not modified                    |
| 400         | Bad request                              |
| 401         | Unauthorized                             |
| 403         | Forbidden                                |
| 404         | Resource not found                       |

---

# Common HEAD Use Cases

HEAD is commonly used for:

* File existence checking
* Cache validation
* Metadata inspection
* Download size checking
* Content type validation

---

# Common HEAD Challenges

## Incorrect Server Implementation

Some servers incorrectly return response bodies for HEAD requests.

Consequences:

* Unexpected bandwidth usage
* Incorrect client behavior
* Protocol inconsistency

---

## Missing Metadata

Some servers fail to provide useful headers.

Example:

* Missing `Content-Length`
* Missing `Last-Modified`

Consequences:

* Poor cache validation
* Weak client optimization
* Reduced metadata usefulness

---

# Best Practices for HEAD

* Return accurate headers
* Avoid sending response bodies
* Keep HEAD behavior consistent with GET
* Provide useful metadata
* Support cache validation headers

---

# HEAD Method Comprehensive Matrix

| Category              | Explanation                                    | Example                      |
| --------------------- | ---------------------------------------------- | ---------------------------- |
| Primary Purpose       | Retrieve headers without response body         | `HEAD /video.mp4`            |
| Metadata Retrieval    | HEAD retrieves resource metadata               | File information lookup      |
| Safe Operation        | HEAD should not modify server state            | Cache validation             |
| Idempotency           | Repeated HEAD requests behave consistently     | Metadata checks              |
| Lightweight Requests  | HEAD avoids downloading large content          | Checking file size           |
| Browser Usage         | Browsers and tools inspect metadata            | Download managers            |
| Cache Validation      | HEAD supports freshness checking               | Last-Modified validation     |
| Response Headers      | HEAD returns metadata only                     | Content-Length inspection    |
| Request Semantics     | HEAD represents metadata inspection            | File existence checks        |
| Status Codes          | Common HEAD responses                          | 200, 304, 404                |
| Request Body Usage    | HEAD usually does not use request bodies       | Lightweight requests         |
| File Validation       | HEAD verifies downloadable content             | Checking PDFs                |
| Content Inspection    | HEAD validates content type                    | MIME type verification       |
| Resource Availability | HEAD checks whether resources exist            | Broken link detection        |
| Response Efficiency   | HEAD minimizes bandwidth usage                 | Metadata-only requests       |
| Common Mistake        | Returning response body accidentally           | Incorrect server handling    |
| Retry Safety          | HEAD requests can usually be retried safely    | Network retry handling       |
| Client Optimization   | HEAD helps clients avoid unnecessary downloads | Pre-download checks          |
| Monitoring Usage      | HEAD commonly supports lightweight validation  | Health verification          |
| Response Consistency  | HEAD headers should match GET metadata         | Consistent resource metadata |

---

# Common HEAD Mistakes

## Using GET Instead of HEAD for Metadata

Incorrect:

```http id="c2q7zn"
GET /large-video.mp4
```

when only metadata is needed.

Consequences:

* Unnecessary bandwidth usage
* Slower responses
* Larger downloads

Correct:

```http id="y5m8tr"
HEAD /large-video.mp4
```

---

## Returning Response Bodies for HEAD

Incorrect behavior:

Server sends full response body for HEAD requests.

Consequences:

* Protocol inconsistency
* Broken client expectations
* Reduced efficiency

Correct behavior:

* Return headers only
* Omit response body entirely

---

# OPTIONS Method

# What is OPTIONS

OPTIONS discovers supported operations for a resource.

Example:

```http
OPTIONS /users
```

Response:

```http id="h3p6xv"
Allow: GET, POST, PUT, DELETE
```

The server informs clients which methods are allowed.

---

# Why OPTIONS Exists

Clients sometimes need to discover server capabilities before sending requests.

OPTIONS helps clients understand:

* Allowed HTTP methods
* Supported headers
* Cross-origin permissions

OPTIONS is especially important for browser communication and APIs.

---

# Characteristics of OPTIONS

OPTIONS is:

* Safe
* Idempotent
* Read-only

OPTIONS does not modify server state.

---

# OPTIONS and CORS

Modern browsers commonly use OPTIONS for CORS preflight requests.

Example workflow:

1. Browser wants to send a cross-origin request
2. Browser sends OPTIONS request first
3. Server validates permissions
4. Browser proceeds if allowed

Example validation:

* Allowed origins
* Allowed methods
* Allowed headers

---

# OPTIONS Request Example

Request:

```http id="v4k9nw"
OPTIONS /api/orders
Origin: https://frontend.example.com
```

Response:

```http id="u1m7qp"
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://frontend.example.com
Access-Control-Allow-Methods: GET, POST, PATCH
Access-Control-Allow-Headers: Content-Type, Authorization
```

The browser checks whether cross-origin communication is allowed.

---

# Browser Behavior with OPTIONS

OPTIONS requests:

* Commonly happen automatically in browsers
* Usually occur before sensitive cross-origin requests
* Usually do not contain response bodies
* Are heavily associated with CORS behavior

Because browsers validate communication permissions before sending certain requests.

---

# Common OPTIONS Status Codes

| Status Code | Meaning                          |
| ----------- | -------------------------------- |
| 200         | Successful capability response   |
| 204         | Successful response with no body |
| 400         | Bad request                      |
| 401         | Unauthorized                     |
| 403         | Forbidden                        |
| 404         | Resource not found               |

---

# Common OPTIONS Use Cases

OPTIONS is commonly used for:

* CORS preflight requests
* API capability discovery
* Allowed method inspection
* Header validation
* Browser permission negotiation

---

# Common OPTIONS Challenges

## CORS Misconfiguration

Incorrect server configuration may block frontend requests.

Consequences:

* Browser request failures
* Cross-origin access errors
* Frontend communication problems

---

## Missing Allowed Headers

Example:

```http id="z6n2xt"
Access-Control-Allow-Headers: Content-Type
```

but missing:

```http id="m8r4kv"
Authorization
```

Consequences:

* Authentication headers rejected
* Browser blocks requests
* API becomes inaccessible

---

# Best Practices for OPTIONS

* Configure CORS carefully
* Return accurate allowed methods
* Return required allowed headers
* Keep OPTIONS responses lightweight
* Validate trusted origins explicitly

---

# OPTIONS Method Comprehensive Matrix

| Category                   | Explanation                                    | Example                    |
| -------------------------- | ---------------------------------------------- | -------------------------- |
| Primary Purpose            | Discover supported communication operations    | `OPTIONS /users`           |
| Capability Discovery       | OPTIONS reveals allowed methods                | Method inspection          |
| Safe Operation             | OPTIONS should not modify state                | Browser validation         |
| Idempotency                | Repeated OPTIONS requests behave consistently  | Preflight retries          |
| CORS Support               | OPTIONS commonly handles preflight requests    | Cross-origin APIs          |
| Allowed Methods            | Server declares supported methods              | GET, POST, PATCH           |
| Allowed Headers            | Server validates accepted headers              | Authorization headers      |
| Browser Usage              | Browsers send OPTIONS automatically            | CORS negotiation           |
| Request Semantics          | OPTIONS represents capability inspection       | API discovery              |
| Status Codes               | Common OPTIONS responses                       | 200, 204, 403              |
| Cross-Origin Communication | OPTIONS validates frontend permissions         | API access control         |
| Header Validation          | OPTIONS checks allowed request headers         | Content-Type validation    |
| Origin Validation          | OPTIONS validates trusted origins              | Frontend authorization     |
| API Discovery              | OPTIONS helps clients understand APIs          | REST exploration           |
| Response Efficiency        | OPTIONS responses are usually lightweight      | Metadata-only negotiation  |
| Common Mistake             | Missing CORS configuration                     | Blocked browser requests   |
| Retry Safety               | OPTIONS requests can usually be retried safely | Browser retries            |
| Security Consideration     | OPTIONS influences browser security behavior   | Controlled API access      |
| Client Compatibility       | OPTIONS supports browser interoperability      | Modern frontend frameworks |
| Response Behavior          | Server declares communication rules            | Capability negotiation     |

---

# Common OPTIONS Mistakes

## Missing CORS Headers

Incorrect behavior:

Server forgets to return:

```http id="x5t9mw"
Access-Control-Allow-Origin
```

Consequences:

* Browser blocks frontend requests
* Cross-origin APIs fail
* Frontend applications break

---

## Returning Incorrect Allowed Methods

Incorrect:

```http id="n4v7yr"
Allow: GET
```

when the API also supports POST.

Consequences:

* Browser confusion
* API capability mismatch
* Client-side request failures

---

# Final Conclusion

HTTP methods define how clients and servers communicate.

Each method has a specific purpose and expected behavior.

Understanding HTTP methods correctly helps developers:

* Build predictable APIs
* Prevent incorrect request behavior
* Improve client-server communication
* Use HTTP consistently

HTTP methods are not merely syntax.

They are communication rules that define how applications interact across the web.

