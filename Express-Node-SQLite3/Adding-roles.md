## 3 parts to making `roles` work (a simple implementation)

1.  Additional Middleware

    ```js
    module.exports = role => {
      return function(req, res, next) {
        //req.decodedJwt.roles is just looking for a role that we added
        //to
        if (req.decodedJwt.roles && req.decodedJwt.roles.includes(role)) {
          next();
        } else if (
          req.decodedJwt.roles &&
          req.decodedJwt.roles.includes("Admin")
        ) {
          next();
        } else {
          res.status(403).json({ you: "don't have permission" });
        }
      };
    };
    ```

2.  Use middleware in routes restricted by role

    ```js
    router.get("/", restricted, checkRole("Admin"), (req, res) => {
      Users.find()
        .then(users => {
          res.json(users);
        })
        .catch(err => res.send(err));
    });
    ```

3)  Add priveldges to token when we hand it back to the browser
    role restricted to Admin

    ```js
    function genToken(user) {
      //can perform other logic here
      const payload = {
        userid: user.id,
        username: user.username,
        // add roles: ["role"] to genToken file
        // if above mentioned logic exists:
        //roles: user.role
        roles: ["Student"]
      };

      const options = { expiresIn: "1h" };
      const token = jwt.sign(payload, secrets.jwtSecret, options);

      return token;
    }
    ```

# Asyc/Await Example

1.  Additional Middleware

    ```js
    const secrets = require("../config/secrets");
    const jwt = require("jsonwebtoken");

    module.exports = department => {
      return (req, res, next) => {
        const token = req.headers.authorization;

        if (token) {
          jwt.verify(token, secrets.jwt, (err, payload) => {
            if (err) {
              res.status(403).json({ message: "You are not authorized" });
            } else {
              if (department !== payload.userDepartment) {
                res.status(403).json({
                  message:
                    "You are not do not have permission for this endpoint."
                });
              } else {
                req.userDepartment = payload.userDepartment;
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

1.  Use middleware in routes restricted by role

```js
router.get("/", permissions("finance"), async (req, res, next) => {
  try {
    const users = await db.find().where("department", req.userDepartment);
    res.json({ users });
  } catch (err) {
    next(err);
  }
});
```

2.  Add priveldges to token when we hand it back to the browser
    role restricted by department

```js
function genToken(user) {
  return jwt.sign(
    {
      userId: user.id,
      userDepartment: user.department
    },
    secrets.jwt,
    {
      expiresIn: "5h"
    }
  );
}
```
