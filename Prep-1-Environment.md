## Dependencies

```javascript
npm init
npm i --save-dev nodemon jest supertest cross-env
npm i express cors morgan helmet sqlite3 dotenv knex bcryptjs
npx gitignore node
```

# Package.json

Scripts

    ```js
        "server": "cross-env NODE_ENV=dev nodemon index.js",
        "start": "node index.js",
        "test": "cross-env NODE_ENV=test jest --verbose --watch"
    ```

Add an additional configuration

    ```js
    ,
    "jest": {
        "testEnvironment": "node"
    }

```

# JWT

- npm i jsonwebtoken

# Sessions and Cookies

-npm i express-session

# .env file

//could do a secrets file that exports and object then call secrets.PORT, etc...
PORT = 5000
JWT_SECRET = "super secret key string"

# Add to .gitignore

```

# database

\*db3

```

```
