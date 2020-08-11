# **SQL**

## **Reporting**

Some ways to handle data:

- Ordering (e.g. sort products alphabetically or by price, movies by year, articles by date, sorting contacts)

- Limiting (e.g. retrieve lastest articles, paging through archive)

- Manipulating Text (e.g. standarizing output: lowercase emails, uppercase last names)

- Aggregation (e.g. count values, sum values, maximum number, minimum number)

- Date (e.g. search using today's date, date calculations)

## **Ordering**

Ordering by a single column criteria:

        SELECT * FROM <table name> ORDER BY <column> [ASC|DESC];

ASC is used to order results in ascending order.  DESC is used to order results in descending order.

Examples:

        SELECT * FROM books ORDER BY title ASC;
        SELECT * FROM products WHERE name = "Sonic T-Shirt" ORDER BY stock_count DESC;
        SELECT * FROM users ORDER BY signed_up_on DESC;
        SELECT * FROM countries ORDER BY population DESC;

Ordering by multiple column criteria:

        SELECT * FROM <table name> ORDER BY <column> [ASC|DESC],
                                            <column 2> [ASC|DESC],
                                            ...,
                                            <column n> [ASC|DESC];

        SELECT * FROM customs ORDER BY first_name ASC, last_name ASC

Ordering is prioritized left to right.

Examples:

        SELECT * FROM books ORDER BY    genre ASC, 
                                        title ASC;

        SELECT * FROM books ORDER BY    genre ASC,
                                        year_published DESC;

        SELECT * FROM users ORDER BY    last_name ASC,
                                        first_name ASC;

## **Limiting**

Databases can store massive amounts of data, and often you don't want to bring back all of the results. It's slow and it affects the performance for other users. In most cases you only want a certain subset of the rows.

SQLite, PostgreSQL and MySQL: To limit the number of results returned, use the LIMIT keyword.

        SELECT <columns> FROM <table> LIMIT <# of rows>;

MS SQL: To limit the number of results returned, use the TOP keyword.

        SELECT TOP <# of rows> <columns> FROM <table>;

Oracle: To limit the number of results returned, use the ROWNUM keyword in a WHERE clause.

        SELECT <columns> FROM <table> WHERE ROWNUM <= <# of rows>;

Limiting just by the top set of results is a fine thing, but let's say you wanted to generate a multi page report. Having a blog archive or listing search results in batches of 50 is where you want paging.

SQLite, PostgreSQL and MySQL: To page through results you can either use the OFFSET keyword in conjunction with the LIMIT keyword or just with LIMIT alone.

        SELECT <columns> FROM <table> LIMIT <# of rows> OFFSET <skipped rows>;
        SELECT <columns> FROM <table> LIMIT <skipped rows>, <# of rows>; 

MS SQL and Oracle: To page through results you can either use the OFFSET keyword in conjunction with the FETCH keyword. Cannot be used with TOP.

        SELECT <columns> FROM <table> OFFSET <skipped rows> ROWS FETCH NEXT <# of rows> ROWS ONLY;

        SELECT * FROM phone_book ORDER BY last_name ASC, first_name ASC LIMIT 20 OFFSET 60;

Example #1:

- Obtain the actor records between the 701st and 720th records using only LIMIT and OFFSET
- N.B Due to actors being removed from the database the 701st row has the id of 702

        SELECT * FROM actors ORDER BY id LIMIT 20 OFFSET 699;

Example #2:

- Obtain the actor records between the 701st and 720th records using only LIMIT and OFFSET
- N.B Due to actors being removed from the database the 701st row has the id of 702

        SELECT * FROM actors ORDER BY id LIMIT 20 OFFSET 699;

Example #3:

- Sort all reviews by username

        SELECT * FROM reviews ORDER BY username ASC;

Example #4:

- Order movies with the most recent movies appearing at the top

        SELECT * FROM movies ORDER BY year_released DESC;

## **Functions**

Keywords: Commands issued to a database. The data presented in queries is unaltered.

Operators: Performs comparisons and simple manipulation

Functions: Presents data differently through more complex manipulation

A function looks like:

        <function name>(<value or column>)

Example:

        UPPER("Andrew Chalkley") 

## **Adding Text Columns Together**

SQLite, PostgreSQL and Oracle
Use the concatenation operator ||.

        SELECT <value or column> || <value or column> || <value or column>  FROM <table>;  

MS SQL
Use the concatenation operator +.

        SELECT <value or column> + <value or column> + <value or column>  FROM <table>;  

MySQL, Postgres and MS SQL
Use the CONCAT() function.

        SELECT CONCAT(<value or column>, <value or column>, <value or column>) FROM <table>;

Example with space:

        SELECT first_name || " " || last_name AS "Full Name", email AS "Email", phone AS "Phone" FROM customers ORDER BY last_name

NOTE: In SQL there's a difference between using single quotes (') and double quotes ("). Single quotes should be used for String literals (e.g. 'lbs'), and double quotes should be used for identifiers like column aliases (e.g. "Max Weight"):

SELECT maximum_weight || 'lbs' AS "Max Weight" FROM ELEVATOR_DATA;

## **Finding the Length of Text**

To obtain the length of a value or column use the LENGTH() function.

        SELECT LENGTH(<value or column>) FROM <tables>;

        SELECT LENGTH(<column>) AS <alias> FROM <table>;

        SELECT <columns> FROM <table> WHERE LENGTH(<column>) <operator> <value>;

Example:

        SELECT username, LENGTH(username) AS length FROM customers;

        SELECT username, LENGTH(username) AS length FROM customers ORDER BY length DESC LIMIT 1;

## **Changing the Case of Text Columns

Use the UPPER() function to uppercase text.

        SELECT UPPER(<value or column>) FROM <table>;

Use the LOWER() function to lowercase text.

        SELECT LOWER(<value or column>) FROM <table>;

Example:

        SELECT LOWER(title) AS lower_case_title, UPPER(author) AS upper_case_author FROM books;

## **Creating Excerpts From Text**

To create smaller strings from larger piece of text you can use the SUBSTR() funciton or the substring function.

        SELECT SUBSTR(<value or column>, <start>, <length>) FROM <table>;

Example:

        SELECT name, SUBSTR(description, 1 50) || "..." AS short_description, price FROM products;

## **Replacing Portions of Text**

To replace piece of strings of text in a larger body of text you can use the REPLACE() function.

        SELECT REPLACE(<original value or column>, <target string>, <replacement string>) FROM <table>;

        SELECT street, city, FROM addresses WHERE REPLACE(state, "California", "CA") = "CA";

        SELECT street, city, REPLACE(state, "California", "CA") zip FROM addresses WHERE REPLACE(state, "California", "CA") = "CA";

## **Counting Results**

To count rows you can use the COUNT() function.

        SELECT COUNT(*) FROM <table>;

To count unique entries use the DISTINCT keyword too:

        SELECT COUNT(DISTINCT <column>) FROM <table>;

Example:

        SELECT COUNT(*) FROM customers ORDER BY id DESC LIMIT 1;

        SELECT COUNT(*) FROM customers WHERE first_name = "Andrew";

        SELECT COUNT(DISTINCT category) FROM products;

        SELECT COUNT(*) AS scifi_book_count FROM books WHERE genre = "Science Fiction";

## **Counting Groups of Rows**

Basic SQL syntax:

        SELECT <column> FROM <table> GROUP BY <column>;

To count aggregated rows with common values use the GROUP BY keywords:

        SELECT COUNT(<column>) FROM <table> GROUP BY <column 

Examples:

        SELECT genre, count(*) as genre_count FROM books GROUP BY genre;

## **Grand Total**

Basic SQL syntax:

        SELECT SUM(<column>) FROM <table> GROUP BY <another column>;

        SELECT SUM(<numeric column) FROM <table>;

        SELECT SUM(<numeric column) AS <alias> FROM <table>
                                            GROUP BY <another column>
                                            HAVING <alias> <operator> <value>;

Examples:

        SELECT SUM(cost) AS total_spend, user_id FROM orders GROUP BY user_id ORDER BY total_spend DESC LIMIT 1;

## **Averages**

To get the average value of a numeric column use the AVG() function.

        SELECT AVG(<numeric column>) FROM <table>;
        SELECT AVG(<numeric column>) FROM <table> GROUP BY <other column>;

Examples:

        SELECT AVG(rating) AS average_rating FROM reviews WHERE movie_id = "6";

## **Min / Max**

To get the maximum value of a numeric column use the MAX() function.

        SELECT MAX(<numeric column>) FROM <table>;
        SELECT MAX(<numeric column>) FROM <table> GROUP BY <other column>;

To get the minimum value of a numeric column use the MIN() function.

        SELECT MIN(<numeric column>) FROM <table>;
        SELECT MIN(<numeric column>) FROM <table> GROUP BY <other 

Examples:

        SELECT AVG(cost) AS average, MAX(cost) AS Maximum, MIN(cost) AS Minimum, user_id FROM orders GROUP BY user_id;

## **Mathematical Numeric Types**

Mathematical Operators
* Multiply
/ Divide
+ Add
- Subtract

        SELECT <numeric column> <mathematical operator> <numeric value> FROM <table>;

Examples:

        SELECT name, ROUND(price * 1.06, 2) AS "Price in Florida" FROM products;

        SELECT name, ROUND(price / 1.4, 2) AS price_gbp FROM products;
git 
## **Other Random Examples**

- Find the actor with the longest name

        SELECT name, LENGTH(name) AS length FROM actors ORDER BY length DESC LIMIT 1;

- Add the username after the review. e.g. "That was a really cool movie - chalkers"

        SELECT review || " - " || username AS combined FROM reviews;

- Uppercase all movie titles

        SELECT UPPER(title) FROM movies;

- In all of the reviews, replace the text "public relations" with "PR"

        SELECT REPLACE(review, "public relations", "PR") FROM reviews;

- From the actors, truncate names greater than 10 charactor with ... e.g. William Wo...

        SELECT name, SUBSTR(name, 1, 10) || "..." AS short_description FROM actors;

- Count number in a specific genre:

        SELECT COUNT(genre) FROM movies WHERE genre = "Musical";

- Average rating:

        SELECT AVG(rating) FROM reviews WHERE username = "chalkers";

- Sorting movie IDs by average rating:

        SELECT movie_id, AVG(rating) FROM reviews GROUP BY movie_id;

## **Date and Time Functions**

One of the powerful features in SQL is writing queries based on today's date and time. Here are some variations based on different types:

SQLite

To get the current date use: DATE("now")
To get the current time use: TIME("now")
To get the current date time: DATETIME("NOW")

MS SQL

To get the current date use: CONVERT(date, GETDATE())
To get the current time use: CONVERT(time, GETDATE())
To get the current date time: GETDATE()

MySQL

To get the current date use: CURDATE()
To get the current time use: CURTIME()
To get the current date time: NOW()

Oracle and PostgreSQL

To get the current date use: CURRENT_DATE
To get the current time use: CURRENT_TIME
To get the current date time: `CURRENT_TIMESTAMP

Example:

    SELECT * FROM orders WHERE status = "placed" AND ordered_on = DATE("now");

    SELECT COUNT(*) AS "shipped_today" FROM orders WHERE status = "shipped" AND ordered_on = DATE("now");

## **Calculating Dates**

Calculating dates are great for generating reports and dashboards that are dynamic in nature.

Examples:

    SELECT COUNT(*) FROM orders WHERE ordered_on BETWEEN DATE("now", "-7 days") AND DATE("now, "-1 day");

    SELECT COUNT(*) FROM orders WHERE ordered_on BETWEEN DATE("now", "-7 days", "-7 days") AND DATE("now, "-1 day", "-7 days");

    SELECT COUNT(status) AS ordered_yesterday_and_shipped FROM orders WHERE status = "shipped" AND ordered_on = DATE("now", "-1 day");

## **Formatting Dates For Reporting**

Examples:

        SELECT *, STRFTIME("%d/%m/%Y", ordered_on) AS UK_date FROM orders;

        SELECT title, STRFTIME("%m/%Y", date_released) AS month_year_released FROM movies;

        STRFTIME("%d-%m", "now")

        STRFTIME("%m-%Y", "2016-12-19 09:10:55")

        STRFTIME("%Y", "2016-12-19 09:10:55", "+1 year")

        SELECT * FROM loans WHERE return_by = DATE("now", "-1 day");