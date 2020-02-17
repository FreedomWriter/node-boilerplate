# Create Validation Middleware

Create a directory called middlewares. For each of the following middlewares, create a file for them and export.

## To restrict to and agent (for example: Postman)

```javascript
module.exports = requiredAgent => (req, res, next) => {
  const userAgent = req.get("User-Agent");
  //   const formatted = requiredAgent.toLowercase();
  //   console.log(formatted);
  console.log(userAgent, requiredAgent);
  if (!userAgent.includes(requiredAgent)) {
    return res.status(500).json({ message: `Must be using ${requiredAgent}` });
  }

  next();
};
```

## To validate id

```javascript
const db = require("../accounts/accounts-router");

async function validateId(req, res, next) {
  try {
    const account = await db("accounts")
      .where("id", req.params.id)
      .first();
    res.json(account);
  } catch (err) {
    next(`No account found with the id of ${req.params.id}`);
  }
}

module.exports = validateId;
```

## To validate that a required value was passed

```javascript
module.exports = prop => (req, res, next) => {
  req.body[prop]
    ? next()
    : res.status(400).json({ errorMessage: `required ${prop}` });
};
```

Would be required like:

```javascript
    const validator = require("../middlewares/validator");
    and used like:
    router.post("/", validator("name"), validator("budget"), async (req, res, next) =>...
```

## Restricted

```javascript
const secrets = require("../config/secrets");
const jwt = require("jsonwebtoken");

module.exports = (req, res, next) => {
  const token = req.headers.authorization;

  if (token) {
    jwt.verify(token, secrets.jwt, (err, payload) => {
      if (err) {
        res.status(403).json({ message: "You are not authorized" });
      } else {
        req.userId = payload.userId;
        next();
      }
    });
  } else {
    res.status(400).json({ message: "No credentials provided" });
  }
};
```

## Restricted with Persmissions

```javascript
const secrets = require("../config/secrets");
const jwt = require("jsonwebtoken");

module.exports = role => {
  return (req, res, next) => {
    const token = req.headers.authorization;

    if (token) {
      jwt.verify(token, secrets.jwt, (err, payload) => {
        if (err) {
          res.status(403).json({ message: "You are not authorized" });
        } else {
          if (role !== payload.userRole) {
            res.status(403).json({
              message: "You are not do not have permission for this endpoint."
            });
          } else {
            req.userId = payload.userId;
            next();
          }
        }
      });
    } else {
      res.status(400).json({ message: "No credentials provided" });
    }
  };
};
```
