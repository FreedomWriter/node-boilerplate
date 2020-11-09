# The AND and the OR keywords

Get all female customers who live in Oregon or New York:

```sql
select count(customerid)
from customers
where gender = 'F'
-- note the parenthesis that wraps the OR statements
AND (state = 'OR'
or state = 'NY')
```

## To note:
