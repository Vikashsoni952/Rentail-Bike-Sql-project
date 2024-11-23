
# Rental Bike PostgreSQL Project

## Project Overview

This project involves designing and implementing a database system for a **bike rental company** using PostgreSQL. The system will store data related to customers, bikes, rental orders, and bike usage statistics. The objective is to create a robust database that enables the analysis of bike rental patterns, customer behavior, and bike availability.

### Key Features:
- **Comprehensive Data Structure**: This project includes tables for managing customer information, bike inventory, rental transactions, and bike usage details.
- **Data Analytics**: SQL queries will be executed to analyze rental trends, bike usage efficiency, customer activity, and revenue generation.

## Database Schema

The database schema consists of the following key tables:

1. **Customers**:
    - Contains customer details, including name, contact information, and registration details.
    - Primary Key: `customer_id`

2. **Bikes**:
    - Stores information about bikes, including model, type, and availability status.
    - Primary Key: `bike_id`

3. **Rentals**:
    - Records the rental transactions, including the customer, bike, rental start and end times, and rental cost.
    - Primary Key: `rental_id`
    - Foreign Keys: `customer_id` references `Customers(customer_id)`, `bike_id` references `Bikes(bike_id)`

4. **Usage_Statistics**:
    - Tracks bike usage statistics, including the distance covered, duration of use, and maintenance records.
    - Primary Key: `usage_id`
    - Foreign Key: `bike_id` references `Bikes(bike_id)`

### Entity-Relationship Diagram (ERD)

- **Customers** and **Rentals**: One-to-Many relationship (one customer can make many rentals).
- **Bikes** and **Rentals**: One-to-Many relationship (one bike can be rented out multiple times).
- **Bikes** and **Usage_Statistics**: One-to-One relationship (each bike has corresponding usage stats).

## Table Structures

### Customers Table:
```sql
CREATE TABLE Customers (
    customer_id SERIAL PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    phone VARCHAR(15),
    address VARCHAR(255),
    city VARCHAR(50),
    state VARCHAR(50),
    zip_code VARCHAR(10),
    registration_date DATE
);
```

### Bikes Table:
```sql
CREATE TABLE Bikes (
    bike_id SERIAL PRIMARY KEY,
    model VARCHAR(100),
    type VARCHAR(50),
    status VARCHAR(20), -- e.g., 'Available', 'In Maintenance', 'Rented'
    purchase_date DATE
);
```

### Rentals Table:
```sql
CREATE TABLE Rentals (
    rental_id SERIAL PRIMARY KEY,
    rental_start TIMESTAMP,
    rental_end TIMESTAMP,
    customer_id INT REFERENCES Customers(customer_id),
    bike_id INT REFERENCES Bikes(bike_id),
    rental_cost DECIMAL(10, 2)
);
```

### Usage_Statistics Table:
```sql
CREATE TABLE Usage_Statistics (
    usage_id SERIAL PRIMARY KEY,
    bike_id INT REFERENCES Bikes(bike_id),
    distance_covered DECIMAL(10, 2), -- in kilometers
    duration INT, -- in minutes
    maintenance_needed BOOLEAN,
    last_maintenance_date DATE
);
```

## Data Insertion

Sample data for customers, bikes, rentals, and usage statistics are included to simulate real-world scenarios for the bike rental company.

Example data insertion:
```sql
-- Insert sample customers
INSERT INTO Customers (first_name, last_name, email, phone, address, city, state, zip_code, registration_date)
VALUES
('Alex', 'Johnson', 'alex.johnson@xyz.com', '555-4321', '789 Maple St', 'Denver', 'CO', '80205', '2022-06-15'),
('Emily', 'Davis', 'emily.davis@xyz.com', '555-8765', '321 Oak St', 'Denver', 'CO', '80205', '2022-07-20');

-- Insert sample bikes
INSERT INTO Bikes (model, type, status, purchase_date)
VALUES
('Trek FX 3', 'Hybrid', 'Available', '2021-03-01'),
('Cannondale Quick 4', 'Fitness', 'Available', '2021-06-10');

-- Insert sample rentals
INSERT INTO Rentals (rental_start, rental_end, customer_id, bike_id, rental_cost)
VALUES
('2023-08-01 10:00:00', '2023-08-01 12:30:00', 1, 1, 25.50),
('2023-08-02 09:00:00', '2023-08-02 11:00:00', 2, 2, 22.00);

-- Insert sample usage statistics
INSERT INTO Usage_Statistics (bike_id, distance_covered, duration, maintenance_needed, last_maintenance_date)
VALUES
(1, 15.2, 150, FALSE, '2023-07-20'),
(2, 20.0, 180, TRUE, '2023-07-18');
```

## Key SQL Queries

### 1. **Find Active Rentals**
This query retrieves all active rentals that have not yet ended.
```sql
SELECT * FROM Rentals WHERE rental_end IS NULL;
```

### 2. **Available Bikes for Rent**
This query lists all bikes that are currently available for rent.
```sql
SELECT * FROM Bikes WHERE status = 'Available';
```

### 3. **Customer Rental History**
This query returns the complete rental history of a specific customer.
```sql
SELECT r.rental_start, r.rental_end, b.model, r.rental_cost
FROM Rentals r
JOIN Bikes b ON r.bike_id = b.bike_id
WHERE r.customer_id = 1;
```

### 4. **Total Rental Revenue**
This query calculates the total revenue generated from all bike rentals.
```sql
SELECT SUM(rental_cost) AS total_revenue FROM Rentals;
```

### 5. **Bikes Requiring Maintenance**
This query identifies bikes that need maintenance based on the usage statistics.
```sql
SELECT b.model, u.distance_covered, u.maintenance_needed
FROM Bikes b
JOIN Usage_Statistics u ON b.bike_id = u.bike_id
WHERE u.maintenance_needed = TRUE;
```

### 6. **Average Rental Duration**
This query calculates the average duration of bike rentals.
```sql
SELECT AVG(EXTRACT(EPOCH FROM (rental_end - rental_start)) / 60) AS avg_duration_minutes
FROM Rentals
WHERE rental_end IS NOT NULL;
```

### 7. **Customers with the Most Rentals**
This query lists the top 3 customers based on the number of rentals they have made.
```sql
SELECT c.first_name, c.last_name, COUNT(r.rental_id) AS total_rentals
FROM Customers c
JOIN Rentals r ON c.customer_id = r.customer_id
GROUP BY c.customer_id
ORDER BY total_rentals DESC
LIMIT 3;
```

### 8. **Bike Usage Statistics**
This query retrieves the total distance covered and total time spent on each bike.
```sql
SELECT b.model, SUM(u.distance_covered) AS total_distance, SUM(u.duration) AS total_duration_minutes
FROM Bikes b
JOIN Usage_Statistics u ON b.bike_id = u.bike_id
GROUP BY b.bike_id;
```

## Analysis Use Cases

1. **Rental Activity Monitoring**: Track active rentals, available bikes, and customer rental history.
2. **Bike Fleet Management**: Analyze bike usage statistics, identify bikes requiring maintenance, and optimize bike availability.
3. **Revenue Analysis**: Calculate total revenue, average rental duration, and identify top-paying customers.
4. **Customer Insights**: Identify the most active customers based on rental frequency and total spending.




## Conclusion

The **Rental Bike PostgreSQL Project** provides a detailed and structured database for managing bike rentals. It covers all the essential operations of a bike rental company, including customer management, bike inventory control, and rental transactions. By executing SQL queries on this database, users can derive valuable insights to improve business operations, optimize bike usage, and enhance customer satisfaction.

