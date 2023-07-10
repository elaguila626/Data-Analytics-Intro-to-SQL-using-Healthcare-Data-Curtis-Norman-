# Healthcare Data SQL Practice Questions

### How many rows of data are in the FactTable that include a Gross Charge greater than $100? 
-- Use a SELECT statement to grab the first and last name names of every customer and their email customer. 


### SELECT DISTINCT
-- Retrieve the distinct rating types our films could have in our database.

SELECT DISTINCT rating FROM film; 

### SELECT WHERE 
-- A customer forgot their wallet we need to track them down. What is the email for the customer Nancy Thomas?

SELECT email FROM customer
<br>WHERE first_name = 'Nancy'
<br>AND last_name = 'Thomas';

-- A customer wants to know the description for the movie "Outlaw Hanky".

SELECT description FROM film
<br>WHERE title = 'Outlaw Hanky';

-- A customer is late on a movie return. Can you get the customer that lives at '259 Ipoh Drive'?

SELECT phone FROM address
<br> WHERE address = '259 Ipoh Drive';

### ORDER BY
-- We want to reward the first 10 paying customers. What are the customer id's for the first 10 customers that who created a payment?

SELECT customer_id FROM payment
<br> ORDER BY payment_date ASC
<br> LIMIT 10; 

-- A customer wants to quickly rent a video to watch over their short lunch break. What are the titles of the 5 shortest (length or runtime) movies?

SELECT title, length FROM film
<br> ORDER BY length ASC
<br> LIMIT 5;

-- If the previous customer can watch any movie that is 50 min or less in run time, how many options do they have?

SELECT COUNT (title) FROM film
<br> WHERE length <= 50;

## GENERAL CHALLENGE 1

-- How many payment transactions were greater than $5.00?

SELECT COUNT (amount) FROM payment
<br> WHERE amount > 5.00;

-- How many actors have a first name that starts with the letter P?

SELECT COUNT(*) FROM actor 
<br> WHERE first_name LIKE 'P%';

--How many unique districts are our customers from?

SELECT COUNT (DISTINCT(district)) -- SELECTing the COUNT of each DISTINCT district -- 
<br> FROM address 

-- Retrieve the list of names for those distinct districts from the previous question

SELECT DISTINCT(district) 
<br> FROM address;

-- How many films have a rating of R and a replacement cost between $5 and $15?
     
SELECT COUNT(*) FROM film
<br> WHERE rating = 'R'
<br> AND replacement_cost BETWEEN 5 AND 15;

-- How many films have the word Truman somewhere in the title?

SELECT COUNT(*) FROM film
<br> WHERE title LIKE '%Truman%';

