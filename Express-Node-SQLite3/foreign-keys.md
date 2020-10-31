# Foreign Keys

A foreign key is a column in a table that points to a Primary Key in another table.

- foreign key has to be the same data type as the primary key it is pointing to

## Querying muliple tables (joins)

# SQL (northwinds database)

    SELECT * FROM OrderDetail
    JOIN Product ON OrderDetail.ProductId = Product.Id

This is saying "Select everything from the OrderDetail Left Table and for each resulting row, find the row in the Product table matches this condition: Where the ProductId in the OrderDetail table matches the Id in the Product table.

To select multiple columns we would specify the table followed by a . and then the column name we are interested in, like this:

    SELECT OrderDetail.Id, Product.Id FROM OrderDetail
    JOIN Product ON OrderDetail.ProductId = Product.Id

This is getting messy, isn't it? To clean it up we can use an alias

    SELECT o.Id, Product.Id
    FROM OrderDetail AS o
    JOIN Product AS p
    ON o.ProductId = p.Id

Another Example where we find the employees first and last name associated with order number 16608 using a join.

    SELECT e.FirstName, e.LastName FROM "Order" AS o
    JOIN "Employee" AS e ON o.EmployeeId = e.id
    WHERE o.Id = 16608

We used "" around Order because order is a reserved keyword (non case sensitive) for SQL, used "" around Employee for consistency.

We could get the customer ContactName as well:

    SELECT e.FirstName, e.LastName, c.ContactName FROM "Order" AS o
    JOIN "Employee" AS e ON o.EmployeeId = e.id
    JOIN "Customer" AS c ON o.CustomerId = c.Id
    WHERE o.Id = 16608

What if Ann Doddsworth (the employee from the above query) gets fired and we delete her row from the employee table? Now our SQL query returns null.

There are multiple JOIN types in SQL. By default we are using what's known as an INNER JOIN. With INNER JOIN's the rule is if there isn't a match on both sides of the JOIN, nothing gets returned.

Other Join Types: http://www.sql-join.com/sql-join-types

INNER JOIN

- Select all records from Left Table and Right Table, where the join condition is met.

LEFT JOIN

- Select all records from Left Table, along with records from Right Table for which the join condition is met (if at all).

RIGHT JOIN

- Select all records from Right Table, along with records from Left Table for which the join condition is met (if at all).

FULL JOIN

- Select all records from Left Table and Right Table, regardless of whether the join condition is met or not.

So if Ann gets fired we would use a LEFT JOIN so that our query will return the data that is available

    SELECT o.Id, o.OrderDate, e.FirstName, e.LastName FROM "Order" AS o
    LEFT JOIN "Employee" AS e ON o.EmployeeId = e.id
    WHERE o.Id = 16608

The table that we define with JOIN (Employee) is the right side, the table that we specify with FROM is the left side

RIGHT JOIN and FULL JOIN are not supported in SQLite
The ON is a condition as to how rows match

## [Aggregate functions](https://www.sqlite.org/lang_aggfunc.html)

[Core functions](https://www.sqlite.org/lang_corefunc.html)

Examples of SUM in SQL

    SELECT SUM(Quantity) AS Total FROM OrderDetail

    SELECT ProductId, SUM(Quantity) AS Total FROM OrderDetail
    GROUP BY ProductId

Figure out how many products are in each category, count each one according to the CategoryId, return the Count and CategoryName:

    SELECT COUNT(Product.Id), Category.CategoryName FROM Product
    JOIN Category ON Category.Id = Product.CategoryId
    GROUP BY CategoryId

# JOIN in knex

INNER JOIN users AS u ON u.id = p.user_id
WHERE user_id = id
SELECT p.id, p.contents, u.username

    .join("table name we are joining", "1st part of conditional", "2nd part of conditional")
    .select("column to be returned", "column to be returned", "column to be returned")

    const { id } = req.params
    const posts = await db("posts as p")
    .join("users as u", "u.id", "p.user_id")
    .where({ user_id: id})
    .select("p.id", "p.contents", "u.username")

    res.json(posts)

# Separating queries from routes

    const postModel = require("./post-model");

    router.get("/", async (req, res, next) => {
    try {
        const { id } = req.params;
        const posts = await db("posts as p")
        .join("users as u", "u.id", "p.user_id")
        .where({ user_id: id })
        .select("p.id", "p.contents", "u.username");

        res.json(posts);
    } catch (err) {
        next(err);
    }
    });

Becomes:

    router.get("/", async (req, res, next) => {
    try {
        const { id } = req.params;
        const posts = await postModel.find(id);

        res.json(posts);
    } catch (err) {
        next(err);
    }
    });

The model looks like:

    const db = require("../data/db-config.js");

    function find(user_id) {
    return db("posts as p")
        .join("users as u", "u.id", "p.user_id")
        .where({ user_id })
        .select("p.id", "p.contents", "u.username");
    }

    module.exports = {
    find
    };
