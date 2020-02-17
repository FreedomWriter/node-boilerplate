# Index.js (root level)

- Require server

```javascript
const server = require("./api/server");

//define port
const port = process.env.PORT;
server.listen(port, () =>
  console.log(`\n*** Server Running on http://localhost:${port} ***\n`)
);
```
