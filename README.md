# KSA Cinema Queries

This repository contains SQL queries and a trigger for analyzing a dataset of cinema halls in Saudi Arabia. The dataset includes information such as cinema names, locations, ratings, and user comments.

## How to Import the Dataset into MySQL
1. Open MySQL Workbench or connect via terminal.
2. Create the `ksa` table if not already created.
3. import the CSV file to ksa table.
  

## Dataset Information
- **Table Name:** `ksa`
- **Columns:** `id`, `name`, `location`, `rating`, `best_comment`

## SQL Queries Included

### 1. List top 5 highest-rated cinema halls
```sql
SELECT name, location, rating FROM ksa ORDER BY rating DESC LIMIT 5;
```
<img width="257" alt="image" src="https://github.com/user-attachments/assets/bbed085c-ad3a-4926-ba0c-ffab8cf9f5b7" />

### 2. Count the number of cinemas in each city
```sql
SELECT location, COUNT(*) AS cinema_count FROM ksa GROUP BY location;
```
<img width="343" alt="image" src="https://github.com/user-attachments/assets/3b038777-460e-47f5-9d55-d50016058860" />

### 3. Find cinemas with ratings below 3
```sql
SELECT name, rating FROM ksa WHERE rating < 3;
```
<img width="213" alt="image" src="https://github.com/user-attachments/assets/7f51ade2-0b7e-4e4b-9c79-f07c3a367769" />

### 4. Get the average rating of cinemas in each city
```sql
SELECT location, AVG(rating) AS avg_rating FROM ksa GROUP BY location;
```
<img width="324" alt="image" src="https://github.com/user-attachments/assets/e2bc352b-e262-4dcb-aa27-141d7ae83538" />

### 5. Find the cinema with the most reviews/comments
```sql
SELECT name, COUNT(best_comment) AS review_count FROM ksa GROUP BY name ORDER BY review_count DESC LIMIT 1;
```
<img width="86" alt="image" src="https://github.com/user-attachments/assets/a60bf750-4f74-4737-a1d5-1cdc4dc8caad" />


### 6. Retrieve all cinemas with keyword 'VOX Cinema' in the name
```sql
SELECT * FROM ksa WHERE name LIKE '%VOX Cinema%';
```
<img width="899" alt="image" src="https://github.com/user-attachments/assets/7e9a1a20-d17d-4210-a9f3-df4cf795aabf" />


### 7. Show locations with more than 3 cinemas
```sql
SELECT location FROM ksa GROUP BY location HAVING COUNT(*) > 3;
```
<img width="260" alt="image" src="https://github.com/user-attachments/assets/87275408-ba3d-4ae5-b7e5-4df46541159d" />

### 8. Get the highest rated cinema in Riyadh
```sql
SELECT name, rating FROM ksa WHERE location LIKE '%Riyadh%' ORDER BY rating DESC LIMIT 5;
```
<img width="205" alt="image" src="https://github.com/user-attachments/assets/80f0852d-1ce2-4711-b9a1-f12d7cefe033" />


### 9. Get the lowest rated cinema in Riyadh
```sql
SELECT name, rating FROM ksa WHERE location LIKE '%Riyadh%' ORDER BY rating ASC LIMIT 5;
```
<img width="205" alt="image" src="https://github.com/user-attachments/assets/c2f3f48e-35e5-40c0-8d94-5292ae48777b" />


### 10. Find the total number of cinemas in the dataset
```sql
SELECT COUNT(*) AS total_cinemas FROM ksa;
```
<img width="107" alt="image" src="https://github.com/user-attachments/assets/56257c3d-bdee-4cfe-bfd3-89baa4088a0e" />


## Bonus Feature: MySQL Trigger
A **MySQL trigger** is implemented to prevent inserting cinemas with a rating above 5.
```sql
CREATE TABLE trigger_log (
    log_id INT AUTO_INCREMENT PRIMARY KEY,
    action_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    action_type VARCHAR(50),
    cinema_name VARCHAR(255),
    rating DECIMAL(2,1),
    message TEXT
);
```

```sql
DELIMITER $$

CREATE TRIGGER prevent_invalid_rating
BEFORE INSERT ON ksa
FOR EACH ROW
BEGIN
    IF NEW.rating > 5 THEN
        -- Log the failed insert attempt
        INSERT INTO trigger_log (action_type, cinema_name, rating, message)
        VALUES ('INSERT ATTEMPT', NEW.name, NEW.rating, 'Rating cannot be above 5');

        -- Prevent insertion
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Rating cannot be above 5';
    END IF;
END$$

DELIMITER ;
```
```sql
INSERT INTO ksa (name, location, rating, best_comment)
VALUES ('Test Cinema', 'Riyadh', 6, 'Testing log');
```
<img width="1000" alt="image" src="https://github.com/user-attachments/assets/e2e736d1-16f5-4843-853a-fc3f4a12e6a9" />




