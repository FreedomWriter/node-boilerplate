# The AND, OR, and NOT keywords

Get all female customers who live in Oregon or New York:

```sql
select count(customerid)
from customers
where gender = 'F'
-- note the parenthesis that wraps the OR statements
AND (state = 'OR'
or state = 'NY')
```

We can also exlude data using NOT:

```sql
select count(customerid)
from customers
where NOt zip = 88654
AND gender = 'F'
AND (state = 'OR'
or state = 'NY')
```

## To note:

There is an order of operations
OR creates a new branch of the filter, the following query will not consider anything before it.
