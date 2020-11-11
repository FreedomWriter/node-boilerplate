# Comparison Operators

We have access to the same comparison operators we are familiar with:

- 1 < 2 // true
- 1 > 2 // false
- 1 <= 2 // true
- 1 >= 2 // false
- 1 = 2 // false \*note we have a single = to denote equality
- 1 != 2 // true

We also have another way to denote `not equal`:

- 1 <> 2 // true

These comparison operators are better used for numbers vs text. Text comparisons using these operators can result in some unexpected results.

For ex:

```sql
/*
Is `a` greater than `a`? -- no
Is 'b' greater than `c`? -- this is processed alphabetically and because b` comes before `c`, it is considered `greater than` in SQL
*/
'abc' > 'ace' -- true
```
