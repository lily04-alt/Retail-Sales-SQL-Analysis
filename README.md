# Adventure Works Product Price and Sales Analysis Using SQL 

## Project Overview

This project uses the AdventureWorksLT sample database to analyze retail product pricing, sales transactions, and potential profitability concerns using Microsoft SQL Server Transact-SQL (T-SQL).

This SQL analysis uses the Adventure Works LT database to compare product manufacturing costs, recommended list prices, and actual selling prices.

Using SQL subqueries, correlated subqueries, and filtering conditions. Compared the results using JOINs statement. This project demonstrates how transactional data can be transformed into actionable business insights.


## Business Objectives

The project answers four key business questions:

1. Pricing benchmark analysis: Which products have a list price higher than the overall average unit price of products sold?
2. Sales price analysis: Which products priced at 100 or more have been sold for less than 100?
3. Product-level price analysis: What are the standard manufacturing cost, list price, and average selling price for each product sold?
4. Potential profitability analysis: Which products have an average selling price below their standard manufacturing cost?


## Dataset

The analysis uses the AdventureWorksLT sample database, which contains product and sales transaction data for a fictional retail business:

- `SalesLT.Product`: Contains product details, including productID, product name, standard manufacturing cost, and list price.
- `SalesLT.SalesOrderDetail`: Contains individual sales transactions, including productID, and the actual unit price charged for each product.

Relationship: `ProductID` connects the product information table to the sales transaction table.


## Tools and Skills Demonstrated

- Microsoft Fabric: SQL query development and execution
- Microsoft SQL Server / T-SQL: data retrieval and analysis
- SQL subqueries
- Correlated subqueries
- Aggregate functions using `AVG()`
- Table joins
- Business profitability analysis


## Query 1: Products Priced Above the Overall Average Selling Price

This query returns products whose list price is higher than the average unit price across all recorded sales.

```sql
SELECT ProductID, Name, ListPrice
FROM SalesLT.Product
WHERE ListPrice > (SELECT AVG (UnitPrice)
FROM SalesLT.SalesOrderDetail)
ORDER BY ProductID;
```

### Interpretation

SQL concepts: Scalar subqueries, AVG(), WHERE, and ORDER BY.

The subquery calculates one overall average selling price from all sales records. The outer query compares each product list against that single average.


## Query 2: Products Listed at 100 or More but Sold Below 100

This query returns products with a list price of at least 100 that have appeared in a sale with a unit price below 100.

```sql
SELECT ProductID, Name, ListPrice
FROM SalesLT.Product
WHERE ProductID IN (SELECT ProductID
FROM SalesLT.SalesOrderDetail
WHERE UnitPrice < 100)
AND 
ListPrice >= 100
ORDER BY ProductID;
```

### Interpretation

SQL concepts: Subqueries, `IN`, multiple filtering conditions, and price thresholds.

The subquery identifies products that were sold below 100. The outer query returns only those products whose recommended list price is 100 or higher.


## Query 3: Cost, List Price, and Average Selling Price by Product

This query returns the standard cost, list price, and average actual selling price for each product.

```sql
SELECT p.ProductID, p.Name, p.StandardCost, p.ListPrice,
 (SELECT AVG (o.UnitPrice)
  FROM SalesLT.SalesOrderDetail AS o
  WHERE o.ProductID = p.ProductID) AS AVGSellingPrice
  FROM SalesLT.Product AS p
ORDER BY ProductID;
```

### Interpretation

SQL concepts: Correlated subqueries, `AVG()`, column aliases, and outer-query references.

This is a correlated subquery because the inner query refers to `p.ProductID` from the outer query. Therefore, the average unit price is calculated separately for each product.

**How it works:**

1. The outer query retrieves productID, manufacturing costs, and list prices.
2. The correlated subquery calculates the average recorded selling price for the current product.
3. The `ProductID` condition ensures that only sales belonging to the current product contribute to its average.
4. Products without recorded sales return `NULL` for `AvgSellingPrice`.


## Query 4: Products Sold Below Their Standard Cost

This query filters the previous analysis to show products whose average selling price is lower than the manufacturing cost.

```sql
SELECT p.ProductID, p.Name, p.StandardCost, p.ListPrice,
  (SELECT AVG (o.UnitPrice)
   FROM SalesLT.SalesOrderDetail AS o
   WHERE o.ProductID = p.ProductID) AS AvgSellingPrice
  FROM SalesLT.Product AS p 
   WHERE StandardCost > (SELECT AVG (op.UnitPrice)
   FROM SalesLT.SalesOrderDetail AS op
   WHERE p.ProductID = op.ProductID)
ORDER BY ProductID;
```

### Interpretation

SQL concepts: Correlated subqueries, aggregate functions, comparison operators, and conditional filtering.

How it works:

1. The outer query retrieves product details and pricing information.
2. The correlated subquery calculates the average unit price for each product.
3. The `WHERE` clause compares the product standard cost against its average selling price.
4. Only products whose standard cost exceeds their average selling price are returned.

A product returned by this query may be unprofitable on average because its average selling price is below its standard manufacturing cost. This result should be investigated for discounting, pricing errors, promotions, or changes in production cost.


## Key Learning and Analytical Insights

### Learning:

- A regular subquery can calculate one value used by the outer query, such as the overall average unit price.
- An `IN` subquery checks whether a product exists in a filtered list of sales records.
- A correlated subquery runs in relation to each row in the outer query.
- `AVG(UnitPrice)` helps compare actual selling prices with product list prices and manufacturing costs.

### Analytical Insights:

The four queries support different stages of retail pricing analysis:

| Analysis                        | Metric or condition                               | Purpose                                                |
| ------------------------------- | ------------------------------------------------- | ------------------------------------------------------ |
| Overall price benchmark         | List price above overall average unit price       | Compare recommended prices with the sales benchmark.   |
| Price threshold                 | List price ≥ 100 and unit price < 100             | Identify products with lower-priced transactions.      |
| Product-level comparison        | Standard cost, list price, and average unit price | Evaluate differences between product pricing measures. |
| Potential cost-recovery concern | Standard cost > average unit price                | Flag products for further investigation.               |


## Author

Aspiring Data Analyst | SQL | Microsoft Fabric | Power BI | Excel

I am developing my data analytics skills through practical projects that combine SQL querying, business problem-solving, and data-driven decision-making. This project demonstrates my approach to analyzing retail pricing and documenting reproducible SQL workflows.
