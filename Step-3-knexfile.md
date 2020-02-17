# Create knexfile.js

- create knexfile
  - npx knex init
  - create configs in knexfile.js

```javascript
module.exports = {
  development: {
    client: "sqlite3",
    useNullAsDefault: true,
    connection: {
      /* this is where we define our database name */
      filename: "./data/projects.db3"
    },
    migrations: {
      directory: "./data/migrations"
    },
    seeds: {
      directory: "./data/seeds"
    },
    pool: {
      afterCreate: (conn, done) => {
        conn.run("PRAGMA foreign_keys = ON", done);
      }
    }
  }
};
```
