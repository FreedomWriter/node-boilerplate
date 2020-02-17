# SQLite

- SQLite is a Data Base Management System (DMBS).
- It is a Relational (R)DBMS

- SQL is the query language, it's applicable to many RDBMS

?'s to ask ourselves
-How many columns?
-What goes in the columns?
-Are there any restrictions?

There is one required column for every table - the Primary Key (ID)

- Does not have to be named ID.
- Does not have to be a number, only has to be unique.

What's the difference between a Database Schema and a Database Table?

- A Schema is the overall structure of the Database.
- A Schema can have multiple Tables which are _like_ spreadsheets.

# SQLite Datatypes

- https://www.tutorialspoint.com/sqlite/sqlite_data_types.htm

1
NUMERIC

    NUMERIC
    DECIMAL(10,5)
    BOOLEAN
    DATE
    DATETIME

2
INTEGER

The value is a signed integer, stored in 1, 2, 3, 4, 6, or 8 bytes depending on the magnitude of the value.

    INT
    INTEGER
    TINYINT
    SMALLINT
    MEDIUMINT
    BIGINT
    UNSIGNED BIG INT
    INT2
    INT8

3
REAL

The value is a floating point value, stored as an 8-byte IEEE floating point number.

    REAL
    DOUBLE
    DOUBLE PRECISION
    FLOAT

4
TEXT

The value is a text string, stored using the database encoding (UTF-8, UTF-16BE or UTF-16LE)

    CHARACTER(20)
    VARCHAR(255)
    VARYING CHARACTER(255)
    NCHAR(55)
    NATIVE CHARACTER(70)
    NVARCHAR(100)
    TEXT
    CLOB

5
BLOB

The value is a blob of data, stored exactly as it was input.

ALSO null

This list in not exhastive, SQLite is a light version of SQL and it plays well with some of the

# Constraints

- NOT NULL Constraint − Ensures that a column cannot have NULL value.

- DEFAULT Constraint − Provides a default value for a column when none is specified.

- UNIQUE Constraint − Ensures that all values in a column are different.

- PRIMARY Key − Uniquely identifies each row/record in a database table.

- CHECK Constraint − Ensures that all values in a column satisfies certain conditions.

- Dropping Constraint - SQLite supports a limited subset of ALTER TABLE. The ALTER TABLE command in SQLite allows the user to rename a table or add a new column to an existing table. It is not possible to rename a column, remove a column, or add or remove constraints from a table.

# DBL commands

CREATE TABLE < table name> (
< column name > < data type > < contraints >,
< column name > < data type > < contraints >,
< column name > < data type > < contraints >
):

DROP TABLE < table name >; //will delete table

ALTER TABLE < table name > < arguments >

# Steps

1. Open DB Browser
2. New Database - find project folder, create
3. Go to Execute SQL tab

   1. CREATE TABLE "table-name"

      `CREATE TABLE "fruits" ( "id" INTEGER NOT NULL UNIQUE PRIMARY KEY AUTOINCREMENT );`

   2. To add another row to an existing table:

```javascript
      DROP TABLE IF EXISTS "fruits"; CREATE TABLE "fruits" ( "id" INTEGER NOT NULL UNIQUE PRIMARY KEY AUTOINCREMENT, "name" TEXT NOT NULL UNIQUE, "avgWeightOz" FLOAT, "delicious" BOOLEAN DEFAULT 0 );
```

3.  ALTER TABLE can be used to change the table name

    `ALTER TABLE "fruitss" RENAME TO "fruits"`

The produce.db3-journal file keeps the database read-only. In the SQL browser, click `Write Changes` to "release" the database. (May need to restart server)

When we `npm i knex` it not only installs the knex dependency but it also installs a knex CLI can access by `npx knex`

`npx knex init` - creates knexfile.js which comes with a lot of boiler plate that we don't need. For now, delete everything except development.

    For this project:

```javascript
    module.exports = {
        deveplopment: {
            client: "sqlite3",, // our DBMS driver
            connection: {
                filename: "./data/produce.db3"
            }, // the location of our DB
            useNullAsDefault: true, // neccesary when using SQLite
        },
    }

    {


    useNullAsDefault: true

}
```

```javascript
const knex = require("knex");
const config = require("../knexfile");

const db = knex(config.development);

module.exports = db;
```

    In fruits-router:

```javascript
const db = require("../utils/db");
```

# Creating Schema outside of SQL Browser

Shortcommings of creating schema in SQL Browser

- Incovenient
- Version Control
- Changes are irreversible

Database Migrations - https://en.wikipedia.org/wiki/Schema_migration

Code written to a file that describes the changes that need to happen over time

The knex CLI has a schema builder and a migrations feature.

SQL Schema Builder - http://knexjs.org/#Schema

1. `npx knex migrate:make fruits` Where `fruits` is the migration name.
2. Migrations folder with a file that was create (filename is a timestamp and the name we gave the migration separated by an underscore). The timestamp is because we make changes to our migrations over time and they run in order they were defined in. Imagine trying to add a row to a table that hadn't been created yet?

   1. This file has two exported functions in the `up` function is where we are going to put our schema builder code that creates or changes our schema in some way.

   ```javascript
   exports.up = async function(knex) {
       await knex.schema.createTable("fruits", table => {
           // translates to:`"id" INTEGER NOT NULL UNIQUE PRIMARY KEY AUTOINCREMENT`//
           table.integer("id").notNull().unique().primary()
           // Knex gives us a shortcut table.increments("id");
           // translates to:`"name" NOT NULL UNIQUE`
           table .text("name") .notNull() .unique();
           // translates to:`"avgWeightOz" FLOAT`
           table.float("avgWeightOz");
           // translates to`"delicious" BOOLEAN DEFAULT 0`
           table.boolean("delicious").defaultTo(false); // or 0 });
           };
   ```

To bring those changes into the db `npx knex migrate:latest`

1.  The `down` function is like moving back one step in time. It should be the reverse of the up function,

```javascript
   exports.down = async function(knex) {
        await knex.schema.dropTableIfExists("fruits");
        };`
```

We DROP the table here execute by `npx knex migrate:rollback`

To Add a Column:

```javascript
exports.up = async function(knex) {
  await knex.schema.alterTable("fruits", table => {
    table.text("color");
  });
};

exports.down = async function(knex) {
  await knex.schema.alterTable("fruits", table.dropColumn("color"));
};
```

- Always better to roll a new migration out to fix erros than to rollback, you don't want to lose data that your customers might have added.
- Migrations run in batches. If we create our migrations at once, then it rolls them back in the same batch. When we roll back, it rolls back the last batch, not the last file.

# Seeds

1. To create a Seed file with the CLI `npx knex seed:make 001_fruits` where < 001_fruits > is the file name. Unlike with migrations, knex does not prepend a date stamp which means if we want them to run in order, we need to number them in a way that is seqential.

2. In the new Seed file:

```javascript
exports.seed = async function(knex) { await knex("fruits".truncate(), await knex("fruits").insert([ { name: "dragonfruit", avgWeightOz: 16.7, delicious: true, color: "red" }, { name: "strawberry", avgWeightOz: 0.42, delicious: true, color: "red" }, { name: "banana", avgWeightOz: 4.0, delicious: true, color: "yellow" }, { name: "noni", avgWeightOz: 15.0, delicious: false, color: "white" } ]); };
```

To run Seed file: `npx knex seed:run`
