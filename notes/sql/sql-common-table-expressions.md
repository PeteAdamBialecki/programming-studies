# **Common SQL Table Expressions**

## **What Is A Common Table Expression?**

Benefits to using a CTE:

    - Makes queries easier to read

    - Organizes queries into reusable modules

    - Better matches how you think about data analysis

Example Code: Basic Common Table Expression

        WITH product_details AS (
            SELECT ProductName, CategoryName, UnitPrice, UnitsInStock
            FROM Products
            JOIN Categories ON PRODUCTS.CategoryId = Categories.Id
            WHERE Products.Discontinued = 0
        ) SELECT * FROM product_details
        ORDER BY 2, 1

Example Code: Basic Common Table Expression Expanded

        WITH product_details AS (
            SELECT ProductName, CategoryName, UnitPrice, UnitsInStock
            FROM Products
            JOIN Categories ON PRODUCTS.CategoryId = Categories.Id
            WHERE Products.Discontinued = 0
        ) SELECT CategoryName, COUNT(*) AS unique_product_count, SUM(UnitsInStock) AS stock_count
        FROM product_details
        GROUP BY 1
        ORDER BY 2

Expanding on an expression, step 1:

        SELECT ProductName, CategoryName, UnitPrice, UnitsInStock
        FROM Products
        JOIN Categories ON PRODUCTS.CategoryId = Categories.Id
        WHERE Products.Discontinued = 0

Expanding on an expression, step 2:

        WITH product_details AS (
        SELECT ProductName, CategoryName, UnitPrice, UnitsInStock
        FROM Products
        JOIN Categories ON PRODUCTS.CategoryId = Categories.Id
        WHERE Products.Discontinued = 0
        )
        SELECT * FROM product_details
        ORDER BY CategoryName, ProductName;

Expanding on an expression, step 3:

        WITH product_details AS (
        SELECT ProductName, CategoryName, UnitPrice, UnitsInStock
        FROM Products
        JOIN Categories ON PRODUCTS.CategoryId = Categories.Id
        WHERE Products.Discontinued = 0
        )
        SELECT CategoryName, COUNT(*) AS unique_product_count, SUM(UnitsInStock) AS stock_count
        FROM product_details
        GROUP BY CategoryName
        ORDER BY unique_product_count;

## **Convert a Subquery to a CTE**

Steps to convert a subquery:

Step #1:

        SELECT 
        all_orders.EmployeeID, 
        Employees.LastName,
        all_orders.order_count AS total_order_count, 
        late_orders.order_count AS late_order_count
        FROM (
            SELECT EmployeeID, COUNT(*) AS order_count
            FROM Orders
            GROUP BY EmployeeID
        ) all_orders
        JOIN (
            SELECT EmployeeID, COUNT(*) AS order_count
            FROM Orders
            WHERE RequiredDate <= ShippedDate
            GROUP BY EmployeeID 
        ) late_orders
        ON all_orders.EmployeeID = late_orders.employeeID
        JOIN Employees
        ON all_orders.EmployeeId = Employees.Id;

Step #2 (take lines 77-81 from above):

        WITH all_orders AS (
            SELECT EmployeeID, COUNT(*) AS order_count
            FROM Orders
            GROUP BY EmployeeID
        ),
        late_orders AS (
            SELECT EmployeeID, COUNT(*) AS order_count
            FROM Orders
            WHERE RequiredDate <= ShippedDate
            GROUP BY EmployeeID 
        )
        SELECT Employees.ID, LastName
        all_orders.order_count AS total_order_count,
        late_orders.order_count AS late_order_count
        FROM Employees
        JOIN all_orders ON Employees.ID = all_orders.EmployeeID
        JOIN late_orders ON Employees.ID = late_orders.EmployeeID;

## **Using Multiple CTEs in a Query**

Sample code:

        WITH 
        all_sales AS (
            SELECT Orders.Id AS OrderId, Orders.EmployeeId,
            SUM(OrderDetails.UnitPrice * OrderDetails.Quantity) AS invoice_total
            FROM Orders 
            JOIN OrderDetails ON Orders.id = OrderDetails.OrderId
            GROUP BY Orders.ID
        ), 
        revenue_by_employee AS (
            SELECT EmployeeId, SUM(invoice_total) AS total_revenue
            FROM all_sales
            GROUP BY EmployeeID
        ), 
        sales_by_employee AS (
            SELECT EmployeeId, COUNT(*) AS sales_count
            FROM all_sales
            GROUP BY EmployeeId
        )
        SELECT revenue_by_employee
        Employees.Id,
        Employees.LastName, 
        revenue_by_employee.total_revenue,
        sales_by_employee.sales_count,
        revenue_by_employee.total_revenue/sales_by_employee.sales_count AS avg_revenue_per_sale
            FROM revenue_by_employee
            JOIN sales_by_employee ON revenue_by_employee.EmployeeId = sales_by_employee.EmployeeId
            JOIN Employees ON revenue_by_employee.EmployeeId = Employees.Id
            ORDER BY total_revenue DESC