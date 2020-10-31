Create a directory named config

# Secrets

In config directory:

- Create file named `secrets.js`

```javascript
module.exports = {
  jwtSecret: process.env.JWT_SECRET
};
```
