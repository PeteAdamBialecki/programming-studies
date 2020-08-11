# **SQL Data Modification**

At the heart of a dynamic application is a database. Whether the application is an eCommerce, sports team, social network or a productivity app on your phone the data needs to change over time.  The following notes are for modifing data for dynamic applications.

## **CRUD**

Four main data operations: Create, Read (this has been primarily been covered in the SQL commands readme file), Update, Delete.

## **Create**

Inserting a single row:

        INSERT INTO <table> VALUES (<value 1>, <value 2>, ...);

This will insert values in the order of the columns prescribed in the schema.

Examples:

        INSERT INTO users VALUES  (1, "chalkers", "Andrew", "Chalkley");
        INSERT INTO users VALUES  (2, "ScRiPtKiDdIe", "Kenneth", "Love");

        INSERT INTO movies VALUES (3, "Starman", "Science Fiction", 1984);
        INSERT INTO movies VALUES (4, "Moulin Rouge!", "Musical", 2001);

Inserting a single row with values in any order (the name of the columns and the values need to be in order in the SQL statement):

        INSERT INTO <table> (<column 1>, <column 2>) VALUES (<value 1>, <value 2>);
        INSERT INTO <table> (<column 2>, <column 1>) VALUES (<value 2>, <value 1>);

Examples:

        INSERT INTO users (username, first_name, last_name) VALUES ("chalkers", "Andrew", "Chalkley");
        INSERT INTO users (first_name, last_name, username) VALUES  ("Kenneth", "Love", "ScRiPtKiDdIe");

        INSERT INTO movies (title, genre, year_released) VALUES ("Starman", "Science Fiction", 1984);
        INSERT INTO movies (title, year_released, genre) VALUES ("Moulin Rouge!", 2001,  "Musical");

NOTE: To prevent clashing of creating an element with the same id, you can enter in 'NULL' and then the auto-incremented value would be added.

NOTE: If you don't specifiy a column and a value, the element will most likely be signified as null.  The exception is if the scheme has been designed where certain columns can not be specified as null as in the example of a library database where a column like "returned_by" needs to be filled in at a later date.

To add multiple rows and values, use the following structure:

        INSERT INTO <table> (<column 1>, <column 2>, ...)
            VALUES
                (<value 1>, <value 2>, ...),
                (<value 1>, <value 2>, ...),
                (<value 1>, <value 2>, ...);

Example:

        INSERT INTO users (username, first_name, last_name)
            VALUES
                ("chalkers", "Andrew", "Chalkley"),
                ("ScRiPtKiDdIe", "Kenneth", "Love");

        INSERT INTO movies (title, genre, year_released)
            VALUES
                ("Starman", "Science Fiction", 1984),
                ("Moulin Rouge!", "Musical", 2001);

## **Updating All Rows in a Table**

An update statement for all rows:

        UPDATE <table> SET <column> = <value>;

The = sign is different from an equality operator from a WHERE condition. It's an assignment operator because you're assigning a new value to something.

Examples:

        UPDATE users SET password = "thisisabadidea";
        UPDATE products SET price = 2.99;

Update multiple columns in all rows:

        UDPATE <table> SET <column 1> = <value 1>, <column 2> = <value 2>;

Examples:

        UPDATE users SET first_name = "Anony", last_name = "Moose";
        UPDATE products SET stock_count = 0, price = 0;

## **Updating Specific Rows**

An update statement for specific rows:

        UPDATE <table> SET <column> = <value> WHERE <condition>;

Examples:

        UPDATE users SET password = "thisisabadidea" WHERE id = 3;
        UPDATE blog_posts SET view_count = 1923 WHERE title = "SQL is Awesome";

Update multiple columns for specific rows:

        UPDATE <table> SET <column 1> = <value 1>, <column 2> = <value 2> WHERE <condition>;

Examples:

        UPDATE users SET entry_url = "/home", last_login = "2016-01-05" WHERE id = 329;
        UPDATE products SET status = "SOLD OUT", availability = "In 1 Week" WHERE stock_count = 0;

## **Removing Data from all Rows in a Table**

To delete all rows from a table:

        DELETE FROM <table>;

Examples:

        DELETE FROM logs;
        DELETE FROM users;
        DELETE FROM products;

## **Removing Specific Rows**

To delete specific rows from a table:

        DELETE FROM <table> WHERE <condition>;

Examples:

        DELETE FROM users WHERE email = "andrew@teamtreehouse.com";
        DELETE FROM movies WHERE genre = "Musical";
        DELETE FROM products WHERE stock_count = 0;
        DELETE FROM products WHERE price >= "11.00";

## **Transactions**

Switch autocommit off and begin a transaction:

        BEGIN TRANSACTION;

Or simply:

        BEGIN;

To save all results of the statements after the start of the transaction to disk:

        COMMIT;

To reset the state of the database to before the begining of the transaction:

        ROLLBACK;

## **NOTES:**

ORM - Object-Relational Mapping – used to perform CRUD operations with a language other than SQL.
DML - Data Manipulation Language - the subset of the SQL programming language that deals with CRUD operations. Examples of ORMs:

Hibernate for Java

CoreData for Objective-C or Swift

Django ORM for Python

ActiveRecord for Ruby