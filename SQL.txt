
-- created a database named Ecommerce
CREATE DATABASE Ecommerce;

-- syntax use is used to add datas to Ecommerce
use Ecommerce;

-- Table customers is created  
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(191) UNIQUE NOT NULL,
    address TEXT
) ENGINE = MyISAM;

-- Table orders is created
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE CASCADE
)ENGINE = MyISAM;

-- Table products is created
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    description TEXT
)ENGINE = MyISAM;

-- we have insert customers name, email and address as values in table customers
INSERT INTO customers (name, email, address)
VALUES 
('Alice Johnson', 'alice@example.com', '123 Elm Street'),
('Bob Smith', 'bob@example.com', '456 Oak Avenue'),
('Charlie Davis', 'charlie@example.com', '789 Pine Road');

-- we have insert products  name, price and description as values in table products
INSERT INTO products (name, price, description)
VALUES 
('Laptop', 1200.00, 'High-performance laptop'),
('Smartphone', 800.00, 'Latest model smartphone'),
('Headphones', 150.00, 'Noise-cancelling headphones');
INSERT INTO products (name, price, description)
VALUES 
('product B', 20.00, 'High-performance product B');

select * from customers;

-- we have insert orders customer_id, order_date and total_amount as values in table orders
INSERT INTO orders (customer_id, order_date, total_amount)
VALUES 
(1, '2024-11-20', 1200.00),  -- Alice placed an order 4 days ago
(2, '2024-10-10', 800.00),  -- Bob placed an order over 30 days ago
(1, '2024-11-01', 1350.00), -- Alice placed another order earlier this month
(3, '2024-11-15', 150.00);  -- Charlie placed an order 9 days ago

-- Question-01
-- SELECT DISTINCT: Retrieves unique rows to avoid duplicates, even if a customer has multiple orders.
-- FROM customers c JOIN orders o ON c.id = o.customer_id: Joins the customers table with the orders table, linking customers to their orders via customer_id.
-- WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY: Filters to include only orders from the past 30 days
-- Retrieve Customers with Orders in the Last 30 Days
SELECT DISTINCT c.id, c.name, c.email, c.address
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

-- Question-02
-- 1. SELECT Clause
-- c.id AS customer_id, c.name AS customer_name: Retrieves the customer’s ID and name, with aliases (customer_id, customer_name) for clarity.
-- SUM(o.total_amount) AS total_order_amount: Calculates the total of all orders (total_amount) for each customer and labels it as total_order_amount.
-- 2. FROM Clause
-- customers c: The customers table, aliased as c.
-- JOIN orders o ON c.id = o.customer_id: Combines customers and orders tables by matching c.id with o.customer_id.
-- 3. GROUP BY Clause
-- c.id, c.name: Groups the data by customer ID and name so the SUM is calculated for each customer.
-- Get the total amount of all orders placed by each customer.
SELECT 
    c.id AS customer_id,
    c.name AS customer_name,
    SUM(o.total_amount) AS total_order_amount
FROM 
    customers c
JOIN 
    orders o ON c.id = o.customer_id
GROUP BY 
    c.id, c.name;
    
-- Question-03
-- SET SQL_SAFE_UPDATES = 0;
-- Disables SQL safe updates.
-- In safe mode, updates or deletes require a WHERE clause with indexed columns or a LIMIT clause to prevent accidental changes to large datasets.
-- UPDATE products SET price = 45.00 WHERE name = 'Product C';
-- Updates the price of a product in the products table to 45.00 where the name is 'Product C'.
-- SET SQL_SAFE_UPDATES = 1;
-- Re-enables SQL safe updates to prevent accidental updates or deletes.   
 
-- Update the price of Product C to 45.00
SET SQL_SAFE_UPDATES = 0;
UPDATE products
SET price = 45.00
WHERE name = 'Product C';
SET SQL_SAFE_UPDATES = 1;

-- Question-04
-- ALTER TABLE products:Specifies the products table to be altered.
-- ADD COLUMN discount DECIMAL(5, 2) DEFAULT 0.00;:Adds a new column named discount.
-- The data type is DECIMAL(5, 2):
-- 5: Maximum of 5 digits (including decimal places).
-- 2: Exactly 2 digits after the decimal point.
-- DEFAULT 0.00: Sets the default value of the discount column to 0.00 for all existing and future rows unless specified otherwise.

-- Add a new column discount to the products table.
ALTER TABLE products
ADD COLUMN discount DECIMAL(5, 2) DEFAULT 0.00;

-- Question-05
-- SELECT id, name, price:Fetches the id, name, and price columns from the products table.
-- FROM products:Specifies the table (products) from which to retrieve data.
-- ORDER BY price DESC:Sorts the rows by the price column in descending order (DESC), so the most expensive products appear first.
-- LIMIT 3:Restricts the result set to the top 3 rows after sorting.

-- Retrieve the top 3 products with the highest price.
SELECT 
    id, name, price
FROM 
    products
ORDER BY 
    price DESC
LIMIT 3;

-- Table order_item is created 
CREATE TABLE order_item(
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL,
    FOREIGN KEY (order_id) REFERENCES ecommerce.orders(id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES products(id) ON DELETE CASCADE
)ENGINE = MyISAM;

select * from orders;
use ecommerce;

-- Question-06
-- SELECT DISTINCT c.name AS customer_name:Fetches unique (DISTINCT) customer names (c.name), with an alias customer_name.
-- FROM customers c:Starts from the customers table, aliased as c.
-- Joins:
-- JOIN orders o ON c.id = o.customer_id: Links customers (c.id) with their orders (o.customer_id).
-- JOIN order_item oi ON o.id = oi.order_id: Links orders (o.id) with items in those orders (oi.order_id).
-- JOIN products p ON oi.product_id = p.id: Links order items (oi.product_id) with the products table (p.id).
-- WHERE p.name = 'Product A':Filters to include only orders that contain 'Product A'

-- Get the names of customers who have ordered Product A. 
SELECT DISTINCT c.name AS customer_name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_item oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE p.name = 'product A';


-- have updated a customer name as product A
SET SQL_SAFE_UPDATES = 0;
UPDATE products
SET name = 'product A'
WHERE name = 'Laptop';
SET SQL_SAFE_UPDATES = 1;
select * from products;

-- Question-07
-- SELECT c.name AS customer_name, o.order_date:Retrieves the customer's name (c.name) and the date of the order (o.order_date).
-- The alias customer_name makes the output more readable.
-- FROM customers c:Specifies the customers table as the main table, aliased as c.
-- JOIN orders o ON c.id = o.customer_id:Joins the customers table with the orders table, linking each customer (c.id) with their respective orders (o.customer_id).

-- Join the orders and customers tables to retrieve the customer's name and order date for each order. 
SELECT 
    c.name AS customer_name,
    o.order_date
FROM 
    customers c
JOIN 
    orders o ON c.id = o.customer_id;
    
-- Question-08
-- SELECT id AS order_id, customer_id, order_date, total_amount:
-- Fetches the following columns from the orders table:
-- id (renamed as order_id for clarity).
-- customer_id (identifies the customer who placed the order).
-- order_date (date of the order).
-- total_amount (total value of the order).
-- FROM orders:Specifies the orders table as the data source.
-- WHERE total_amount > 150.00:Filters to include only rows where the total_amount is greater than 150.00.

-- Retrieve the orders with a total amount greater than 150.00.
SELECT 
    id AS order_id,
    customer_id,
    order_date,
    total_amount
FROM 
    orders
WHERE 
    total_amount > 150.00;
	
-- Question-09
--  INSERT INTO Query
-- This part adds multiple records to the order_item table, associating orders with specific products and their quantities.
-- First Record: Order 1 contains 2 units of Product 1 and 1 unit of Product 2.
-- Second Record: Order 2 contains 1 unit of Product 3.
-- Third Record: Order 3 contains 1 unit of Product 1 and 3 units of Product 2.

-- SELECT Query
-- SELECT o.id AS order_id, c.name AS customer_name, o.order_date, p.name AS product_name, oi.quantity:Selects the order ID, customer name, order date, product name, and quantity of each item in the order.
-- FROM orders o:Specifies the orders table (o) as the main data source.
-- JOIN customers c ON o.customer_id = c.id:Joins the orders table with the customers table to get the customer name associated with each order.
-- JOIN order_item oi ON o.id = oi.order_id:Joins the orders table with the order_item table to link each order to the items it contains.
-- JOIN products p ON oi.product_id = p.id:Joins the order_item table with the products table to get the product name for each order item.

-- Normalize the database by creating a separate table for order items and updating the orders table to reference the order_items table.
INSERT INTO order_item (order_id, product_id, quantity)
VALUES
(1, 1, 2), -- Order 1 contains 2 units of Product 1
(1, 2, 1), -- Order 1 contains 1 unit of Product 2
(2, 3, 1), -- Order 2 contains 1 unit of Product 3
(3, 1, 1), -- Order 3 contains 1 unit of Product 1
(3, 2, 3); -- Order 3 contains 3 units of Product 2

SELECT 
    o.id AS order_id,
    c.name AS customer_name,
    o.order_date,
    p.name AS product_name,
    oi.quantity
FROM 
    orders o
JOIN 
    customers c ON o.customer_id = c.id
JOIN 
    order_item oi ON o.id = oi.order_id
JOIN 
    products p ON oi.product_id = p.id;

-- Question-10
-- SELECT AVG(total_amount) AS average_order_total:
-- AVG(total_amount): Calculates the average value of the total_amount column, which represents the total price of each order.
-- AS average_order_total: Assigns an alias to the result of the AVG function for clarity in the output.
-- FROM orders:-- Specifies the orders table as the source for the total_amount data.

-- Retrieve the average total of all orders.
SELECT AVG(total_amount) AS average_order_total
FROM orders;


 




