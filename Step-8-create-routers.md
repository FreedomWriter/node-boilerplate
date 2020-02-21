# Example of "/auth/login" and "/auth/register"

## Note

These files are using `bcrypt`, `jwt`, `secrets`
We are generating our `token` in this file
We `hash` the password on `register` before sending it on to our model to be added to the db

# Example of an Auth Router:

```javascript
const router = require("express").Router();
const bcrypt = require("bcryptjs");
const jwt = require("jsonwebtoken");

const secrets = require("../config/secrets");
const Users = require("../users/users-model.js");

function generateToken(user) {
  return jwt.sign(
    {
      userId: user.id
    },
    secrets.jwt,
    {
      expiresIn: "1h"
    } //1h = 1 hour
  );
}

// async/await

router.post("/register", async (req, res, next) => {
  try {
    let user = req.body;
    const hash = bcrypt.hashSync(user.password, 10);
    user.password = hash;
    const newUser = await db.add(user);
    const token = await genToken(newUser);
    res.status(201).json({
      message: `Welcome ${user.username}`,
      token: token,
      department: user.department
    });
  } catch (err) {
    next(err);
  }
});

// for endpoints beginning with /api/auth
router.post("/register", (req, res) => {
  let user = req.body;
  const hash = bcrypt.hashSync(user.password, 10); // 2 ^ n
  user.password = hash;

  Users.add(user)
    .then(saved => {
      const token = generateToken(saved);
      res.status(201).json({
        message: `Welcome ${user.username}`,
        authToken: token
      });
    })
    .catch(error => {
      res.status(500).json(error);
    });
});

router.post("/login", (req, res) => {
  let { username, password } = req.body;

  Users.findBy({ username })
    .then(user => {
      if (user && bcrypt.compareSync(password, user.password)) {
        const token = generateToken(user);
        res.status(200).json({
          message: `Welcome ${user.username}`,
          authToken: token
        });
      } else {
        res.status(401).json({ message: "Invalid Credentials" });
      }
    })
    .catch(error => {
      res.status(500).json(error);
    });
});

module.exports = router;
```
