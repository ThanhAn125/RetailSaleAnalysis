# Retail Sale Analysys
## Objectives
1. **Create Data Base**: Create and populate a retail sales database with the provided sales data.
2. **Data Cleaning**: Identify and remove any records with missing or null or duplicate values. Find outlier in dataset.
3. **Exploratory Data Analysis (EDA)**: Exploratory data analysis to understand the dataset.

## 1 Create DataBase:
Schema:
```sql
Drop table if exists sale_table;
Create Table sale_table(
	transactions_id	int primary key,
	sale_date	date,
	sale_time time,
	customer_id	int,
	gender varchar(10),
	age	int,
	category varchar(15),	
	quantity	int,
	price_per_unit int,	
	cogs int,	
	total_sale int
);

Select * from sale_table;
```
## 2 Data Cleaning
1. Identify and remove missing or null data :

```sql
--Check Null data
Select * 
From sale_table
Where transactions_id is null
	or sale_date is null
	or sale_time is null
	or customer_id is null
	or gender is null
	or age is null
	or category is null
	or quantity is null
	or price_per_unit is null
	or cogs is null
	or total_sale is null
;

--Delete Null data
Delete From sale_table
Where  transactions_id is null
	or sale_date is null
	or sale_time is null
	or customer_id is null
	or gender is null
	or age is null
	or category is null
	or quantity is null
	or price_per_unit is null
	or cogs is null
	or total_sale is null
;
```
2. Identify and remove duplicate data:
```sql
-- Find Duplicate Data row
with Dup_table as(
Select 
	*,
	ROW_NUMBER() over (PARTITION BY 
	sale_date, 
	sale_time, 
	customer_id, 
	gender,
	age,
	category,
	quantity,
	price_per_unit,
	cogs,
	total_sale
	ORDER BY
	transactions_id
	) as row_num
from sale_table
)
select * from Dup_table
where row_num >= 2
;
-- No Duplicate row
```
3. Find outlier in dataset:
```sql
-- Check total row
Select count(*) from sale_table; -- 1987 row total

-- Check outlier of Age column

with quartile as (
select 
	quartile,
	max(age) as quartile_value
from (
	select 
	age,
	ntile(4) over (order by age) as quartile
	from sale_table) as quartiles
where quartile in (1,3)
group by quartile 
), -- Create quartile of value and find q3 , q1
irl as(
	select max(quartile_value) as q3, min(quartile_value) as q1,(max(quartile_value) - min(quartile_value)) as irl 
	from quartile
) -- Create irl = q3 - q1
select age
from sale_table
where age > (select q3 + 1.5*irl from irl) -- Check outlier over 1.5 * irl
	or age < (select q1 - 1.5*irl from irl)-- Check outlir under 1.5 * irl

-- No outlier in Age column


-- Check outlier Total_sale column
with quartile as (
select 
	quartile,
	max(total_sale) as quartile_value
from (
	select 
	total_sale,
	ntile(4) over (order by total_sale) as quartile
	from sale_table) as quartiles
where quartile in (1,3)
group by quartile
),-- Create quartile of value and find q3 , q1
irl as(
	select max(quartile_value) as q3, min(quartile_value) as q1,(max(quartile_value) - min(quartile_value)) as irl 
	from quartile
)-- Create irl = q3 - q1
select total_sale
from sale_table
where total_sale > (select q3 + 1.5*irl from irl)
	or total_sale < (select q1 - 1.5*irl from irl)
-- No outlier in Total_sale column
```

## 3 Exploratory Data Analysis (EDA):

Q.1 Write a SQL query to retrieve all columns for sales made on '11/05/2022'
```sql
Select total_sale 
From sale_table
where sale_date like '%11/05/2022'
;
```
Q.2 Write a SQL query to retrieve all transactions where the category is 'Clothing' and the quantity sold is more than 2 in the month of Nov-2022
```sql
Select * 
From sale_table
Where category = 'Clothing' and quantity > 2
;
```
Q.3 Write a SQL query to calculate the total sales (total_sale) for each category.
```sql 
Select category, sum(total_sale) 
From sale_table
group by category
;
```
```sql 
Q.4 Write a SQL query to find the average age of customers who purchased items from the 'Beauty' category.
Select avg(age) 
From sale_table
Where category like '%beauty%'
;
```

Q.5 Write a SQL query to find all transactions where the total_sale is greater than 1000.
```sql 
Select * 
From sale_table
Where total_sale > 1000
;
```

Q.6 Write a SQL query to find the total number of transactions (transaction_id) made by each gender in each category.
```sql 
Select gender,Count(*) as TotalOrder
From sale_table
Group by gender
;
```

Q.7 Write a SQL query to calculate the average sale for each month. Find out best selling month in each year
```sql 
With T1 as (
Select Datepart(yyyy,sale_date) as Years,
	   Datepart(mm,sale_date) as Months,
	   Avg(total_sale) as Avg_sale,
	   Rank() over (Partition by Datepart(yyyy,sale_date) Order by avg(total_sale) DESC) as Ranks
from sale_table
Group by Datepart(yyyy,sale_date),Datepart(mm,sale_date) )
Select 
	*
from T1
Where Ranks = 1
;
```

Q.8 Write a SQL query to find the top 5 customers based on the highest total sales 
```sql 
Select top(5) 
	customer_id, 
	sum(total_sale) as totalsale
From sale_table
Group by customer_id
Order by totalsale DESC
;
```

Q.9 Write a SQL query to find the number of unique customers who purchased items from each category.
```sql 
Select category, 
	count(distinct(customer_id)) as TotalCustomerPurchased
From sale_table
Group by category
;
```

Q.10 Write a SQL query to create each shift and number of orders (Example Morning <=12, Afternoon Between 12 & 17, Evening >17)
```sql 
With hourly_sale 
as
(
Select
	*,
	Case
	when datepart(hh,sale_time) <=12 then 'Morning'
	when datepart(hh,sale_time) BETWEEN 12 and 17 then 'Afternoon'
	when datepart(hh,sale_time) >17 then 'Evening'
	End as DateShift
From sale_table)
Select 
	DateShift,
	Count(*) as TotalSale
from hourly_sale
Group by DateShift
;
```



