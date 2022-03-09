# Creating a database

In the command line, get into postgres:
```node
    psql -U <user> <database>
```
-U indicates I'm going to give you a user

Create a database with default options:

```node
CREATE DATABASE <name>; -- semi colon not optional!
```
To specify different options, we use the `WITH` command:

```node
CREATE DATABASE <name>
    [ [ WITH ] [ OWNER [=] user_name -- default is the current user]
        [ TEMPLATE [=] template ] -- default is template1
        [ ENCODING [=] encoding ] -- default is UTF8
        [ LC_COLLATE [=] lc_collate ] 
        [ LC_CTYPE [=] lc_type ]
        [ TABLESPACE [=] tablespace ]
        [ CONNECTION LIMIT [=] connlimit] -- default is 100 ]
``` 