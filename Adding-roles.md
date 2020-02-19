3 parts to making `roles` work

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

2.  Add priveldges to token when we hand it back to the browser
    role restricted to Admin

    ```js
    router.get("/", restricted, checkRole("Admin"), (req, res) => {
      Users.find()
        .then(users => {
          res.json(users);
        })
        .catch(err => res.send(err));
    });
    ```

3)  Add role to payload

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
