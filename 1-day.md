# Backend — Day 1

## Why does a Backend exist?

### 1. The problem I started with

Suppose Rahul registers on a website.

```text
Rahul
  ↓
Name + Email
  ↓
Register
```

If we store this only inside Rahul's browser/device:

* The data belongs only to that device.
* Rahul cannot automatically get the same data from another device.
* The application cannot maintain one shared state for all users.
* The user can potentially manipulate the local data.

So we need a **central place that maintains the application's shared/persistent state**.

---

## 2. My mental model of Backend

I currently think of backend as:

> **The trusted part of an application that receives requests, applies rules, and manages shared/persistent state.**

The basic flow is:

```text
User
 ↓
Frontend
 ↓ HTTP request
Backend
 ↓
Rules + validation + authorization
 ↓
Database
```

The backend is **outside the user's device**, so users cannot directly control its rules or database.

---

## 3. What happens when Rahul clicks Register?

```text
Rahul enters details
        ↓
Clicks Register
        ↓
Frontend sends HTTP request
        ↓
Backend endpoint receives it
        ↓
Backend checks the request
        ↓
Backend applies business rules
        ↓
Database is updated
        ↓
Backend sends response
        ↓
Frontend shows result
```

Important:

> The frontend asks. The backend decides.

---

## 4. One mistake I corrected

I initially thought:

> "For every operation/task, we write a backend function and give it a URL."

Correction:

> **Not every backend function becomes a URL.**

An API endpoint is an **entry point** into the backend.

For example:

```text
POST /users
```

might internally use:

```text
Endpoint
   ↓
Controller
   ↓
Service
   ↓
Repository
   ↓
Database
```

But functions such as:

```text
validateEmail()
hashPassword()
checkPermission()
calculatePrice()
```

can remain internal backend functions.

So:

> **Endpoint ≠ every backend function**

---

# 5. Never trust the frontend

This became one of the most important things I learned today.

The frontend can send:

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com"
}
```

But a user can manually change the request and send:

```json
{
  "name": "Rahul",
  "email": "admin@college.com",
  "role": "ADMIN"
}
```

Therefore:

> **Anything coming from the client is untrusted input.**

Hiding a field in the frontend is **not security**.

The backend must enforce the actual rules.

---

# 6. Authentication vs Authorization

I initially mixed these together.

### Authentication

> **Who are you?**

Example:

```text
This request belongs to user 123.
```

### Authorization

> **What are you allowed to do?**

Example:

```text
User 123 can edit user 123.
User 123 cannot edit user 124.
```

So:

```text
Authentication
      ↓
Who?
      ↓
Authorization
      ↓
Allowed to do what?
```

---

# 7. My first authorization rule

If Rahul is user `123`:

```text
Authenticated user = 123
Requested user = 123
        ↓
      ALLOW
```

But:

```text
Authenticated user = 123
Requested user = 124
        ↓
      DENY
```

I initially thought this was the whole authorization system.

It isn't.

An administrator might be allowed to edit user `124`.

So the real question becomes:

```text
Who are you?
     ↓
What permissions/role do you have?
     ↓
What resource are you accessing?
     ↓
What operation are you trying to perform?
     ↓
Is this allowed?
```

---

# 8. Backend ≠ Database

I initially thought of the server as the place that stores everything.

Better model:

```text
Backend
   ↓
Decides what should happen

Database
   ↓
Stores what currently exists
```

So normally:

```text
Client
  ↓
Backend
  ↓
Database
```

The client should **not directly control the database**.

---

# 9. The important distinction I need to remember

When a request arrives, different questions exist.

```text
Authentication
"Who are you?"

Authorization
"Are you allowed to do this?"

Validation
"Is this data acceptable?"

Business rules
"Does this operation follow our application's rules?"

Database constraints
"Can this state actually be stored correctly?"
```

These are related, but **they are not the same thing**.

---

# 10. My current backend mental model

```text
                 USER
                   ↓
               FRONTEND
                   ↓
              HTTP REQUEST
                   ↓
                BACKEND
                   ↓
        ┌──────────┼──────────┐
        ↓          ↓          ↓
 Authentication Authorization Validation
        └──────────┼──────────┘
                   ↓
            Business Rules
                   ↓
                DATABASE
                   ↓
              HTTP RESPONSE
                   ↓
               FRONTEND
                   ↓
                 USER
```

### One sentence to remember

> **The client requests. The backend decides. The database remembers.**
