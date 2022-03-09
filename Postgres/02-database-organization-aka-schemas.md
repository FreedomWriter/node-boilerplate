# Database Organization

Schemas allow us orgainze our tables, views, indexes, etc.

Each database gets a public schema by default.

Unless you specify a schema, the default is assumed to be public so:

```sql
SELECT * FROM employees
-- is the same as
SELECT * FROM public.employees
```

There is a command to show all schemas:
(when in postgres land in the terminal)
```node
postgres=# \dn;
// results in:

  List of schemas
  Name  |  Owner   
--------+----------
 public | postgres
(1 row)
```
where postgres is the database we are currently in.

Using schemas:
- allows many users to use one database without intefering with each other
- allows us to organize database ojects into logical groups to make them more manageable
- allows third party applications to be put into separate schemas so they do not collide with the name of other objects