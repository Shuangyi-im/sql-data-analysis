# SQL-Data-analysis
Using sql queries, this project aims to build a schema modell and analyze the relevant data,to answer specific business questions.
Further, a MySQL server data was hooked with Power BI, necessary data analysis and data cleaning was performed.
In the end, all the data was used to build interactive dashboards on Power BI

## Approach
1. Import the database from sql files and explore the SQL database entities schema to organize and connect relationships between tables. Check/Assign primary keys, not-null/default values, dataframe(rows, columns, business context) of each table.

2. Create SQL queries to clean, organize and extract series of data for analytical purpose.

3. Use Power BI to Create visualization on key characteristics leading to higher sales. Built a dashboard to summarize the results.

## Lists of tables
* 'cfpb_compliants 2500'  has 2500 rows and 15 columns. The data are from [Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/) of an official website of US government, recording the complaint issues on banking activities in 2011.
 
* 'tripadvisor_hotel_sample' and 'tripadvisor_data_for_handson_assignment_ONLY' are from online tripadvisor.com reviews of hotels (Location: USA; Year: 2012) and the lists of hotel samples showing in the reviews. 


## Performing analysis on 'cfpb_compliants 2500'

**Task 1: please summarize the amount of complaints group by companies and products**<br>
<br>
```sql 
select Company,Product,count(*) as freq from cfpb_complaints_2500 group by Company,Product
```

