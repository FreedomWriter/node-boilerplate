# Comments

```sql
-- Comment all the things
-- We can use single line comments

/*
Multi line COMMENTS
are also a thing
*/

-- Don't forget the single quote must be used to get the space between the fields
select concat(first_name, ' ', last_name)
-- The alias for the concated fields needs to be before the FROM statement
as "Full Name"
from employees
Where first_name = 'Mayumi'
and last_name = 'Schueller' -- filter on Mayumi Schuller
```

Your code does NOT explain itself.
