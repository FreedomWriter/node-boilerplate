# Data Modeling Notes

# Normalization

- No field values are repeated
- No redundant records
- Each record has a unique primary key
- All fields in a table should directly relate to the primary key (you shouldn't have a column in a table that is not related to that data)

# One to One 1:1

- each citizen has one social security number, each social security number belongs to one citizen
- each house has one electricity account, each account corresponds to one house

Either table could have the foreign key, but that foreign key must be unique. Consider if it would be better to represent the data in a single table. (It often will be better to have two tables.)

# One to Many 1:\*

- one user can have many posts, but each post belongs to one user
- one foot can have many toes, each toe has one foot

When you are working with one to many relationships, there is going to be a foreign key in one of the tables. To know which of the tables gets the foreign key, figure out which of the tables is the one and which is the many. The foreign key goes in the many table.

# Many to Many _:_

- on band will play at many different venues, each venue will have many bands play
- one magazine will have many authors, each author can write for many magazines

With many to many tables we have to create an intermediary table that links the two tables together. In the intermediary table, each of the keys won't be unique but we expect to see unique combinations.

    Band table
    Venue table
    Table with Band Id and Venue Id //purpose of this table is to hold a relationship between farms and animals - primary key is (band_id, venue_id), sometimes called a join table or an intermediary table

# Foreign Keys in Knex

Code necessary to set up foreign key enforcement in knexfile.js:

    pool: {
        afterCreate: (conn, done) => {
            conn.run('PRAGMA foreign_keys = ON', done)
        },
    },

We need to understand our one to many tables before we create the tables. The one has to be created before the many since the many will reference the one.

You can chain .createTable to the end of another table. You especially want to do this when creating foreign keys.

    exports.up = function(knex) {
        return knex.schema.createTable('farms', tbl => {
            tbl.increments();
            tbl.string('farm_name', 128).notNullable();
        })
        .createTable('ranchers', tbl => {
            tbl.unsigned()
            .notNullable()
            .references('id')
            .inTable('farms')
        })
    }

    exports.down = function(knex) {
        return knex.schema.dropTableIfExists('ranchers')
            .dropTableIfExists('ranchers')
    };

Order matters in the down function, think about shoes and socks. First you put your socks on, then you put your shoes on. When taking them off, you have to take your shoes off first, then you can take your socks off. Drop tables in the opposite order that you created them.

# 00-cleanup.js

    exports.seed = async (knex) => {
        await knex('zoo_animals').truncate()
        await knex('animals').truncate()
        await knex('species').truncate()
        await knex('zoos').truncate()
    }

The above code can replace the cleaner library. the library sometimes does not respect the order and can cause problems.

## Requirements

A client has hired you to build an API for managing `zoos` and the `animals` kept at each `zoo`. The API will be used for `zoos` in the _United States of America_, no need to worry about addresses in other countries.

For the `zoos` the client wants to record:

- name.
- address.

For the `animals` the client wants to record:

- name.
- species.
- list of all the zoos where they have resided.

Determine the database tables necessary to track this information.
Label any relationships between table.

We need 3 tables ( tables = entities, properties = columns or fields, relationships = foreign keys )

## zoos

id
name
address

## animals

id
name
species_id

## species

id
name

## animals zoos

zoo_id
animal_id
from_date
to_date
