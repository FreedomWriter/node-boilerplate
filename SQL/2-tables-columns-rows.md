# Tables, columns, and rows

## What is a table?

- A table is a represntation of an object or resource.

- Each table has a name that relates to the data being stored.

- Each table has columns and rows.

- These rows and columns form tables much in the way that spreadsheets have columns and rows that belong to them. In a relational model tables are more nuanced than a spreadsheet, but it can be helpful to think of them this way.

## What are columns aka Attributes?

- Each column represents a specific type of data.

- Each table should have an id column.

- Each column has a header that describes the data it is concerned with.

- Each column is unique.

- A collection of columns is called the `Degree` of the relation.

- Given a table with the columns: id, lastName, firstName, dob we could say "The degree of the relation is id, firstName, lastName, dob"

- When we talk about what a column can store, we call that the `domain` or the `constraint`. So we can say in dob, only store dates - that is the contraint/domain.

- We might say my table has < attributes > with < constraints >.

## What is a row aka tuple?

- Rows represent an entry in a table.

- When we start inserting data, we are creating rows.

- Rows will include entries for columns that belong to a single entry.

- Each row must follow the column contraints

- When we talk about all of the rows (data) in a table, we call that the cardinality.
