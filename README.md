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
1. ** Identify and remove missing or null data **:

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
2. ** Identify and remove duplicate data **:
