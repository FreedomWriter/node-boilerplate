# Aggregate functions

In database management an aggregate function is a function where the values of multiple rows are grouped together as input on certain criteria to form a single value of more significant meaning.

Some common examples:

- AVG() -- calculates the average of a set of values.
- COUNT() -- counts rows in a specified table or view.
- MIN() -- gets the minimum value in a set of values.
- MAX() -- gets the maximum value in a set of values.
- SUM() -- calculates the sum of values.

## Examples

## COUNT

```sql
SELECT count(emp_no)
from employees
```

In this case it didn't matter which field we selected to count, we just needed something that would be present in each row.

## MIN

```sql
SELECT min(emp_no)
from employees
```

This would give us the smallest employee number.

## MAX

```sql
SELECT max(emp_no)
from employees
```

This would give us the largest employee number.

## SUM

```sql
SELECT max(emp_no)
from employees
```

This would give us a sum of all of the employee numbers.

## AVERAGE

```sql
SELECT avg(emp_no)
from employees
```

This would give us an average of all of the employee numbers.
