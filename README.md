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



