# Create db-config.js (to be required in model files)

- In data directory

# Example of a db-config.js file:

```javascript
const knex = require("knex");
const config = require("../knexfile");

const env = process.env.DB_ENV || "development";

module.exports = knex(config[env]);
```
