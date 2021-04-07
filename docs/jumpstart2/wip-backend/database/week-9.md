# Week 9: Advanced Database Topics    
In an increasingly data-driven world, efficiently configuring and managing a database are crucial
 to creating robust and scalable web applications. This week, we'll learn some tools and
  techniques to help us make the most of our Postgres database.

## Agenda
- Database Constraints
    - Primary Keys
    - Foreign Keys
    - Other Constraints
        - `UNIQUE` and `NOT NULL`
        - `CHECK`
- Advanced Queries
    - `DISTINCT`
    - `UNION`
    - Aggregate Functions
        - `COUNT`
        - `MIN`/`MAX`
        - `AVG`
        - `SUM`
        - `GROUP BY` & `HAVING`
    - `ORDER BY`
    - `CASE`
    - `LIKE`
    - `JOIN`
        - Different Types of Joins
    - `SUBQUERY`
    


In this lesson, we'll cover some more advanced database topics that will help you make the most out of your Postgres database by creating and enforcing predictable and reliable data structures.

## Database Constraints
Databases allow us to define constraints, which are just rules that we can ask our columns to
 follow. Now, we'll take a look at some of the most common constraints that you'll use when
  working with databases.

### Primary Keys
By this point, you've heard plenty about ids, and you've probably worked with them in some capacity. It's important that we take the time to understand exactly what an id is and why we use them.

Let's back up a little bit and begin with primary keys. As the name suggests, a table can only have one primary key. The primary key is the key which uniquely identifies each entry in the table. Tables do not need a primary key, nor does the primary key need to be a column called id. However, both are standard practice, and it's important to understand why.

It's straightforward to see why primary keys are so helpful. The most important benefit is that
 they allow us to quickly identify exactly one row in a table. Whether we are programming a React
  component on our client or business logic in our backend, ids provide a way of easily
   identifying entries in our database. Standard practice is to create a column `id` of type
    `SERIAL` to serve as our primary key. `id` is a short, descriptive and unambiguous name. The `SERIAL` type stores an integer, with the added benefit of auto increment: whenever we make a new entry, Postgres will automatically increment the id for us. This way, we don't need to worry about setting the id values ourselves. `PRIMARY KEY` is technically just a constraint (more on constraints later) that mandates that all values be `UNIQUE` and `NOT NULL`. Thus, we are guaranteed that each row has an id, and that no other row shares that id.

To designate a column as a primary key when we create a table, we use this syntax:

```sql
CREATE TABLE products (
    id serial PRIMARY KEY,
    name text,
    price numeric
);
```

### Foreign Keys
What gives databases their power and versatility is their ability to establish complex relationships between entries in different tables. For example, let's say we are building a simple e-commerce application that has a list of products and allows users to place orders of those products.

We start with our products table, which lists all of our products:
```sql
CREATE TABLE products (
    id serial PRIMARY KEY,
    name text,
    price numeric
);
```
Next, we want to create an orders table that lists all orders made in the application. However, before we define our table, there is an important observation that we can make. Every order must be <em>of</em> a particular product; in other words, every order must contain exactly one product. It doesn't make sense to have an order with no products, and we can't store multiple products in one row (since we'd need an arbitrary number of columns to store each product). So, we need a column in our orders table that references an entry in the products table. It makes sense, then, to use its id. That gives us this definition:

```sql
CREATE TABLE orders (
    id serial PRIMARY KEY,
    product_id integer
);
```
As you can see, every `order` has a `product_id`. But this makes no guarantee that this id actually points to a product. For example, say you had only two products, with ids of `1` and `2`, but you created an order with a `product_id` of `3`. To prevent unpredictable errors from happening, we might want to add a constraint that guarantees that `product_id` contains a valid product id. That's exactly what the `FOREIGN KEY` constraint does. To use it, we can slightly alter our table definition:
```sql
CREATE TABLE orders (
    id serial PRIMARY KEY,
    product_id integer REFERENCES products
);
```
`REFERENCES` tells Postgres to add a foreign key constraint, and the table name (`products`, in this case) immediately follows, telling Postgres that the `product_id` column refers to a a primary key in the `products` table. Now, if we try to create an order with an invalid `product_id`, we'll get an error. Similarly, if we try to delete a product that is referenced to by an order, we will also get an error.

With this foreign key in place, we can now make an order for any product, and keep order information separate from product info. But we'd like to take this one step further: a single order should be able to contain multiple products. As we briefly mentioned earlier, this is impossible with the current two-table setup, because our `orders` table would need to have an arbitrary number of `product_id`s. The solution is to create another table called a <strong>join</strong> table. It has one purpose: to <em>join</em>, or connect, two other tables, so that we can establish what we call a <em>many-to-many</em> relationship -- put simply, this means that an order can contain an arbitrary many products.

Our new table only needs two columns: an order id and a product id. Every entry in this table represents something like a single item in a shopping basket. That item belongs to a particular basket (the order), and points to a product. So, we might create a table like this:
```sql
CREATE TABLE order_items (
    product_id integer REFERENCES products,
    order_id integer REFERENCES orders
);
```
This is an example of a table that needs no primary key. We might add one if we want, but it may not be useful because the only purpose this table serves is to join two existing tables. If we need to refer to a specific entry in this table, we can use the combination of `order_id` and `product_id`, since no two rows should have the same value for both. We'll see how to do this in the next section.

### Other Constraints

#### `UNIQUE` & `NOT NULL`
As we mentioned earlier, `PRIMARY KEY` is technically just a combination of the `UNIQUE` and `NOT
 NULL` constraints. These constraints are self-explanatory; `UNIQUE` requires that a column's
  value is unique with respect to all other rows in the table, and `NOT NULL` requires that a
   column's value is not `NULL` (like in Javascript, `NULL` just represents the absence of a value
   ). In general, most columns should be `NOT NULL`. Only allow columns be `NULL` if it makes sense for there to be no data.

> Note: when we say "require" in terms of constraints, we mean that any attempt to create,
 update, or delete an entry in the table that will violate the constraint will result in an error
>. This enables us to assume that all entries in a table satisfy the table's constraints.

Here is an example of how we might define these constraints:
```sql
CREATE TABLE products (
    id serial UNIQUE NOT NULL,
    name text NOT NULL,
    price numeric
);
```
What if no columns in a table are unique by themselves, but the combination of two or more is unique? For example, recall `order_items`, our join table from earlier:
```sql
CREATE TABLE order_items (
    product_id integer REFERENCES products,
    order_id integer REFERENCES orders
);
```
We can't make `product_id` unique because we may have multiple order items that refer to the same product. Similarly, we can't make `order_id` unique because the entire point of creating this join table was to allow multiple entries to belong to the same order. However, we may want to guarantee that there are no duplicate entries -- i.e., the <em>combination</em> of the product id and order id is unique. More formally, no entry in the table contains both the same product id and the same order id.

Fortunately, Postgres allows us to define a `UNIQUE` constraint over multiple columns:
```sql
CREATE TABLE order_items (
    product_id integer REFERENCES products,
    order_id integer REFERENCES orders,
    UNIQUE (product_id, order_id)
);
```

#### `CHECK`
There is one final important constraint that we will cover today called `CHECK`. `CHECK` allows us to guarantee that some arbitrary expression is true for every entry in our table. For example, let's say we wanted to make sure that all of our product's prices are positive (greater than zero). We might use this definition:
```sql
CREATE TABLE products (
    id serial PRIMARY KEY,
    name text,
    price numeric CHECK (price > 0)
);
```
Here, whatever follows `CHECK` is an expression that Postgres will automatically check any time we create or update an entry in our table. This again allows us to assume that every entry in the table satisfies this constraint, so we can be absolutely sure that every product has a positive price.

We also might define a constraint that involves two columns. For example, say that we also had a `discounted_price` column, which we want to constrain to be strictly less than the `price`. We can define our updated table like this:
```sql
CREATE TABLE products (
    id serial PRIMARY KEY,
    name text,
    price numeric CHECK (price > 0),
    discounted_price numeric CHECK (discounted_price > 0),
    CHECK (price > discounted_price)
);
```
Note that the final `CHECK` is on its own row, separate from any column. This is because it is a <em>table</em> constraint rather than a <em>column</em> constraint. Table constraints are defined with respect to the entire table, whereas column constraints are only defined with regards to the column whose line they are defined on. Any column constraint can be written as a table constraint, but the reverse is not true. (Column constraints cannot reference other columns.)

## Advanced Queries
Last week, we learned how to construct basic SELECT queries that can read rows and columns from
 our database. In the real world, though, data is much more complex than just a list of rows and
  columns. Now, we'll learn some advanced querying techniques that will help us get exactly the
   data we want using only SQL.

### Distinct
Normally, we can use the `SELECT` keyword to select all relevant rows from a table. But what if
 we want to only select distinct rows? That is, we want to select all relevant rows that are not
  identical (by the given criteria).
  
For example, let's say we wanted a list of all distinct product names from our `products` table
 defined above. Rather than query all products and remove duplicates, we can use SQL to query
  only distinct values:
  
```sql
SELECT DISTINCT name FROM products
```

We can also apply `DISTINCT` to multiple columns. This works how you would think: 

```sql
SELECT DISTINCT name, price FROM products
```

selects all rows whose combination of name and price is unique. In other words, the resulting
 table will have two columns, `name` and `price`, and no two rows will have the same value for both.

### Union
Some of you may already be familiar with the mathematical notion of a union -- the combination of
 two sets. Similarly, we can use SQL's `UNION` keyword to combine the results of two queries
 . Note that the queries must reference the same columns: a union of two sets of rows with different
  columns doesn't make sense.
  
Like union in math, `UNION` does not include any duplicates (unless you use `UNION ALL`).

Here's an example of a union query:

```sql
SELECT column1, column2 FROM table WHERE column1 > 50
UNION
SELECT column1, column2 FROM table WHERE column1 < 5
```

### Aggregate Functions
If you've used Excel before, you probably appreciate the statistics operations that allow you to
 quickly get info about data entries, like average() and max(). As you might expect, Postgres
  includes this functionality out of the box.

#### Count
For example, let's say we want to know how many entries are in a table. This is trivial:

```sql
SELECT COUNT(*) FROM table
```

The asterisk tells Postgres to count all rows, including `NULL`s and duplicates. We can also add
 `WHERE` clauses to count only entries that satisfy some condition, like this:

```sql
SELECT COUNT(*) FROM table WHERE column3 = false
```

This query returns a table with only a single column `count` and one entry containing the count.

#### Min/Max
To get the minimum or maximum values of a numeric column in a table, we can use the `MIN()` and `MAX
()` functions, like this:

```sql
SELECT MIN(price) FROM products;
```

Like `COUNT`, this returns a table with a single column `min` and one entry containing the
 minimum value of the queried column.
 
#### Avg
Average returns the average of a numeric column:

```sql
SELECT AVG(price) FROM products
```

#### Sum
Sum returns the sum of a numeric column:

```sql
SELECT SUM(price) FROM products
```

### Group By & Having
Let's say we had a `payments` table defined like this:

```sql
CREATE TABLE payments (
    id serial PRIMARY KEY,
    customer_id integer,
    order_id integer,
    amount integer
)
```

What if we want to know how much each customer has paid over all their orders? We can use SQL's
 `GROUP BY` clause to group payments by their `customer_id`, and then use `SUM()` to compute the
  sum.
  
```sql
SELECT customer_id, SUM(amount) FROM payments GROUP BY customer_id
```

Now, imagine we only cared about our most loyal customers -- the ones who've spent more than $200
. You might think that we could use a `WHERE` clause to only get the groups of payments that sum
 to more than 200. But `WHERE` only works for rows, not groups in the result. Instead, we use the `HAVING
 ` keyword, like this:
 
```sql
SELECT customer_id, SUM(amount) FROM payments GROUP BY customer_id HAVING SUM(amount) > 200
```

### Order By
Often, we'll want to sort the data that we receive from our database. We could do this in our
 application logic, but it's almost always faster to ask the database to do it directly for us
 . Fortunately, SQL makes this trivially easy for us.
 
For example, let's say we wanted to get a list of products sorted from cheapest to most expensive
. This simple query does that for us:

```sql
SELECT * FROM products ORDER BY price ASC
```

Of course `ASC` indicates that the resulting rows will have an ascending price. `DESC` would be
 the opposite.
 
Sorting works on more than just numeric values. Ordering textual values in `ASC` order sorts by
 alphabetical order, for example.
 
You can also sort on multiple columns as a tie-breaker by separating each term with a comma:

```sql
SELECT * FROM products ORDER BY column1 ASC, column2 DESC, column3 DESC;
```

### Case
The `CASE` statement, similar to if-then-else statement or a switch statement, goes through
 different conditions and returns a value when the first condition is met. If the first condition
  is true, a value will be returned and , if not, the rest of the conditions will be evaluated
  . In case non of the given conditions are true, it returns the value in the `ELSE` clause (just like the `default` case in switch statements). Don't forget to tell the program that your `CASE` statement is done by an `END` clause. Here is what the syntax looks like:

```sql
SELECT ...
...
CASE
    WHEN condition1 THEN result1
    WHEN condition2 THEN result2
    ...
    WHEN conditionN THEN resultN
    ELSE result
END; 
```

and here is an example with a `SELECT` statement:

```sql
SELECT order_id, amount,
CASE
    WHEN amount > 10 THEN 'Payment due is more that $10'
    WHEN amount < 10 THEN 'Payment due is not much!'
    ELSE 'You gotta pay exactly $10'
END AS PaymentDueFeedback
FROM payments; 
```
> Note: `AS` is used for renaming a column in the table that we want to be displayed after running
 a query.*


### Like
This operation is used with the `WHERE` clause. `LIKE` allows you to find a specific pattern in a column of a table. You can use `%` and `_` in conjuction with `LIKE` if there are other characters before or after the wanted pattern. The percent sign and underscore can be used in combinations as well. 
- `%` represents zero, one, or multiple characters.
- `_` represents a single character.

Imagine for some reason you wanted to find all products that have <em>s</em> as their second letter; you can perform the following query:
```sql
SELECT * FROM products
WHERE name LIKE '_s%';
```
You can also have as many conditions as you'd like by using `AND` or `OR` operators. So now imagine you wanted every product in your database whose name either started or ended with s:
```sql
SELECT * FROM products
WHERE name LIKE 's%' OR '%s';
```

### Join
So far, we've seen how to design tables to help us store related data. For example, we created an e-commerce database with three tables: `orders`, `products`, and `order_items`. Every row in `orders` represents one shopping basket. Every row in `order_items` belongs to one `order` and refers to a `products`, so we can have as many products as we want in one shopping basket.

But storing our data is only one part of our application. Equally important is accessing it. Let's say we want to list all the products in an order. The first step is to obtain all the
 `product_id`s associated with that order. So, we might run:
 
```sql
SELECT * FROM order_items WHERE order_id = 20
```

to find all the order_items associated with order 20. Then, for each order item, we can find its product by running separate queries:
 
```sql
SELECT * FROM products WHERE id = ORDER_ITEM_ID
```

but this is verbose. Instead, we can use the `JOIN` keyword, which allows us to join two tables
 based on a matching column, like an id. So, we could write:
 
```sql
SELECT p.id AS Product_ID, p.name AS Product_Name, o.id AS Order_ID
    -- the trailing 'o' below lets you set a table alias, so you can refer to it in other parts of the query
    FROM order_items o 
    JOIN products p
    ON Product_ID = o.product_id;
```

This table will give us 3 colums representing the Product id in our order, the name of the product, and finally our order id in case we wanted to double check that we've got the right products. The rows will contain the list of products in our order.

#### Different types of joins

Based on what we want to get from two tables, we can do different types of joins. As you can see in the picture below, some of these different joins are: `INNER JOIN` (or `JOIN` as default in SQL), `LEFT JOIN`, `RIGHT JOIN`, and `FULL JOIN`. 

![SQL Join Comparison](./img/Joins-in-SQL-Inner-Outer-Left-and-Right-Join.jpg)

`INNER JOIN`: returns rows when there is a match in both tables (default if no qualifier is
 specified).

`LEFT JOIN`: returns all rows from the left table, even if there are no matches in the right table.

`RIGHT JOIN`: returns all rows from the right table, even if there are no matches in the left table.

`FULL JOIN`: It combines the results of both left and right outer joins.
The joined table will contain all records from both the tables and fill in NULLs for missing matches on either side.


### Subquery
Subquery's definition is within its name: a query within a query! Subqueries could be nested inside a `SELECT`, `INSERT`, `UPDATE`, `DELETE`, or another subquery. They can output a list of data or individual values. 

We use subqueries as a condition (therefore it's usually used within a `WHERE` clause) and will return data that will be used in the main query and the point of it is to restrict the information that's being retrieved from the database. Make sure you surround your subqueries with paranthesis.

>Note: Operators could be `<`, `=`, `>`, etc.

```sql
SELECT column1, column2, ...
FROM table
WHERE clause/expressions OPERATOR 
    (SELECT column
    FROM table
    WHERE condition);
```

Here's an example:
```sql
SELECT * FROM products
WHERE id IN (SELECT product_id AS id FROM order_items
            GROUP BY product_id
            HAVING COUNT(product_id) > 5) s;
```
