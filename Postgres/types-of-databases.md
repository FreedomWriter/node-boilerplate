# Types of Databases Postgress Creates

When you set up your postgres database, 3 databases were created:
1. postgres
   - The default database that is created when you setup postgres (initdb). The default user you get with a postgres installation is `postgres` which creates a database called postgres. Postgres will assume a connection to a database with the same name as the user if no database is supplied.
2. template0
   - This is the template that is used to create template1 - do NOT change it. It's a backup for template1.
3. template1
   - Used to create new databases. Any changes made to template1 will be applied to all new databases. When you run the create database command, it will use template1 unless you specify differently. Because template1 is the default template, if it is being accessed or connected to, no new databses can be created until the connection is closed.

Command to set up a database:
```node
    psql -U <user> <database>
```
-U indicates I'm going to give you a user
