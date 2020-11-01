# Renaming Columns

```sql
SELECT column as '<new name>
```

Simply by selecting the column name and declaring a new name enclosed in quotes, we are able to rename a column. This allows us to present our data in a more friendly, human readable form.

```sql
SELECT emp_no AS "Employe #", birth_date AS "Birthday", first_name AS "First name"
FROM employees
```

Old Column Name --> New Column Name

`emp_no` --> `Employee #`

`birth_date` --> `Birthday`

`first_name` --> `First name`

NOTE: This does not alter the tables, only the way the data is returned from the query.
