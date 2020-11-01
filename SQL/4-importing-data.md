# Importing Data

In the command line:

```node
psql -h localhost -U postgres -d DatabaseName < filetoimport.sql
```

DatabaseName is to be replaced with the name of your database

filetoimport.sql should be replaced with the file you are looking to import
