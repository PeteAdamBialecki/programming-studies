# **SQL**

## **Querying Relational Databases**

How to structure data in an organized manner.  Benefits of relational databases:

- Maximize storage

- Better application functionality

- Cleaner, richer data for business reporting

## **Normalization**

The process of eliminating redundant or repeating data in a database.

## **UNIQUE Keys**

- Can be null

- Multiple unique keys per table (e.g. email, SSN, etc.)

- Can be modified to a new value

## **PRIMARY Keys**

- Never be null

- One primary key per table

- Cannot be modified to a new value

## **FOREIGN Key**

- Columns designed correctly

- Foreign Key constraint

Extra:

Referential Integrity: FOREIGN key values must also exist as a PRIMARY key value in the reference table.

## **One-To-Many Relationships**

One to Many relationships are by far the most common type of table relationship. There is a wealth of information about One to Many relationships available.

## **Many-To-Many Relationships**

Many to Many relationships mean that a record in one table can relate to many other records in another table, and one record from the second table can also relate back to many records in the first table.

## **One-To-One Relationships**

A “One to one” relationship between two tables means that a row in one table can only relate to one row in the table on the other side of their relationship and vice versa. This is the least common database relationship. One to One relationships are the least common database relationship.

## **JOIN**

A join is the instruction to the query engine on how to relate records in one table to records in another.

INNER JOIN: The backbone of advanced SQL querying. There are literally thousands of books, articles and blog posts with examples and information about SQL joins.  Basic syntax structure:

        SELECT <columns>
            FROM <table1>
            INNER JOIN <table2> ON <equality criteria>
            INNER JOIN <table3> ON <equlaity criteria>
            WHERE <search criteria>...

Examples:

        SELECT * FROM make INNER JOIN model ON make.MakeID = model.MakeID;

...a cleaner version:

        SELECT MakeName, ModelName FROM make INNER JOIN model ON make.MakeID = model.MakeID;

...a more detailed version:

        SELECT mk.MakeName, md.ModelName FROM make AS mk INNER JOIN model AS md ON mk.MakeID = md.MakeID;

To isolate a single data type:

        SELECT mk.MakeName, md.ModelName FROM make AS mk INNER JOIN model AS md ON mk.MakeID = md.MakeID WHERE mk.MakeName = "Chevy";

OUTER JOIN (LEFT, RIGHT, FULL): A few database engines, like SQLite, only support the Left Outer Join. Most other database systems support all three types.

Right Outer Joins are essentially the same thing as Left Outer Joins, but just specified in the opposite direction.

Full Outer Joins are functionally different in that you are guaranteed to get all rows from both tables. Full Outer Joins aren’t used as much in application or report coding as Inner and Left/Right Outer Joins, but can come in very handy when doing things like comparing data between two tables that are structurally similar.  Basic syntax structure:

        SELECT <columns>
            FROM <table1>
            LEFT OUTER JOIN <table2> ON <equality criteria>
            INNER JOIN <table3> ON <equlaity criteria>
            WHERE <search criteria>...

Examples:

        SELECT mk.MakeName, md.ModelName FROM make AS mk LEFT OUTER JOIN model AS md ON mk.MakeID = md.MakeID;

...to force NULL values to be counted:


        SELECT mk.MakeName, COUNT(md.ModelName) AS NumberOfModels FROM make AS mk LEFT OUTER JOIN model AS md ON mk.MakeID = md.MakeID GROUP BY mk.MakeName;

Example:

        SELECT DISTINCT first_name, email FROM patrons INNER JOIN loans ON patrons.id = loans.patron_id WHERE loans.returned_on IS NULL;

        SELECT MakeName, ModelName, VIN, StickerPrice
            FROM Make
            INNER JOIN Model ON Make.MakeID = Model.MakeID
            INNER JOIN Car ON Car.ModelID = Model.ModelID;

Sample: In a car database there is a Sale table with columns, SaleID, CarID, CustomerID, LocationID, SalesRepID, SaleAmount and SaleDate. The database also has a SalesRep table with columns, SalesRepID, FirstName, LastName, SSN, PhoneNumber, StreetAddress, City, State and ZipCode.Show the First and Last Name of each sales rep along with SaleAmount from both the SalesRep and Sale tables in one result set.

        SELECT FirstName, LastName, SaleAmount
            FROM Sale
            INNER JOIN SalesRep ON Sale.SalesRepID = SalesRep.SalesRepID;

        SELECT ModelName, VIN FROM Model LEFT OUTER JOIN Car ON Car.ModelID = Model.ModelID;

## **Set Operations**

Combine or limit results using two or more datasets.  There are four types: UNION, UNION ALL, INTERSECT, and EXCEPT.

UNION Operations

        SELECT * FROM make UNION SELECT * FROM foreignMake;

        SELECT MakeName FROM Make UNION SELECT MakeName FROM ForeignMake;

        SELECT MakeID, MakeName FROM Make UNION SELECT ForeignMakeID, MakeName FROM ForeignMake;

        SELECT MakeID, MakeName FROM Make
            WHERE MakeName < "D"
        UNION 
        SELECT ForeignMakeID, MakeName FROM ForeignMake
            WHERE MakeName < "D";
            ORDER BY MakeName;

UNION ALL Operations

        SELECT MakeName FROM Make UNION ALL SELECT MakeName FROM ForeignMake ORDER BY MakeName;

INTERSECT Operations

As with a UNION, they must have the same columns in both the left and right side of the SQL operation.

        SELECT MakeName FROM Make
            INTERSECT
        SELECT MakeName FROM ForeignMake ORDER BY MakeName DESC;

EXCEPT Operations

This uses the same format as INTERSECT, but outputs only the records that are not in the latter table.

        SELECT ForeignMakeID, MakeName FROM ForeignMake EXCEPT SELECT MakeID, MakeName FROM Make;

Other Examples:

    Create a list of all books in both north and south locations

        SELECT * FROM loans_north
        UNION
        SELECT * FROM loans_south;

    Create a list of unique books. 
    Books that are in the north or south location, but not in both locations.

        SELECT title FROM books_north
        INTERSECT
        SELECT title FROM books_south
        ORDER BY title DESC;

    Create a list of duplicate books. 
    Book titles that exist in BOTH north AND south locations

        SELECT title FROM books_north
        UNION
        SELECT title FROM books_south
        ORDER BY title DESC;

    There are two tables Fruit and Vegetable table. The Fruit table has a FruitID and a Name column and the Vegetable table has a VegetableID and Name column.Create a list of all fruits and vegetables starting with the letters A through K . In other words all fruit and vegetables that don't start with the letter L to Z.

        SELECT Name FROM Fruit WHERE Name < "L" UNION SELECT Name FROM Vegetable WHERE Name < "L" ORDER BY Name ASC;

    There are two tables Fruit and Vegetable table. The Fruit table has a FruitID and a Name column and the Vegetable table has a VegetableID and Name column. Create an alphabetical list of vegetables that are NOT considered a fruit.

        SELECT Name FROM Vegetable
        EXCEPT
        SELECT Name FROM Fruit
        ORDER BY Name;

## **SUBQUERIES**

For lots of extra notes on sub queries, click [here](https://sqlschool.modeanalytics.com/advanced/subqueries/).  Possible use case scenarios include searching for criteria where a clause is not specifically known or you need a temporary dataset to join with other tables in your database.  Example structure for SQL subquery syntax:

        SELECT <columns>
        FROM <table 1>
        WHERE COlumn1 IN (
            SELECT column1
            FROM <table 2>
            WHERE <search criteria>
        );

## **IN**

        SELECT * FROM Sale WHERE CarID IN (1, 3, 5);
        SELECT CarID FROM Car WHERE ModelYear = 2015;

Changes to:

        SELECT * FROM Sale WHERE CarID IN (SELECT CarID FROM Car WHERE ModelYear = 2015);

For the opposite:

        SELECT * FROM Sale WHERE CarID NOT IN (SELECT CarID FROM Car WHERE ModelYear = 2015);

## **TEMPORARY TABLE**

Basic temporary SQL table structure:

        SELECT <columns>
        FROM <table>
        <INNER | OUTER> JOIN
        (SELECT <columns> FROM <table> WHERE <search criteria>) AS <alias>
        ON <join criteria>;

Sample code example:

        SELECT * FROM Sale AS s
            INNER JOIN (SELECT CarID, ModelYear FROM Car WHERE ModelYear = 2015) AS t
            ON s.CarID = t.CarID;

Another more complex example of a derived table:

        SELECT sr.LastName, Loc1.StLouisAmount, Loc2.ColumbiaAmount FROM SalesRep AS sr LEFT OUTER JOIN (
            SELECT SalesRepID, SUM(SaleAmount) AS StLouisAmount
            FROM Sale AS s WHERE s.LocationID = 1
            GROUP BY SalesRepID
        ) AS Loc1 ON sr.SalesREpID = Loc1.SalesRepID
        LEFT OUTER JOIN (
            SELECT SalesRepID, SUM(SaleAmount) AS ColumbiaAmount
            FROM Sale AS s WHERE s.LocationID = 2
            GROUP BY SalesRepID
        ) AS Loc2 ON sr.SalesRepID = Loc2.SalesRepID;

Additional examples:

Generate a report that lists the book titles from both locations and count the total number of books with the same title.

Sample #1:

        SELECT title, COUNT(*) AS Number FROM (
            SELECT title FROM books_north
            UNION ALL
            SELECT title FROM books_south
        ) GROUP BY title
            UNION
        SELECT "Total number of books with the same title", COUNT(*)
            FROM (SELECT title, COUNT(*) AS numBooks FROM (
                SELECT title FROM books_north
                UNION ALL
                SELECT title FROM books_south
            ) GROUP BY title) AS t
        WHERE t.numBooks >= 2;

Sample #2 (of above):

        SELECT * FROM 
            (SELECT title, COUNT(*) AS Number FROM (
            SELECT title FROM books_north
            UNION ALL
            SELECT title FROM books_south
            ) GROUP BY title
            )
        LEFT OUTER JOIN
            (SELECT * FROM
            (SELECT COUNT(*) AS "Total number of books with the same title"
            FROM (SELECT title, COUNT(*) AS numBooks FROM (
            SELECT title FROM books_north
            UNION ALL
            SELECT title FROM books_south
            ) GROUP BY title) AS t
            WHERE t.numBooks >= 2))

Sample question #3: Generate a report that lists a patron's first name, email and loan count for loans that haven't been returned.

        SELECT first_name, email, COUNT(*) AS "Loan Count" FROM patrons
        INNER JOIN 
            (SELECT patron_id FROM loans_south WHERE returned_on IS NULL
            UNION ALL
            SELECT patron_id FROM loans_north WHERE returned_on IS NULL) AS loans
        ON patrons.id = loans.patron_id
        GROUP BY patron_id;

Sample Question #4: In a car database there is a Model table with columns, ModelID, MakeID and ModelName and a Car table with columns, CarID, ModelID, VIN, ModelYear and StickerPrice. Use a subquery along with IN to list all the Model Names with a Sticker Price greater than $30000.

        SELECT ModelName FROM Model
        WHERE ModelID IN (
        SELECT ModelID FROM Car
        WHERE StickerPrice > 30000
        );

Sample Question #5: In a car database there is a Sale table with columns, SaleID, CarID, CustomerID, LocationID, SalesRepID, SaleAmount and SaleDate and a Car table with columns, CarID, ModelID, VIN, ModelYear and StickerPrice.Use a subquery along with IN to list all sales of cars with Sticker Price greater than $30000. Include all columns.

    SELECT * FROM Sale
    WHERE CustomerID IN (
        SELECT CustomerID FROM Customer
        WHERE Gender = "F"
        );

Sample Question #6: In a car database there is a Sale table with columns, SaleID, CarID, CustomerID, LocationID, SalesRepID, SaleAmount and SaleDate and a Customer table with columns, CustomerID, FirstName, LastName, Gender and SSN. Use a subquery as a derived table to show all sales to female ('F') customers. Select all columns from the Sale table only.

SELECT * FROM Sale AS s
    INNER JOIN (
    SELECT CustomerID FROM Customer
    WHERE Gender = 'F'
    ) AS c
ON s.CustomerID = c.CustomerID;