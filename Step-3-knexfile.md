# Create knexfile.js

- create knexfile
  - npx knex init
  - create configs in knexfile.js

# Example of a knex file:

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
  },
  testing: {
    client: "sqlite3",
    connection: {
      filename: "./data/test.db3"
    },
    useNullAsDefault: true,
    migrations: {
      directory: "./data/migrations"
    },
    seeds: {
      directory: "./data/seeds"
    }
  }
};
```
