# Shopping Trends
![Shopping Trends Logo](https://github.com/ctian5505/Shopping_Trends/blob/main/Online-Shopping-Trends-What-Do-Recent-Reports-Suggest-About-Online-Shopper-Behavior-Globally-3.jpg)

## I ask ChatGPT to generate business questions about the data.
1. High-Value Customers Report : "Find the top 10 customers who have spent the most. Include their customer ID, total purchase amount, and total number of purchases."

2. Subscription vs. Non-Subscription Spending : "Compare the average purchase amount of subscribed vs. non-subscribed customers."

3. Most Popular Product Categories by Gender : "Show the top 3 most purchased product categories for each gender."

4. Discount Impact Analysis : "Find out if discounts drive higher spending. Compare the average purchase amount of customers who used a discount vs. those who didn’t."

5. Seasonal Purchase Trends : "Show the total revenue generated per season to identify the most profitable shopping period."

6. Payment Method Preferences : "Identify the most frequently used payment method for purchases and compare it with customers' preferred payment methods."

7. Shipping Type vs. Purchase Amount : "Analyze whether customers who choose express shipping tend to spend more compared to those using standard shipping."

8. Product Color Popularity : "Rank the top 5 most frequently purchased colors."
___

## Solution
```sql
-- 1. High-Value Customers Report : "Find the top 10 customers who have spent the most. Include their customer ID, total purchase amount, and total number of purchases."

SELECT TOP 10 
	customer_id,
	SUM(purchase_amount_usd) AS [total_purchase_amount],
	COUNT(customer_id) AS [total_number_purchase]
FROM
	sales_trend
GROUP BY
	customer_id
ORDER BY
	 [total_purchase_amount] DESC
```

```sql
-- 2. Subscription vs. Non-Subscription Spending : "Compare the average purchase amount of subscribed vs. non-subscribed customers."

SELECT 
    CASE 
        WHEN subscription_status = 'Yes' THEN 'Subscribed'
        WHEN subscription_status = 'No' THEN 'Non-Subscribed'
        ELSE 'null'
    END AS subscribed_status,  
    AVG(purchase_amount_usd) AS average_purchase_amount
FROM 
	sales_trend
GROUP BY 
	CASE 
        WHEN subscription_status = 'Yes' THEN 'Subscribed'
        WHEN subscription_status = 'No' THEN 'Non-Subscribed'
        ELSE 'null'
    END
```

```sql
--3. Most Popular Product Categories by Gender : "Show the top 3 most purchased product categories for each gender."

WITH CTE as (
	SELECT
		gender,
		category,
		count(category) AS [purchased_count],
		RANK() OVER(PARTITION BY gender ORDER BY count(category) DESC) AS [Rank]
	FROM
		sales_trend
	GROUP BY
		gender,
		category
)
SELECT 
	* 
FROM 
	CTE
WHERE
	Rank <= 3

```

```sql
--4. Discount Impact Analysis : "Find out if discounts drive higher spending. Compare the average purchase amount of customers who used a discount vs. those who didn’t."

SELECT 
	CASE
		WHEN discount_applied = 'Yes' THEN 'With Discount'
		WHEN discount_applied = 'No' THEN 'No Discount'
		ELSE 'null'
	END AS [discount_status],
	AVG(purchase_amount_usd) AS [average purchase]
FROM
	sales_trend
GROUP BY
	CASE
		WHEN discount_applied = 'Yes' THEN 'With Discount'
		WHEN discount_applied = 'No' THEN 'No Discount'
		ELSE 'null'
	END

```

```sql
--5. Seasonal Purchase Trends : "Show the total revenue generated per season to identify the most profitable shopping period."

SELECT
	season,
	SUM(purchase_amount_usd) AS [revenue]
FROM
	sales_trend
GROUP BY
	season
ORDER BY
	SUM(purchase_amount_usd)

```

```sql
--6. Payment Method Preferences : "Identify the most frequently used payment method for purchases and compare it with customers' preferred payment methods."

WITH CTE_1 AS (
	SELECT
		payment_method AS [mode_of_payment],
		COUNT(payment_method) AS [payment_method_count],
		RANK() OVER (ORDER BY COUNT(payment_method) DESC) AS [payment_method_rank]
	FROM 
		sales_trend
	GROUP BY
		payment_method
),
CTE_2 AS (
	SELECT
	preferred_payment_method AS [preferred_payment_method],
	COUNT(preferred_payment_method) AS [preferred_payment_method_count],
	RANK() OVER (ORDER BY COUNT(preferred_payment_method) DESC) AS [preferred_payment_method_rank]
FROM 
	sales_trend
GROUP BY
	preferred_payment_method

)

SELECT 
	CTE_1.mode_of_payment,
	CTE_1.payment_method_count,
	CTE_1.payment_method_rank,
	CTE_2.preferred_payment_method_count,
	CTE_2.preferred_payment_method_rank
FROM
	CTE_1
LEFT JOIN
	CTE_2
ON
	CTE_1.mode_of_payment = CTE_2.preferred_payment_method
```

```sql
--7. Shipping Type vs. Purchase Amount : "Analyze whether customers who choose express shipping tend to spend more compared to those using standard shipping."

SELECT
	shipping_type,
	AVG(purchase_amount_usd) AS [average_spending]
FROM
	sales_trend
GROUP BY
	shipping_type
ORDER BY
	AVG(purchase_amount_usd) DESC

```

```sql
--8. Product Color Popularity : "Rank the top 5 most frequently purchased colors."

WITH CTE AS (
	SELECT
		color,
		COUNT(color) AS [color_count],
		RANK() OVER(ORDER BY COUNT(color) DESC) AS [rank_of_frequently_purchased_colors]
	FROM
		sales_trend
	GROUP BY
		color
		)
SELECT
	*
FROM
	CTE
WHERE 
	rank_of_frequently_purchased_colors <= 5
*/
```
