# Column Concatenation

Sometimes, when we present our data, we want to combine multiple columns into a single column. This can be acheived using the `Concat function`.

NOTE: When we are refering text, we use single quotes, as we did in the concat function to include a space between the two columns. Double quotes are for selecting a column.

```sql
SELECT Concat(emp_no, ' ', title) AS "Employee Title"
FROM "public"."titles"
```

This will return a single column named `Employee Title` with the value of `10001 Senior Engineer` which is a combination of the emp_no: 10001 and title: Senior Engineer, with a single space between them.
