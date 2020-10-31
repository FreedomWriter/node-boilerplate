# Sessions [(express-sessions)](https://github.com/expressjs/session#readme)

## Session/Cookie Flow:

- Client sends credentials to server
- Server verifies credentials
- Server creates a session for the client
- Server sends back cookie as header (Set-Cookie header)
- Client stores thecookie in it's cookie jar
- Client sends all cookies in the cookie jar on every request
- Server verifies the cookies is valid
- Server provides access to resource

Allows us to create sessions in memomry on our machine:

    `npm i express-session`

    To be used:

```javascript
const session = require("express-session");
server.use(session());
```

    In the login router, assign the session to the user:
    req.session.user = user;

    To validate in protected routes:

```javascript
if (!req.session || !req.session.user) {
  return res.status(403).json({
    message: "You are not authorized"
  });
}
```

The session function can take an options object:

```javascript
server.use(
  session({
    name: "banana", //, sid - changes name so it is not obvious we are using express-session
    resave: false, //keep it false to avoid recreating sessions that have not changed
    saveUninitialized: false, // GDPR laws agains setting cookies automatically
    secret: "keep it secret, keep it safe!", // to cryptographically sign the cookie, should abstract into an environment variable (.env)
    cookie: {
      httpOnly: true, //javascript can't access the contents of the cookie
      maxAge: 1000 * 60 * 60 * 24 * 7, //logs user out after 7 days
      secure: false // in prod this should be true so the cookie header is encrypted
    }
  })
);
```

Updated restricted middleware:

```javascript
module.exports = () => {
  const authError = {
    message: "Invalid Credentials"
  };

  return (req, res, next) => {
    if (!req.session || !req.session.user) {
      return res.status(401).json(authError);
    }

    next();
  };
};
```

Updated protected route:

```javascript
router.get("/protected", restricted(), async (req, res, next) => {
  try {
    res.json({
      message: "You are authorized"
    });
  } catch (err) {
    next(err);
  }
});
```

Logout:

```javascript
router.get("/logout", restricted(), (req, res, next) => {
  req.session.destroy(err => {
    if (err) {
      next(err);
    } else {
      res.json({
        message: "You are logged out"
      });
    }
  });
});
```

Adding persistance:
As it stands, the session is stored in memory so everytime the server restarts, we lose our authorization. [Here is a list of express-session compatible stores.](https://github.com/expressjs/session#compatible-session-stores)

`npm i connect-session-knex`

In server.js

```javascript
const knexSessionStore = require("connect-session-knex")(session);
```

Add to sessionConfig:

```javascript
const sessionConfig = {
  name: "banana",
  resave: false,
  saveUninitialized: false,
  secret: "Keep it secret, keep it safe!",
  cookie: {
    httpOnly: true,
    maxAge: 1000 * 60 * 60 * 24 * 7,
    secure: false
  },
  store: new knexSessionStore({
    knex: require("../data/db.config"),
    tablename: "sessions",
    sidfieldname: "sid",
    createtable: true,
    clearInterval: 1000 * 60 * 60
  })
};
```

This new store config creates a table in the database to store sessions
