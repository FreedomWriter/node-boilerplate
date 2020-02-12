# Node RESTful API and Database

## Dependencies

- npm i --save-dev nodemon jest
- npm i express cors morgan helmet sqlite3 dotenv knex bcryptjs
- npx gitignore node

## Server

- create directory named `api`
- create file named `server.js`
  
- require 
  - const express = require("express")
  - const helmet = require("helmet")
  - const morgan = require("morgan")
  - const cors = require("cors")
  - 
- create server
  - const server = express()
  
- Once routers are created, require them here as well
  - const someRouter = require("../some/some-router")
  
- use required packages
  - server.use()
      - cors()
      - express.json()
      - helmet()
      - morgan("dev")
      - "/api/some", someRouter

- set up a test route - (to use when get with no endpoint is called)
  - server.get("/", (req, res) => { res.send("<h3>It's Alive!</h3>)})

- set up an error handler
  - server.use((err, req, res, next) => { console.log(err); res.status(500).json({message: "Something went wrong!"})})

- export server
  - module.exports = server

# Index.js (root level)

- Require server
  - const server = require("./api/server")

- define port
  - const port = process.env.PORT || 5000
  - server.listen(port, () => console.log(`\n*** Server Running on http://localhost:${port} ***\n`))

# Create knexfile.js

- create knexfile
  - npx knex init 
  - create configs in knexfile.js
    - module.exports = {
        development: {
            client: "sqlite3",
            useNullAsDefault: true,
            connection: {
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

# Create db.config.js (to be required in model files)

- in data directory
  - const knex = require("knex");
    const config = require("../knexfile");

    const env = process.env.DB_ENV || "development";

    module.exports = knex(config[env]);

# Create Tables

- table migrations are time stamped to ensure that they run in order
- this table include examples of foreign keys and intermediary tables 
- npx migrate:make initial
  - exports.up = async function(knex) {
        await knex.schema.createTable("projects", tbl => {
            tbl.increments();
            tbl.string("project_name").notNullable();
            tbl.text("description");
            tbl.boolean("complete").defaultTo("false");
        });
        await knex.schema.createTable("tasks", tbl => {
            tbl.increments();
            tbl.text("description").notNullable();
            tbl.text("notes");
            tbl.boolean("complete").defaultTo("false");
            tbl
            .integer("project_id")
            .notNullable()
            .unsigned()
            .references("id")
            .inTable("projects")
            .onUpdate("CASCADE")
            .onDelete("CASCADE");
        });
        await knex.schema.createTable("resources", tbl => {
            tbl.increments();
            tbl.string("resource_name").notNullable();
            tbl.text("description");
        });
        await knex.schema.createTable("projects_resources", tbl => {
            tbl
            .integer("project_id")
            .notNullable()
            .unsigned()
            .references("id")
            .inTable("projects")
            .onUpdate("CASCADE")
            .onDelete("CASCADE");
            tbl
            .integer("resource_id")
            .notNullable()
            .unsigned()
            .references("id")
            .inTable("resources")
            .onUpdate("CASCADE")
            .onDelete("CASCADE");
            tbl.primary(["project_id", "resource_id"]);
        });
        await knex.schema.createTable("contexts", tbl => {
            tbl.increments();
            tbl.text("context").notNullable();
        });
        await knex.schema.createTable("tasks_contexts", tbl => {
            tbl
            .integer("task_id")
            .notNullable()
            .unsigned()
            .references("id")
            .inTable("tasks")
            .onUpdate("CASCADE")
            .onDelete("CASCADE");
            tbl
            .integer("context_id")
            .notNullable()
            .unsigned()
            .references("id")
            .inTable("contexts")
            .onUpdate("CASCADE")
            .onDelete("CASCADE");
            tbl.primary(["task_id", "context_id"]);
        });
        };

        exports.down = async function(knex) {
        await knex.schema.dropTableIfExists("tasks_contexts");
        await knex.schema.dropTableIfExists("contexts");
        await knex.schema.dropTableIfExists("projects_resources");
        await knex.schema.dropTableIfExists("resources");
        await knex.schema.dropTableIfExists("tasks");
        await knex.schema.dropTableIfExists("projects");
    };
- npx knex migrate:latest
- if not yet in production and need to make changes
  - npx knex migrate:rollback 
    - allows you to rollback the table, make changes, then migrate:latest to apply changes
- if in production, it is better to create new migrations that `alter` the table to protect any data a user may have saved to database already

# Create Seeds

- seeds are not timestamped, create them with a prefix (00 for cleanup, the rest should start at 001 and increment to ensure that they run sequentially according to how they were created, as some data may be required in future tables they need to be run correctly. Add id's to seeds, it is not necessary but could avoid future issues with foreign keys expecting a particular id)
  
- npx knex seed:make 
  - example of multiple seeds create sequentially exist [here](https://github.com/FreedomWriter/node-db-challenge/tree/master/data/seeds)
  - exports.seed = async function(knex) {
        await knex("tasks").truncate();
        await knex("tasks").insert([
            { project_id: 1, description: "Enroll in Lambda" },
            { project_id: 1, description: "Cry", notes: "Don't cry too long" },
            { project_id: 1, description: "start understanding" }
        ]);
    };

- When all seeds are created run them
  - npx knex:seed run

# Create models

- For each resource, create a directory (in the case of projects as a resource, create a directory name projects)
- In this folder, create a file named projects-model.js
  - require the db config
    - const db = require("../data/db.config")
  - create needed functions and export them
    - function find() {
        return db("projects").select();
        }

        function findById(id) {
        return db("projects")
            .limit(req.query.limit)
            .orderBy("name", req.query.orderBy)
            .where({ id })
            .first();
        }

        async function add(project) {
        const [id] = await db("projects").insert(project);
        return db("projects")
            .where({ id })
            .first();
        }

        async function update(id, body) {
        await db("projects")
            .where({ id })
            .update(body);

        return findById(id);
        }

        function remove(id) {
        return db("projects")
            .where({ id })
            .del();
     }

     module.exports = {
        find,
        findById,
        add,
        update,
        remove
     };

# Create Validation Middleware

- Create a directory called middlewares. For each of the following middlewares, create a file for them and export.

- To restrict to and agent (for example: Postman)
  - module.exports = requiredAgent => (req, res, next) => {
    const userAgent = req.get("User-Agent");
    //   const formatted = requiredAgent.toLowercase();
    //   console.log(formatted);
    console.log(userAgent, requiredAgent);
    if (!userAgent.includes(requiredAgent)) {
        return res.status(500).json({ message: `Must be using ${requiredAgent}` });
    }

    next();
    };

- To validate id
  - const db = require("../accounts/accounts-router");

        async function validateId(req, res, next) {
        try {
            const account = await db("accounts")
            .where("id", req.params.id)
            .first();
            res.json(account);
        } catch (err) {
            next(`No account found with the id of ${req.params.id}`);
        }
    }

    module.exports = validateId;

- To validate that a required value was passed
  - module.exports = prop => (req, res, next) => {
        req.body[prop]
            ? next()
            : res.status(400).json({ errorMessage: `required ${prop}` });
    };

    Would be required like: 
    const validator = require("../middlewares/validator");
    and used like:
    router.post("/", validator("name"), validator("budget"), async (req, res, next) =>...

# Create Routers

- const express = require("express");
    const projectModel = require("./projects-model");
    const db = require("./projects-model");
    const tasksRouter = require("../tasks/tasks.router");

    const validator = require("../middlewares/validator");
    const validateID = require("../middlewares/validateId");

    const router = express.Router();

    router.use("/:id/tasks", tasksRouter);

    router.get("/", async (req, res, next) => {
    try {
        const projects = await db.find();
        res.json(projects);
    } catch (err) {
        next(err);
    }
    });

    router.get("/:id", validateID, async (req, res, next) => {
    const { id } = req.params;
    const project = await db.findById(id);
    if (project) {
        res.json(project);
    } else {
        res.status(404).json({ message: "Could not find project with given id" });
    }
    try {
    } catch (err) {
        next(err);
    }
    });

# Auth Router

- create a directory named auth
  
- create a file named auth-router.js
  - const router = require("express").Router();
    const bycrypt = require("bcryptjs");

    const usersModel = require("../users/users-model.js");

    router.post("/register", async (req, res, next) => {
    try {
        const saved = await usersModel.add(req.body);

        res.status(201).json(saved);
    } catch (err) {
        next(err);
    }
    });

    router.post("/login", async (req, res, next) => {
    try {
        const { username, password } = req.body;

        const user = await usersModel.findBy({ username }).first();
        const passwordValid = await bycrypt.compare(password, user.password);

        if (user && passwordValid) {
        res.status(200).json({ message: `Welcome ${user.username}!` });
        } else {
        res.status(401).json({ message: "Invalid Credentials" });
        }
    } catch (err) {
        next(err);
    }
    });

    module.exports = router;

- Example of using auth in a users model and router

- users-model.js
  
  - const bcrypt = require("bcryptjs");

    const db = require("../database/dbConfig.js");

    function find() {
        return db("users").first("id", "username");
    }

    function findBy(filter) {
        return db("users")
            .select("id", "username", "password")
            .where(filter);
    }

    async function add(user) {
        user.password = await bcrypt.hash(user.password, 14);
        const [id] = await db("users").insert(user);
        return findById(id);
    }

    function findById(id) {
        return db("users")
            .where({ id })
            .first("id", "username");
    }

    module.exports = {
        add,
        find,
        findBy,
        findById
    };

- users-router.js
  - const router = require("express").Router();
    const bycrypt = require("bcryptjs");

    const usersModel = require("./users-model.js");

    function restricted() {
    const authError = {
        message: "Invalid Credentials"
    };

    return async (req, res, next) => {
        try {
        const { username, password } = req.headers;
        if (!username || !password) {
            return res.status(401).json(authError);
        }
        const user = await usersModel.findBy({ username }).first();
        if (!user) {
            return res.status(401).json(authError);
        }
        const passwordValid = await bycrypt.compare(password, user.password);
        if (!passwordValid) {
            return res.status(401).json(authError);
        }
        // if we reach this point in the code we know that the user is authenticated
        next();
        } catch (err) {
        next(err);
        }
    };
    }

    router.get("/", restricted(), async (req, res, next) => {
    try {
        const users = await usersModel.find();
        res.json(users);
    } catch (err) {
        next(err);
    }
    });

    module.exports = router;