# Week 7: Database Basics and Connecting the Backend to the Database

## Agenda
- Database basics
  - How to think in terms of dbs
  - How to think about tables
  - Data types
  - How to create a table/db
  - Basic query structure
- Backend + db connection
  - JDBC and making calls to the db
  - Prepared statements
  
## Basic Plan
1. Databases!
  - Excel Sheets to Database Tables
  - Data Types in SQL
    - `SERIAL`
    - `CHAR(x)`
    - `VARCHAR(x)`
      - `CHAR` vs `VARCHAR`
    - `TEXT`
      - `CHAR`/`VARCHAR` vs `TEXT`
    - `INTEGER`/`BIGINTEGER`
    - `DECIMAL`
    - `TIMESTAMP`
    - `BOOLEAN`/`TINYINTEGER`
    - `ENUM`
  - Column modifiers
    - `PRIMARY KEY`
    - `FOREIGN KEY`
    - `NOT NULL`
    - `AUTO INCREMENT` (`SERIAL`)
    - `DEFAULT`
  - Creating a Database and Table
  - Query structure
    - Comments
    - The `INSERT` statement
    - The `SELECT` statement
    - The `UPDATE` statement
    - The `DELETE` statement
2. Backend + DB Connection
  - JDBC and Working with the DB in Java
    - Properties and Environment Files
    - `Connection`s and the `DriverManager`
  - Prepared Statements
  
## Databases! 

Databases are persistent storage. That means that they continue to hold application data on disk, even if your app stops
running (whether intentionally or unintentionally). They have APIs, mostly through special software Open DataBase 
Connectivity (ODBC) and Java DataBase Connectivity (JDBC, what we'll be using). The type of database we're going to be 
working with is called a relational database, which means it acts like a collection of Excel sheets. It also has a nice
little syntax for performing operations called SQL (pronounced "see-quel" or "ess-que-ell"). There are also 
other types of DBs, called NoSQL databases, which act a little differently, but we'll just stick with relational in this
program.

### Excel Sheets to Database Tables

When you think of Excel, you think of a bunch of rows/entries and columns/fields which make up a sheet. In terms of DBs, 
each table is like a separate sheet containing pretty uniform data. Each column (the up/down ones) acts like a separate 
field. You specify a datatype and name for your columns, and then as data gets entered, rows (the long left/right) ones 
get created, read, updated, and deleted (remember CRUD). One thing to note is that in relational databases, unlike 
Excel, you don't have an unlimited amount of columns that can be automatically added. A table has a set structure once
you create it. This means you can only add/remove/update columns by changing the structure of the table. You can, 
however, add an unlimited number of rows in theory, though you'll run out of space and your queries will start to slow 
down after a while. 

Here's a reminder of what an Excel sheet can look like:

| These | are | your | columns | :) | 
| :---: | :---: | :---: | :---: | :---: |
| T | h | e | s | e |
| a | r | e | :) | :) |
| y | o | u | r | :) |
| r | o | w | s | :) |

### Data Types in SQL

In SQL, when making new columns on a table, altering columns, and dealing with table data, you need to be aware of the
data types of the variables you're working with. It's somewhat similar to working with types in Java, however, you don't
need to declare types all over the place. It matters most when you're trying to perform operations on types and getting 
the data using the JDBC/ODBC. For example, if you try to perform a query where you're adding two strings, you'll receive 
an error since strings can't be added. You can, however, concatenate two strings. 

There are many types available, especially in PostgreSQL, but below are a few of the most commonly used ones.

>Note: some of the data types we're introducing here may not be compatible with other databases. You'll have to 
>check the documentation when working with other databases if you're getting errors. You should also be able to look
>up equivalent data types between different database systems.

#### SERIAL

The `SERIAL` type is an `INTEGER` under the hood, usually used as an ID. It's a 
PostgreSQL specific data type, which is usually just an `INTEGER` with the `AUTO INCREMENT` modifier in most other 
databases (we'll be going over this in a sec). This is really useful for integer-typed
`PRIMARY KEYS` in other databases, since it automatically creates a new unique ID for you.

#### CHAR

The `CHAR(x)` type is actually a string of max length `x`. If a value you insert isn't the full length `x`, then the 
value will be right-padded with spaces. For example, if the column type is `CHAR(10)`, then "helloworld" would be saved as 
"helloworld", but "hey" would be saved as "hey____" (pretend the '_' are spaces).

#### VARCHAR

The `VARCHAR(x)` type is a variable-length string of max length `x`. If a value you insert isn't the full length `x`, 
then the value will _not_ be right-padded with spaces. For example, if the column type is `VARCHAR(5)`, then "hello" 
would be saved as "hello" and "hey" would be saved as "hey".


##### CHAR vs VARCHAR

If we were to save the string "Hey there!" to a `CHAR(15)` and a `VARCHAR(15)`, what do you think they'll output? If 
your guess was "Hey there!_____" (same as before, pretend the '_' are spaces) for the `CHAR` and "Hey There!" for the `VARCHAR`, you were right!
The difference between `CHAR(x)` and `VARCHAR(x)` is that a `CHAR` will right-pad the entry with spaces if it isn't of 
length `x`, but a `VARCHAR` would save the entered value. If either columns attempt to save a value that is longer than
`x` characters, then the value will be truncated and an error or warning might be thrown.

#### TEXT

The `TEXT` type is a string of unlimited length. The text will not be truncated.

##### VARCHAR/CHAR vs TEXT

The difference between `CHAR(x)`/`VARCHAR(x)` and `TEXT` is that a `CHAR`/`VARCHAR` is limited by the length of `x`. If
an entry is made with length greater than `x`, the value will be truncated and an error or warning might be thrown. 
`TEXT` will not be truncated. It's generally more efficient to store and perform operations on a `CHAR` or `VARCHAR` 
so if possible, try to use those. Columns like usernames, first/last names, emails, website URLs, and others, whose 
length generally won't be longer than a certain length should be stored as that.

#### INTEGER/BIGINTEGER

The `INTEGER` or `INT` type is an integer with a range of [-2147483648, 2147483647]. The `BIGINTEGER`/`BIGINT`
is an integer value with an extended range of [-9223372036854775808, 9223372036854775807] (wow that was terrible to type
out). 

#### DECIMAL

The `DECIMAL` type is similar to a double/float in other programming languages. You can usually specify the precision
and scale of the column by writing it like this: `DECIMAL(19, 2)`. 

**Precision** defines the total count of significant digits in the number (total number of digits on *both sides* of the
decimal point).
**Scale** defines the count of digits to the right of the decimal.

The range of this data type is *up to 131072 digits before the decimal point; up to 16383 digits after the decimal 
point*.

#### TIMESTAMP

The `TIMESTAMP` type is one of the date/time types commonly seen in SQL databases. There's also a `TIMESTAMP` type with
a time zone, if that suits your needs better. The regular `TIMESTAMP` type looks like this `2020-09-17 04:05:06` (note 
that it always has 19 characters, the leading 0s are part of the format), and
the `TIMESTAMP` with a time zone looks like `2020-09-12 13:50:20.633239-07` (there are other ways of formatting this 
though). You can read more about date/time types on the [PostgreSQL 
documentation](https://www.postgresql.org/docs/9.1/datatype-datetime.html).

##### CURRENT_TIMESTAMP

The `CURRENT_TIMESTAMP` function returns the current timestamp. This makes things a lot easier for default values or
when you need the current time in SQL. You can try it out with `SELECT CURRENT_TIMESTAMP;`.

##### DATE

The `DATE` type is a `TIMESTAMP` with only the date component. The default format looks like this `2020-09-08`.

##### TIME

The `TIME` type is a `TIMESTAMP` with only the time component. The default format looks like these without time zone
`04:05:06.789`, `04:05:06`, or `04:05`, and these with time zone `04:05:06.789-08`, `04:05:06-08`, or `04:05-08`

#### BOOLEAN/TINYINTEGER

The `BOOLEAN` is a type which is available in PostgreSQL. In many other DBs, `TINYINTEGER` or `TINYINT` is commonly used
instead (they are the same thing). The possible negative values are `false` and `0`, and the possible positive values 
are `true`, and `1`.

#### ENUM

The `ENUM` type allows you to specify a small set of possible values for a column. In PostgreSQL, enums are created as
a new datatype and then used in tables afterward. In other DB management systems, enums are usually just written as 
you're declaring a column type.

Here's an example of creating an enum type, which is shown used in a table later on:
```sql
CREATE TYPE personality AS ENUM ('nice', 'mean', 'neither');
```

### Column Modifiers

Column modifiers are special modifiers you add when declaring a column in a table which give it extra properties. Here
are a few of the common ones (some that you may need soon too!).

#### PRIMARY KEY

The `PRIMARY KEY` modifier declares a column or combination of columns as the ID for a given row. The database also uses
a `PRIMARY KEY` to speed up some search functions that use those fields (see 
[Indexes](https://www.postgresql.org/docs/9.1/indexes.html) if you're interested ). If used, all primary 
keys must be unique on the given table. This column is also not nullable.

#### FOREIGN KEY

The `FOREIGN KEY` modifier is very similar to a `PRIMARY KEY` in that it defines a unique way to identify records on a 
table. The main difference, though, is that a foreign key creates a link between a given table's columns and another
table's `PRIMARY KEY`. We'll talk about this more in the next lesson.

#### NOT NULL

The `NOT NULL` modifier lets the database know that a column should never accept `null` values. You should try to use this
where you can, since it can help with preventing `null`s from being entered where you're not expecting them.

#### AUTO INCREMENT

The `AUTO INCREMENT` modifier lets the database that it should automatically enter this column as the default value
(last value + 1) if the value isn't provided when entering data into the table. This is really useful for integer-typed
`PRIMARY KEYS` in other databases, since it automatically creates a new unique ID for you.

>Note: on PostgreSQL, this is actually 
>the data type `SERIAL` instead, and the `AUTO INCREMENT` modifier isn't available.

#### DEFAULT

The `DEFAULT` modifier lets you specify a default value for this column if nothing is entered. For example, saying 
`DEFAULT 0` will insert 0 if no value is provided for this column on insert. 

### Creating a Database Table

To create a database, all you have to do is run one of the following commands.

```sql
CREATE DATABASE your_database_name;

-- Do it, but perform a check to make sure it doesn't exist first. If it does, it will throw an error.
CREATE DATABASE IF NOT EXISTS your_database_name;
```

To delete a database, the command is very similar.

```sql
DROP DATABASE your_database_name;

-- Or if you want to perform the check too.
DROP DATABASE IF EXISTS your_database_name;
```

>Note: if you're going to be deleting a database, you should be __*REALLY SURE*__ you want to actually do that. If 
>you're developing locally (especially on this project), you're probably fine, but keep that in mind.

Creating a table follows a simple formula. Declare the table and add all columns by stating the column name, data type,
and any modifiers if applicable. If you have any constraints (we'll cover that in a later lesson), then those get added
at the bottom. 

>Note: the spacing we use where each statement is lined up is used for readability, you don't have to do 
>it if you don't want to. 

>Note: traditionally, SQL syntax is written in all caps, while user defined things like tables,
>columns, and functions are named using lower-case characters.

>Note: You may run into issues where the DB manager doesn't know specifically which database you're talking about if you 
>have multiple. You may need to prefix the table with the database name. Instead of writing `your_table_name`, you'll 
>want to write `your_db_name.your_table_name`.

Example of a table:
```sql
CREATE TABLE people (
-- Declare an ID as a serial with a primary key.
    id          SERIAL          PRIMARY KEY,
-- Create a name column with a max size of 50 characters which cannot be null (but it can be "").
    name        VARCHAR(50)     NOT NULL,
-- Create a bio column which allows an unlimited size body of text. This is nullable.
    bio         TEXT,
    age         INT             NOT NULL,
-- Create a timestamp which is automatically set using the built-in function "CURRENT_TIMESTAMP"
    signup_date TIMESTAMP       DEFAULT CURRENT_TIMESTAMP,
-- An example of how an enum is used (see the ENUM section above).
    personality PERSONALITY
-- Notice how this is closed with a semicolon. If this is run in a script with following statements, it will fail 
-- without this.
);
```

If you want to delete a table, you can use the following syntax.

```sql
DROP TABLE table_name;

-- Again, perform the check.
DROP TABLE IF EXISTS table_name;
```

Later on, we'll talk about migrations, but for now if you make any mistakes, just drop and recreate the table.

### Query Structure

Now that you know how to create a table, we'll go through how to CRUD data on that table. Similar to how there's a 
formula for creating a table, there are formulae for the other data manipulation tools.

#### Comments

We'll start off by showing comments, since we'll be using them in the script to describe what they're doing.
You should be able to write block comments the same way you do for Java and JavaScript, with the `/* */` style. If
you want to write a one-line comment, or if the block comment style isn't working, you can prefix the line with `--` 
(make sure you add a space after the `--` before your comment).

#### The INSERT Statement

The `INSERT` statement is the statement used for creating new data (**C**RUD). This is used whenever you want to enter 
new data into your table. It has this basic structure:

>Note: the indentation is also for readability. We're starting a 
>new statement normally, and then the rest of the lines for the same statement use a hanging indent.

Example of insert statements:
```sql
-- Tell the DB you're inserting data into the specific table.
INSERT INTO table_name VALUES 
-- Insert data into every column in the order the columns appear in the structure.
    (values, for, every, column, in, table, structrue, order), 
-- Add commas at the end of the groups to add more than one row. Otherwise just terminate the first one with a ";".
    (more, values, ...), 
    ...;

-- Another way of inserting into tables where you insert into only the columns you want (columns you leave out will just
-- get null or its default value) in the order you want.
INSERT INTO other_table_name (columns, you, want, to, supply) VALUES
    (columns, you, want, to supply),
-- The first one is what entering null looks like. The last one is what a timestamp looks like.
    (null, "hello", 5, "2020-08-26 10:20:00.918963-07");
```

An example using the `people` table:
```sql
-- Insert into our table from earlier.
INSERT INTO people VALUES
-- Usually we'd just want to let the DB manager enter the next SERIAL value for us.
    (0, "Jumpstart Team", "Hey, this is the Jumpstart team, and we're really excited you could join us!", 0, 
    CURRENT_TIMESTAMP, 'nice'),
-- We think you're nice, but we'll leave that up to you :)
    (1, "You", "Hey, put a description here!", 19, CURRENT_TIMESTAMP, null);

-- Insert values into only the name and age columns. The id and signup_date will be handled for us, and bio will just be
-- null.
INSERT INTO people (name, age) VALUES
    ("Your friend", 20);
```

>Remember, this is what the PERSONALITY column looks like
>CREATE TYPE personality AS ENUM ('nice', 'mean', 'neither');

#### The SELECT Statement

The `SELECT` statement is the statement used for reading data that already exists (C**R**UD). This is probably the most 
commonly used SQL statement, and we hope that you'll be pretty used to it by the end. There are many conditions and 
extra things we can add on, like grouping, aggregate functions, renaming columns, sub-queries, joins, row limiting, 
and others, but to keep it simple right now we'll just work with the structure below. You'll learn more about some
of those later on.

Example of select statements:
```sql
-- Select all columns for all rows on a table. The bare minimum you need when selecting (the columns -- "*" = all -- and
-- the table name).
SELECT * FROM table;

-- Select specific columns from a table with some condition(s).
SELECT column, names, you, want, some_date_column_too
    FROM table_name
-- Note how equality comparison is done with a single "=". Even for string types.
    WHERE names = 'someName'
-- Can do comparisons with numbers.
        AND column > 5
-- Can do comparisons with dates too! Also, you can group conditions together if you want to using parentheses.
        OR (some_date_column_too BEFORE CURRENT_TIMESTAMP
-- Assuming the 'you' column is a VARCHAR/CHAR/TEXT type, you can search for strings that start with 'hello' using the 
-- 'like' operator. This will find results like 'hello', 'hello ', and 'hello world'. The '%' is like a wildcard, it can
-- match any string when used with like.
            AND you LIKE "hello%")
-- Check for null/not null with 'IS NULL' and 'IS NOT NULL'.
        AND want IS NULL;
    
```

An example using the `people` table:
```sql
-- Select all fields from every record in people.
SELECT * FROM people;

-- Select all fields from every record in people who are greater than 5 (age is a column on the people table).
SELECT * FROM people 
    WHERE age > 5;

-- Select the name and id fields (in that order) from every record where the name is 'like' jump. 
-- Also, we don't like the name of the id column, so we're going to rename it to 'other_name'
SELECT name, id AS other_name FROM people 
    WHERE name LIKE 'jump%';
```
    
The above `SELECT` statements will probably look something like this:

>Result 1:
>All rows and columns on the table are returned.

| id | name | bio | age | signup_date | personality |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | "Jumpstart Team" | "Hey, this is the Jumpstart team, and we're really excited you could join us!" | 0 | 2020-09-12 14:08:00 | nice |
| 1 | "You" | "Hey, put a description here!" | 19 | 2020-09-12 15:00:00 | null |
| 2 | "Your friend | null | 20 | 2020-09-13 14:08:00 | null |

>Result 2:
>It only selects you and your friend since we're not even a year old yet!

| id | name | bio | age | signup_date | personality |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | "You" | "Hey, put a description here!" | 19 | 2020-09-12 15:00:00 | null |
| 2 | "Your friend | null | 20 | 2020-09-13 14:08:00 | null |

>Result 3:
>It only returns Jumpstart, since it's the only record with "jump..." as part of its name. Also, since we only selected
>two columns, those are the only to show up.

| name | other_name |
| :--- | :--- |
| "Jumpstart Team" | 0 |

#### The UPDATE Statement

The `UPDATE` statement is the statement used for altering preexisting data (CR**U**D). The structure is somewhat 
similar to the previous statements, so we'll just show you an example of how it works.

Example of update statements:
```sql
-- Update columns on the given table name
UPDATE table_name
-- Set the column 'column_name' to be some calculation or data. The "=" sign can be used as both assignment and 
-- comparison.
    SET column_name = calculation_or_data;

UPDATE table_name
-- Set columns 1 and 2 to the numbers 1 and 2.
    SET column1_name = 1, 
        column2_name = 2,
-- Set column 3 to the sum of 1 and 2 (yes, you can use the values of columns on the current table to set values -- it 
-- uses the value before updating column1_name and column2_name though).
        column3_name = column1_name + column2_name;

UPDATE table_name 
    SET value = "Hello Jumpstarters!"
-- A query like the ones above but it only activates on a row with a certain condition.
    WHERE condition;
```

An example using the `people` table:
```sql
-- Set all signup_date values to now.
UPDATE people
    SET signup_date = CURRENT_TIMESTAMP;

-- Set all ages to the user's id.
UPDATE people
    SET age = id;

-- Set all bio values to be the person's name and set the names to be "Jumpstarter".
UPDATE people
    SET bio = name,
        name = "Jumpstarter";
```

#### The DELETE Statement

The `DELETE` statement is the statement used for deleting existing data (CRU**D**). Again, the structure is similar
to the previous statements. 

>Note: if you want to delete everything from the table without having to drop and recreate 
>the table, you can use the `TRUNCATE table_name;` statement instead. 

Example of delete statements:
```sql
-- Delete statement.
DELETE FROM table_name
    WHERE condition;

-- Truncate statement. This deletes ALL data in a table.
TRUNCATE table_name;
```

An example using the `people` table:
```sql
DELETE FROM people
    WHERE name = "your friend";

TRUNCATE people;
```

## Backend + DB Connection

Now to the fun part. We'll be connecting your backend application to the database so that you can stop storing 
information in memory!

### JDBC and Working with the DB in Java

Again, the ODBC is software that allows an application to connect with a database. The JDBC is a Java version of that 
written in Java. To actually start making queries in Java, you'll need to tell the JDBC how to connect with your 
database.

#### Properties and Environment Files

Most of the time, when working locally, you and other coworkers/teammates/classmates will have different 
usernames/passwords/urls/other properties which are extremely unlikely to be the same. You also wouldn't want these 
values to be seen publicly on a Github repository or anything, because then that means anyone with access to the 
repository will have access to your sensitive information. To deal with this, developers create 
property files and environment files with that data, which are excluded from git in the .gitignore file. Since we'll now
be connecting to the database, it's about time we start storing some of our data in there. We'll be including a 
db.properties.example file (not excluded from git in the .gitignore), which will have dummy variables that you'll
have to fill in when you copy it. 

>Note: If you think of any other properties, feel free to add them here if they're related
>to the database, or you can create a new properties file and example file (with dummy variables). Make sure you don't
>add your properties file to the .gitignore

Our db.properties file:
```properties
# This is our properties example file. We have this here so that YOU CAN COPY IT and fill in
# properties you don't want to actually commit to the repo. So the program will end up reading the
# copied and filled in db.properties file, but you'll get the basic structure for that from
# db.properties.example. We also don't want to hard-code these values in the program, since it may
# be different on other systems, and it's a lot simpler to keep changeable values like these
# in one place.

# This is the database url. If you named your database something other than 'jumpstart', replace
# that in the db.properties file.
database.url = jdbc:postgresql://localhost:5432/jumpstart?autoreconnect=true

# Unless you created a new database user, this will probably stay the same.
database.username = postgres

# We don't know what your password is, so fill it in here. Also, if you're getting an authentication
# error, watch for trailing whitespace in the password here.
database.password = password
```

>Note: yes, my database password is actually `password` (I'm not too worried about anyone stealing my data here), but 
>yours **should not** be! >:(

To load these properties, we made a helper class that looks like this. Loading files in Java relative to the start path
is a little weird, so it will be provided to you.

Our `PropertiesLoader`:
```java 
/**
 * This class will load properties files for you. If you have more properties you want to add, feel
 * free.
 */
public class PropertiesLoader {
  // Our properties directory base path.
  private static final String basePath = "properties/";

  /**
   * Return a {@link Properties} object from the given file in the
   * service/src/main/resources/properties/ directory
   *
   * @param fileName The name of the file (include the .properties suffix).
   * @return The loaded properties.
   */
  private static Properties getProperties(String fileName) {
    // Get the full path (from the root of service/src/main/) to the file.
    String path = basePath + fileName;

    try (InputStream input = PropertiesLoader.class.getClassLoader().getResourceAsStream(path)) {
      Properties prop = new Properties();
      prop.load(input);
      return prop;
    } catch (IOException ex) {
      throw new IllegalArgumentException("Cannot find file: " + path, ex);
    }
  }

  /** Get properties for the database from the db.properties file. */
  public static Properties getDbProperties() {
    return getProperties("db.properties");
  }
}
```

And you'll be able to load properties in the file by doing the following:
```java 
// Get a Properties object containing the values in db.properties.
Properties dbProps = PropertiesLoader.getDbProperties();

// Then you can get certain values like this.
String dbUrl = dbProps.getProperty("database.url");
```

#### Connections and the DriverManager

The way that you actually interact through the database is with a `Connection` object from Java's `sql` package. To get
one of these, you can use the `DriverManager`, which will help you set up your database JDBC driver. 

Example of using the `DriverManager`:
```java 
Connection conn = DriverManager.getConnection(url, username, password);
```

The `Connection` object handles interactions with the database through Java. 

>Note: the `Connection` object must be closed when you're done doing your work. If left open for too long, they may 
>begin to timeout or you may end up opening too many connections for the database to handle. Once you're done with 
>whatever operations you need to perform, make sure you call `close()` on your connection (and any `ResultSets` and 
>`Statements` if you have any of those open).

### Prepared Statements

The way we'll be writing queries in Java is to replace all 'variables' with question marks (`?`), because we'll be using
prepared statements. Prepared statements are special ways to write queries to tell the `Connection` object 
you have open what query you want to make, and then you provide it with all of the variables separately later on. We 
want to do this because it's simpler in that it's simpler to not always be looking to escape your 
strings/query params/variables all the time, and it's safer to do (we'll explain why later on). 

Writing a prepared statement using the person table from before looks like this:
```java 
try {
    // This is the query you want to run. Notice how we replaced the id and age we want to search for with '?'. We'll be 
    // able to substitute that out later with variables.
    String sql = "SELECT name, age, bio FROM person WHERE id = ? AND name LIKE ?;";
    
    // Get our Connection from the DriverManager.
    Connection conn = DriverManager.getConnection(url, username, password);
    // Prepare the statement with the Connection.
    PreparedStatement stmt = conn.prepareStatement(sql);
    
    // Set the '?' with values using the provided PreparedStatement. The 1 stands for the '?' to replace. It's not 
    // 0-indexed. So the first '?' is 1, the second is 2, and so on...
    // The 0 stands for the value we want to provide. So this first setInt is setting the first '?' to be 0.
    stmt.setInt(1, 0);
    // Set the second '?' to select anything with the letter 'a' in it.
    stmt.setString(2, "%a%");
    
    // Execute the query and get the ResultSet. There's also the execute() method, which just returns a boolean 
    // indicating success or failure.
    ResultSet res = stmt.executeQuery();
    // The ResultSet doesn't really let you know how many values were returned, it just lets you know if there's a next 
    // value when calling next() (true means a next value was loaded). Initially, no values are loaded, so you need to 
    // call next() to get started. Since we don't know how many values there are, and unless you're expecting excactly 
    // one back, looping through them using while is a good way to do it. If you're expecting exactly one or zero, then 
    // you can just use if/else statements.
    while (res.next()) {
        System.out.println("----- Start result: -----");
        // You can either get the column by position in the select statement or by name.
        System.out.println(res.getString("name"));
        System.out.println(res.getInt("age"));
        // Get the bio, which is the third selected column.
        System.out.println(res.getString(3));
    }   
    
    // When you're done with getting values from this result, don't forget to close your ResultSet.
    res.close();
    // Close this PreparedStatement too.
    stmt.close();
    // And finally, if you're also done with the Connection, close that too. You'll want to be done with this whenever 
    // you're done doing a block of operations (or just one if that's all your 'block' is), like updating a couple of 
    // values in a table or running a couple get requests to get info you need.
    conn.close();
}
catch (SQLException e) {
    // Sometimes 'checked' SQL exceptions are thrown, so we need to catch and rethrow them. Don't forget to include the
    // caught exception as a nested cause.
    throw new IllegalStateException("Something went wrong with our db query.", e); 
}
```

You can also prepare update, insert, and delete statements the same way with the '?'. 

Here's an example of an insert statement:
```java 
try {
    // Insert a couple of rows into the people table.
    String sql = "INSERT INTO people (name, age) VALUES (?, ?), (?, ?);";
    
    Connection conn = DriverManager.getConnection(url, username, password);
    PreparedStatement stmt = conn.prepareStatement(sql);
    
    // Set the first row's values.
    stmt.setString(1, "user1");
    stmt.setInt(2, 5);
    // Set the second row's values.
    stmt.setString(3, "user2");
    stmt.setInt(4, 10);
    
    // Run the execute() method instead and check to make sure it worked correctly.
    boolean success = stmt.execute();
    if (success) {
        System.out.println("Yay! The thing worked!");
    }
    else {
        System.out.println("Oh no, something went wrong!");
        throw new IllegalStateException("Something went wrong with the database query.");
    }
    
    // Don't forget to close your statement and Connection.
    stmt.close();
    conn.close();
}
catch (SQLException e) {
    throw new IllegalStateException("Something went wrong with our db query.", e); 
}
```

>Note: if you'd prefer to handle the `SQLExceptions` somewhere else you can also just change your method to declare that
>it throws SQLExceptions like this `public void doSomeDatabaseWork() throws SQLException {`. You'll still have to catch
>and rethrow, but if that makes it easier for you, then feel free to do that.
