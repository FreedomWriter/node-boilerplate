# The AND, OR, and NOT keywords

Get all female customers who live in Oregon or New York:

## AND

Using `AND` means that both conditions must be met, in order for a row to be included in the returned data.

So given:

```sql
select count(customerid)
from customers
where gender = 'F'
AND state = 'OR'
```

We will get a count of all of the customerid's in the customer table where the gender is 'F' and the state is 'OR'. If a customer is male, or lives in a state beides Oregon, their record will not be included in our total count.

## OR

Using `OR` allows us to select data based on whetherthe first condition is met or the second condition is met.

So given:

```sql
select count(customerid)
from customers
where state = 'OR'
OR state = 'NY'
```

We will get a count of all customers who live in either the state of Oregon or New York

## Combining AND and OR

We can use these two filters together. If we wanted a count of all the customers who were female and lived in either Oregon or New York, our query would look like this:

```sql
select count(customerid)
from customers
where gender = 'F'
-- note the parenthesis that wraps the OR statements
AND (state = 'OR'
or state = 'NY')
```

## NOT

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
