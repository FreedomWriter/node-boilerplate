# Order of operations for a query

Before diving into operator precedence, let's quickly think about the order a query is executed.

FROM -- Which table are we dealing with?<br>
WHERE -- What conditions should be met<br>
SELECT -- What fields should be returned?<br>

## Operator Precedence

A statement that has muliple operators, is evaluated based on the priority of the operators. That priority at a high level looks like this, starting from the first thing to be evaluated (`highest priority`) to the last thing to be evalutate (`lowest priority`)

- Parentheses
- Multiplication/Division
- Subraction/Addition
- NOT
- AND
- OR

If operators have `equal priority`, (suppose there are multiple AND conditions) the operators will be evaluated directionally, from left to right or right to left. There are specific rules dictating when the direction will be from right to left or left to right.

The Postgres [docs]("https://www.postgresql.org/docs/7.2/sql-precedence.html") have a table that lists the associativity, which is another way of saying the direction it will be read in.

AND and OR both have left associativity. This means that the following:

```sql
SELECT state, gender FROM customers
WHERE gender = 'F'
AND state = 'OR'
OR state = 'NY'
```

would not return the results we might expect it do which is a list of states and gender from all of the females living in New York or Oregon,

What we have actually asked for is state and gender from customers who are female from Oregon, or from New York (regardless of gender).

There are a couple of ways to fix this. We could add parenthesis:

```sql
SELECT state, gender FROM customers
WHERE gender = 'F'
AND (state = 'OR'
OR state = 'NY')
```

Or we can be more verbose:

```sql
SELECT state, gender FROM customers
WHERE gender = 'F'
AND state = 'OR'
OR state = 'NY'
AND gender = 'F'
```

However the more verbose method is ineficient in comparison to using parenthesis. The verbose method will first look for all the females who live in Oregon. Then it would look for all of the females who live in New York, but it would look at the females who live in Oregon again, just to make sure they arent' New Yorkers, and of course, we would look at both the male and female records again.

When we use the parenthesis, we are first selecting only the records from Oregon or New York, and then looking for females from that subset.

Another example:

```sql
SELECT state, gender, salary, state
FROM customers
WHERE age > 20
AND salary > 1000
AND gender = 'F'
AND NOT state = 'NY'
```

1. NOT has the highest priority, so `AND NOT state = 'NY"` is going to be the first thing executed. NOT reads to the right of itself

   AND reads to the left of itself so it will "chain up"

2. `age > 20`
3. `salary > 1000`
4. `gender = 'F'

One more:

```sql
SELECT state, gender, salary, state
FROM customers
WHERE age > 20
OR age < 30
AND salary > 1000
AND gender = 'F'
AND NOT state = 'NY'
AND NOT state = 'OR'
```

We will have 2 filters happening:

Filter 1:

1. Younger than 30
2. Not from New York
3. Not from Oregon
4. Salary greater than 100

Filter 2:

1. Older than 20

This would not give us a very useful data set. We would have nicely filtered data from the first filter, but the second filter would essentially undo all of that by dumping anyone older than 20 into our results

A more complex example:

```sql
SELECT state, gender, salary, state
FROM customers
WHERE
(
    salary > 10000 AND state = 'NY'
    OR (
        (age > 20 AND age < 30)
        AND salary <= 20000
    )
)
AND gender = 'F'
```

This example says, give me anyone lives in New York and make more than 10000 or are older than 20 and younger then 30 and make more than or equal to 20000, once those conditions are met, give me the results that are female.
