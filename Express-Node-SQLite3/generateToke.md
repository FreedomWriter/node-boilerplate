# Function to generate a token

```javascript
const jwt = require("jsonwebtoken");
const secrets = require("../config/secrets");

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
```

# Example adding permissions to a user

```javascript
function generateToken(user) {
  return jwt.sign(
    {
      userId: user.id,
      userRole: "student"
    },
    "super secret string",
    {
      expiresIn: "1h"
    } //1h = 1 hour
  );
}
```

# Example of use in a login router

```javascript
router.post("/login", (req, res) => {
  let { username, password } = req.body;

  Users.findBy({ username })
    .first()
    .then(user => {
      if (user && bcrypt.compareSync(password, user.password)) {
        generateToken(user);
        res.status(200).json({
          message: `Welcome ${user.username}`,
          authToken: generateToken
        });
      } else {
        res.status(401).json({ message: "Invalid Credentials" });
      }
    })
    .catch(error => {
      res.status(500).json(error);
    });
});
```

# Returning token on "/register" (no login after register)

```javascript
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
```
