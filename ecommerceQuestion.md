# E-commerce SQL Queries Guide

This document provides an explanation of various SQL queries used to analyze and manage an e-commerce database. Each query addresses a specific business need or provides valuable insights into platform performance. Below, you'll find an overview of the queries, their explanations, use cases, and practical tips.

## Queries Breakdown

### 1. List all users with the number of products they have listed.

```sql
SELECT u.Name, COUNT(p.ProductId) AS ProductCount
FROM User u
LEFT JOIN Product p ON u.UserId = p.SellerId
GROUP BY u.UserId, u.Name;
```

Explanation:
LEFT JOIN: Ensures that all users are included, even those who have no products listed.
COUNT(): Counts the number of products listed by each user.
GROUP BY: Aggregates the results by user, allowing you to get the count of products per seller.
Use Case: Helps sellers track their product contributions.

### 2. What is the average price of products listed by each seller?

```sql
SELECT u.Name, AVG(p.Price) AS AvgPrice
FROM User u
JOIN Product p ON u.UserId = p.SellerId
GROUP BY u.UserId, u.Name;
```

Explanation:
AVG(): Calculates the average price of products listed by each seller.
JOIN: Filters to include only sellers with products listed.
GROUP BY: Aggregates by seller to calculate their average product price.
Use Case: Sellers can evaluate the pricing trends of their products.

### 3. Which product has the highest number of reviews?

```sql
SELECT p.Name, COUNT(r.ReviewId) AS ReviewCount
FROM Product p
JOIN Review r ON p.ProductId = r.ProductId
GROUP BY p.ProductId, p.Name
ORDER BY ReviewCount DESC
LIMIT 1;
```

Explanation:
COUNT(): Counts the number of reviews for each product.
ORDER BY: Sorts the products by the number of reviews, in descending order.
LIMIT 1: Returns only the product with the highest review count.
Use Case: Identifying popular products helps prioritize marketing efforts.

### 4. Show the total sales amount (subtotal) for each product.

```sql
SELECT p.Name, SUM(oi.Subtotal) AS TotalSales
FROM Product p
JOIN OrderItem oi ON p.ProductId = oi.ProductId
GROUP BY p.ProductId, p.Name;
```

Explanation:
SUM(): Adds up the total sales for each product.
GROUP BY: Aggregates results by product to calculate the total sales for each.
Use Case: Useful for analyzing product profitability.

### 5. List buyers with the total amount they spent on orders.

```sql
SELECT u.Name, SUM(o.TotalAmount) AS TotalSpent
FROM User u
JOIN `Order` o ON u.UserId = o.BuyerId
GROUP BY u.UserId, u.Name;
```

Explanation:
SUM(): Sums up the total amount spent by each buyer.
GROUP BY: Aggregates results by buyer to calculate the total spent per buyer.
Use Case: Useful for rewarding loyal customers or analyzing buyer behavior.

### 6. Show the top 3 highest-rated products.

```sql
SELECT p.Name, AVG(r.Rating) AS AvgRating
FROM Product p
JOIN Review r ON p.ProductId = r.ProductId
GROUP BY p.ProductId, p.Name
ORDER BY AvgRating DESC
LIMIT 3;
```

Explanation:
AVG(): Calculates the average rating for each product.
ORDER BY: Sorts the products by average rating in descending order.
LIMIT 3: Limits the result to the top 3 highest-rated products.
Use Case: Focuses on top-performing products.

### 7. What is the total number of orders placed this month?

```sql
SELECT COUNT(OrderId) AS TotalOrders
FROM `Order`
WHERE MONTH(OrderDate) = MONTH(CURRENT_DATE())
  AND YEAR(OrderDate) = YEAR(CURRENT_DATE());
```

Explanation:
COUNT(): Counts the total number of orders placed this month.
MONTH() and YEAR(): Filters the orders for the current month and year.
Use Case: Tracks monthly order trends for business performance.

### 8. Identify buyers who ordered more than 3 times.

```sql
SELECT u.Name, COUNT(o.OrderId) AS OrderCount
FROM User u
JOIN `Order` o ON u.UserId = o.BuyerId
GROUP BY u.UserId, u.Name
HAVING OrderCount > 3;
```

Explanation:
COUNT(): Counts the number of orders placed by each buyer.
HAVING: Filters buyers who have made more than 3 orders.
Use Case: Identifies frequent buyers for targeted marketing.

### 9. What is the average rating for each product?

```sql
SELECT p.Name, AVG(r.Rating) AS AvgRating
FROM Product p
JOIN Review r ON p.ProductId = r.ProductId
GROUP BY p.ProductId, p.Name;
```

Explanation:
AVG(): Calculates the average rating for each product.
GROUP BY: Aggregates results by product to calculate their average rating.
Use Case: Tracks product performance based on user feedback.

### 10. Show the total revenue generated per month.

```sql
SELECT MONTH(OrderDate) AS OrderMonth, SUM(TotalAmount) AS MonthlyRevenue
FROM `Order`
GROUP BY OrderMonth;
```

Explanation:
SUM(): Sums the total revenue for each month.
GROUP BY: Groups results by month to calculate monthly revenue.
Use Case: Helps forecast future revenues.

### 11. What is the highest-rated seller?

```sql
SELECT u.Name, AVG(r.Rating) AS AvgSellerRating
FROM User u
JOIN Product p ON u.UserId = p.SellerId
JOIN Review r ON p.ProductId = r.ProductId
GROUP BY u.UserId, u.Name
ORDER BY AvgSellerRating DESC
LIMIT 1;
```

Explanation:
AVG(): Averages the ratings of a seller's products.
ORDER BY: Sorts the sellers by average rating in descending order.
LIMIT 1: Returns only the highest-rated seller.
Use Case: Recognizes top-performing sellers.

### 12. List products with no reviews.

```sql
SELECT p.Name
FROM Product p
LEFT JOIN Review r ON p.ProductId = r.ProductId
WHERE r.ReviewId IS NULL;
```

Explanation:
LEFT JOIN: Includes all products, even those with no reviews.
WHERE r.ReviewId IS NULL: Filters to include products that have no reviews.
Use Case: Focuses on improving underperforming products.

### 13. Identify products that have sold more than 50 units.

```sql
SELECT p.Name, SUM(oi.Quantity) AS TotalSold
FROM Product p
JOIN OrderItem oi ON p.ProductId = oi.ProductId
GROUP BY p.ProductId, p.Name
HAVING TotalSold > 50;
```

Explanation:
SUM(): Sums the quantity sold for each product.
HAVING: Filters to include only products with more than 50 units sold.
Use Case: Identifies high-demand products.

### 14. What is the total number of products listed by sellers with no orders?

```sql
SELECT COUNT(p.ProductId) AS NoOrdersProducts
FROM Product p
LEFT JOIN OrderItem oi ON p.ProductId = oi.ProductId
WHERE oi.OrderId IS NULL;
```

Explanation:
LEFT JOIN: Includes all products, even those with no orders.
WHERE oi.OrderId IS NULL: Filters products with no associated orders.
Use Case: Useful for seller feedback on improving product appeal.

### 15. Calculate the average order value (AOV) for the platform.

```sql
SELECT AVG(TotalAmount) AS AvgOrderValue
FROM `Order`;
```

Explanation:
AVG(): Calculates the average value of all orders.
Use Case: Measures business health via order value trends.

### 16. Find all products that received a 5-star review.

```sql
SELECT DISTINCT p.Name
FROM Product p
JOIN Review r ON p.ProductId = r.ProductId
WHERE r.Rating = 5;
```

Explanation:
DISTINCT: Ensures that each product is listed only once, even if it has multiple 5-star reviews.
WHERE r.Rating = 5: Filters for 5-star reviews.
Use Case: Highlights exceptionally rated products.

### 17. Which buyer has spent the most money?

```sql
SELECT u.Name, SUM(o.TotalAmount) AS TotalSpent
FROM User u
JOIN `Order` o ON u.UserId = o.BuyerId
GROUP BY u.UserId, u.Name
ORDER BY TotalSpent DESC
LIMIT 1;
```

Explanation:
SUM(): Sums up the total amount spent by each buyer.
ORDER BY: Orders buyers by total spent, in descending order.
LIMIT 1: Returns only the buyer who has spent the most.
Use Case: Identifies top buyers for loyalty rewards.

## Summary of Key SQL Functions and Clauses Used in the Queries

- **GROUP BY**: Used to aggregate results by a specific column (e.g., by user, by product).
- **SUM()**: Used to calculate the total value, such as total sales or total revenue.
- **COUNT()**: Used to count occurrences, such as the number of products listed or the number of reviews.
- **AVG()**: Used to calculate the average of a numeric column (e.g., average price or rating).
- **HAVING**: Used to filter groups of data after aggregation, typically combined with GROUP BY.
- **JOIN and LEFT JOIN**: Used to combine rows from two or more tables based on related columns. LEFT JOIN includes rows with no matching records in the other table.
- **ORDER BY**: Sorts results in ascending or descending order.
- **LIMIT**: Restricts the number of rows returned by a query.

## Practical Tips

- **Use GROUP BY with Aggregation Functions**: When you need to summarize data, such as calculating totals or averages, use GROUP BY with aggregation functions like SUM(), COUNT(), or AVG().
- **Efficient Filtering with HAVING**: When filtering grouped data, use HAVING instead of WHERE. HAVING works after aggregation, while WHERE works before aggregation.
- **Use LEFT JOIN for Incomplete Data**: Use LEFT JOIN when you need to include rows that may not have matching data in the related table (e.g., products with no reviews).
- **Limit Results for Key Insights**: Use LIMIT when you're only interested in the top results, such as top-rated products or best-selling items.
- **Sort with ORDER BY**: Always sort your results when you need to display them in a specific order (e.g., by total amount spent or average rating).

With these queries and techniques, you can efficiently manage and analyze an e-commerce database to gain insights into sales trends, customer behavior, product performance, and more.
