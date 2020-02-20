# Automated testing node.js (Still needs to be audited for accuracy)

Types of Automated Testing

- unit testing - we use this to test our pure functions
- integrations testing - we use this to test routers and middleware, _should take priority_
- snapshot testing
- end-to-end testing

`npm --save-dev jest` - would have already done this is following guide

# Add a test script

```js
    //start
    "test": "cross-env DB_ENV=testing jest --verbose --watch"
    // add
      "jest": {
    "testEnvironment": "node"
    }
```

# Create first test file

In root create `index.spec.js` or alternatively `index.test.js`

Create a placeholder test to ensure our test runner is finding files.

    ```js
    test("a placeholder test", async () => {
    expect(2 + 2).toBe(4);
    });
    ```

# Supertest

## We have split out the code so that server.js defines the server and index.js actually starts it, so this isn't a problem for us.

When using supertest, we don't want the server running, supertest will start it behind the scenes, automatically. Because of this, we need to adjust our index.js file. There, we have server.listen() which is running the server. We don't want that! We want to export our server. Then a really simple way to handle this problem by saying if this index file is required into another file, don't actually start the server, just export it. But if this index file is actually the file that node is running, start the server.

    ```js
    // in index.js
    if (!module.parent) {
    const port = process.env.PORT || 5000;
    server.listen(port, () => console.log(`\n** server up on port ${port} **\n`));
    }
    const port = process.env.PORT || 5000;
    server.listen(port, () => console.log(`\n** server up on port ${port} **\n`));

    module.exports = server;
    ```

Another method of solving this could be:

- We could use an environment variable

  We have split out the code so that server.js defines the server and index.js actually starts it, so this isn't a problem for us.

Consider 3 questions:

1. Does it return the expected status code?
2. Does it return the expected data format?
3. Does it return the expected data?

# Simple Integration test

    ```js
    // test("a placeholder test", async () => {
    // expect(2 + 2).toBe(4);
    // });

    const supertest = require("supertest");
    //remember our server wount actually start
    // due to the if statement in index.js
    const server = require("./index");
    const db = require("./data/dbConfig");

    beforeEach(async () => {
    await db.seed.run;
    });

    test("welcome route", async () => {
    const res = await supertest(server).get("/");
    // Does it return the expected status code?
    expect(res.status).toBe(200);
    // // Does it return the expected data format?
    expect(res.type).toBe("application/json");
    // Does it return the expected data?
    // console.log(res.body);
    expect(res.body.api).toBe("up");
    });

    test("create hobbit route", async () => {
    const res = await supertest(server)
    .post("/hobbits")
    .send({ name: "gaffer" });
    expect(res.status).toBe(201);
    expect(res.type).toBe("application/json");
    expect(res.body.name).toBe("gaffer");
    //deep assertion
    expect(res.body).toEqual({ id: 5, name: "gaffer" });
    });

    test("get hobbit list", async () => {
    const res = await supertest(server).get("/hobbits");
    expect(res.status).toBe(200);
    expect(res.type).toBe("application/json");
    expect(res.body.length).toBeGreaterThan(0);
    expect(res.body[0].id).toBe(1);
    expect(res.body[0].name).toBe("sam");
    });
    ```

# Testing routes that access the database

In `knexfile.js`

    ```js
    test: {
        client: 'sqlite3',
        connection: {
        filename: './data/test.db3',
        },
        useNullAsDefault: true,
        migrations: {
        directory: './data/migrations',
        },
        seeds: {
        directory: './data/seeds',
        },
    }
    ```

In terminal:

    ```node
    npx knex migrate:latest --env=dev
    ```

    ```node
    npx knex migrate:latest --env=test
    ```

# Unit test for model

Hobbits Model Unit Tests
create a file called `hobbitsModel.spec.js` in the `hobbits` directory

```js
const db = require("../data/dbConfig");
const hobbitsModel = require("./hobbitsModel");

beforeEach(async () => {
  //re seed the database each time tests are run
  await db.seed.run();
});
describe("hobbits model", () => {
  test("getAll", async () => {
    const all = await hobbitsModel.getAll();
    expect(all).toHaveLength(4);
  });

  test("findById", async () => {
    const res = await hobbitsModel.findById(1);
    expect(res.name).toBe("sam");
  });

  test("insert", async () => {
    await hobbitsModel.insert({ name: "bilbo" });
    const hobbits = await db("hobbits").select();
    expect(hobbits).toHaveLength(5);
  });

  test("update", async () => {
    await hobbitsModel.update(1, { name: "tom" });
    const res = await hobbitsModel.findById(1);
    expect(res.name).toBe("tom");
  });

  test("delete", async () => {
    const res = await hobbitsModel.remove(1);
    expect(res).toBe(1);
    const hobbits = await hobbitsModel.getAll();
    expect(hobbits).toHaveLength(3);
    expect(hobbits.length).toBeGreaterThan(0);
  });
});

// Need an afterEach to stop leak (destroy db connection)
```
