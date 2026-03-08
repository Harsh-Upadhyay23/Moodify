# Mongoose `pre()` and `post()` Middleware (UserSchema Example)

## 1. What is Middleware in Mongoose?

In **Mongoose**, middleware are functions that run **before or after a specific database operation**.

They help you perform tasks like:

* Password hashing
* Logging
* Validation
* Sending notifications

Mongoose provides two main middleware types:

| Middleware | When it Runs         |
| ---------- | -------------------- |
| `pre()`    | Before the operation |
| `post()`   | After the operation  |

---

# 2. `pre()` Middleware

## Definition

`pre()` middleware runs **before a specific operation is executed**.

Think of it like a **checkpoint before saving data**.

### Syntax

```javascript
schema.pre("operation", function(next){
   // code
   next();
});
```

### Parameters

* **operation** → the mongoose operation (`save`, `find`, `deleteOne`, etc.)
* **next()** → tells mongoose to continue the operation

---

## Example: Hash Password Before Saving User

```javascript
userSchema.pre("save", async function(next){
    if(!this.isModified("password")) return next();

    const bcrypt = require("bcrypt");

    this.password = await bcrypt.hash(this.password,10);

    next();
});
```

### What Happens Here

1. User tries to save data
2. `pre("save")` runs **before saving**
3. Password gets hashed
4. Data is stored securely in database

### Flow

```
User Save Request
       ↓
pre("save") runs
       ↓
Password hashed
       ↓
Data saved to MongoDB
```

---

# 3. `post()` Middleware

## Definition

`post()` middleware runs **after the operation is completed**.

It is mainly used for:

* Logging
* Sending response
* Notifications
* Analytics

### Syntax

```javascript
schema.post("operation", function(doc){
   // code
});
```

### Parameters

| Parameter | Meaning                               |
| --------- | ------------------------------------- |
| `doc`     | the document returned after operation |

---

## Example: Log User After Save

```javascript
userSchema.post("save", function(doc){
    console.log("User saved successfully:", doc.email);
});
```

### What Happens Here

1. User data is saved
2. `post("save")` runs
3. Success message is logged

### Flow

```
User Save Request
       ↓
Data saved in MongoDB
       ↓
post("save") runs
       ↓
Log / notification
```

---

# 4. Difference Between `pre()` and `post()`

| Feature      | `pre()`          | `post()`           |
| ------------ | ---------------- | ------------------ |
| Runs         | Before operation | After operation    |
| Use case     | Modify data      | Logging / response |
| Has `next()` | Yes              | No                 |
| Example      | Hash password    | Log saved user     |

---

# 5. Simple Real Life Example

Imagine **Airport Security**

| Stage                          | Middleware Type |
| ------------------------------ | --------------- |
| Security check before boarding | `pre()`         |
| Announcement after landing     | `post()`        |

---

# 6. Common Operations Used With Middleware

| Operation   | Meaning         |
| ----------- | --------------- |
| `save`      | Save document   |
| `validate`  | Validate schema |
| `find`      | Find documents  |
| `deleteOne` | Delete document |
| `updateOne` | Update document |

Example:

```javascript
schema.pre("find", function(){
    console.log("Searching users...");
});
```

---

# 7. Full Example

```javascript
const mongoose = require("mongoose");
const bcrypt = require("bcrypt");

const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  password: String
});

// Pre middleware
userSchema.pre("save", async function(next){
    if(!this.isModified("password")) return next();

    this.password = await bcrypt.hash(this.password,10);
    next();
});

// Post middleware
userSchema.post("save", function(doc){
    console.log("New user created:", doc.email);
});

const User = mongoose.model("User", userSchema);
```

---

# 8. One Line Summary

| Middleware | Meaning                           |
| ---------- | --------------------------------- |
| `pre()`    | Run **before** database operation |
| `post()`   | Run **after** database operation  |

---

# Final Quick Revision

```
pre()  → before database action
post() → after database action
```

Example:

```
pre("save")  → before saving user
post("save") → after saving user
```
