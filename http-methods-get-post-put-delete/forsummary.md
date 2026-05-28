# In-Depth Reference Tables

## HTTP Methods Comparison Matrix

| Method  | Primary purpose        | Safe? | Common usage              | Has body?  | Typical codes | Cacheable? | Bookmarkable? | HTML form |
|---------|------------------------|-------|---------------------------|------------|---------------|------------|---------------|-----------|
| GET     | Retrieve data          | Yes   | Reading resources         | Usually no | 200, 304, 404 | Usually    | Yes           | Yes       |
| POST    | Create / submit data   | No    | Forms, resource creation  | Yes        | 201, 400, 422 | Usually no | No            | Yes       |
| PUT     | Replace full resource  | No    | Full update               | Yes        | 200, 204      | Usually no | No            | No        |
| PATCH   | Partial update         | No    | Update specific fields    | Yes        | 200, 204      | Usually no | No            | No        |
| DELETE  | Remove resource        | No    | Deletion                  | Sometimes  | 204, 404      | Usually no | No            | No        |
| HEAD    | Headers only           | Yes   | Metadata, file size check | No         | 200, 304      | Usually    | No            | No        |
| OPTIONS | Discover allowed methods | Yes | Capability, preflight     | Usually no | 200, 204      | Usually no | No            | No        |

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
