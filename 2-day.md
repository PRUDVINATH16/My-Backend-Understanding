# Backend — Day 1 Continued

## 11. Validation

Authorization answers:

> **"Can this user perform this operation?"**

Validation answers:

> **"Is the data they sent acceptable?"**

Example:

```text
name  → string
age   → number and >= 0
email → valid format
```

So a request can be:

```text
Authentication ✅
Authorization  ✅
Validation     ❌
```

Being allowed to perform an operation does **not** mean the input is valid.

---

## 12. Don't blindly accept client fields

Suppose Rahul sends:

```json
{
  "name": "Rahul",
  "email": "rahul@gmail.com",
  "role": "ADMIN"
}
```

The problem isn't that `"ADMIN"` is an invalid value.

The problem is:

> **Rahul should not be allowed to decide his own role.**

So the backend defines which fields an operation can change.

```text
User can update:
    name
    email
    age

User cannot update:
    role
    userId
    createdAt
```

### Remember

> **The client sends data. The backend decides what data it will accept.**

---

## 13. Unique ID vs Unique Data

I initially mixed these together.

### User ID

```text
101
102
103
```

Identifies each record uniquely.

### Email

```text
rahul@gmail.com
```

May also need to be unique because of a business rule:

> One email → one account.

So:

> **Not every field needs to be unique.**

---

## 14. Concurrent requests

I initially assumed:

> "The first request finishes, then the second request starts."

That's not safe.

Two requests can execute at almost the same time:

```text
Request A → Check email → Not found
Request B → Check email → Not found

Request A → Insert
Request B → Insert
```

Therefore:

> **A backend check alone cannot guarantee uniqueness.**

---

## 15. Database constraints

If our rule is:

> One email → one account

the database should enforce it:

```text
email → UNIQUE
```

Then even concurrent requests cannot both successfully create the same unique email.

### Remember

> **Backend checks early. Database protects critical data integrity.**

---

## 16. Database error vs User response

If the database rejects duplicate email, don't send the raw database error to the user.

Bad:

```text
UNIQUE constraint violation
users.email
PostgreSQL error...
```

Better:

```text
"This email is already registered."
```

Why?

* User gets a useful message.
* Internal database details aren't exposed.

### Mental model

> **Database speaks database language. API speaks application language.**

---

## 17. Internal error vs User response

If the database goes down:

```text
Authentication ✅
Authorization  ✅
Validation     ✅
Business rules ✅
Database       ❌
```

User should generally receive:

> **"Something went wrong. Please try again later."**

But internally, we should keep the real error for debugging/logging.

```text
Actual error
     ↓
 ┌───┴────┐
 ↓        ↓
Internal  User
detailed  simple
error     message
```

### Remember

> **Detailed internally. Simple externally.**

---

# Final Day 1 mental model

```text
HTTP Request
      ↓
Authentication
      ↓
Authorization
      ↓
Validation
      ↓
Business Rules
      ↓
Database
      ↓
HTTP Response
```

And the principles I discovered:

> **Client input is untrusted.**

> **Frontend restrictions are not security.**

> **Authentication ≠ Authorization ≠ Validation.**

> **Backend checks rules; database protects critical data integrity.**

> **Don't assume requests happen sequentially.**

> **Don't expose internal errors to users.**

### One-line revision

> **The client requests. The backend decides. The database remembers and protects the data.**
