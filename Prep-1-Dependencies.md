## Dependencies

```javascript
npm init
npm i --save-dev nodemon jest
npm i express cors morgan helmet sqlite3 dotenv knex bcryptjs
npx gitignore node
```

# Add scripts

    ```js
            "server": "nodemon index.js",
        "start": "node index.js",
        "test": "jest --verbose --watch"
    ```

# JWT

- npm i jsonwebtoken

# Sessions and Cookies

-npm i express-session

# .env file

PORT = 5000
JWT_SECRET = "super secret key string"

# Add to .gitignore

```
# database
*db3
```
