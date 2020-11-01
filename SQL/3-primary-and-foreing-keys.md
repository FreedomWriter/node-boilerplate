# Primary and foreign keys

We need to be able to link our tables, we use primary and foreign keys

Each table should have an id column. The id needs to be unique. The `id` is the `Primary Key` that `uniquely` identifies each `row`.

Once we have a Primary key, we can reference data in a given table A to table B by using the Primary key of table A as a foreign key of table B.

This is done by injecting a new column into table B whose sole purpose is to manage the relationship between the two tables. The Primary Key from table a is now the foreign key in table B.
