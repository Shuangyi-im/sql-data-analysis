# SQL-Data-analysis
Using sql queries, this project aims to build a schema modell and analyze the relevant data,to answer specific business questions.<br>Further, a MySQL server data was hooked with Power BI, necessary data analysis and data cleaning was performed.<br>In the end, all the data was used to build interactive dashboards on Power BI

## Approach
1. Import the database from sql files and explore the SQL database entities schema to organize and connect relationships between tables. Check/Assign primary keys, not-null/default values, dataframe(rows, columns, business context) of each table.

2. Create SQL queries to clean, organize and extract series of data for analytical purpose.

3. Use Power BI to Create visualization on key characteristics leading to higher sales. Built a dashboard to summarize the results.

## Lists of tables
* 'cfpb_compliants 2500'  has 2500 rows and 15 columns. The data are from [Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/) of an official website of US government, recording the complaint issues on banking activities in 2011.
 
* 'tripadvisor_hotel_sample' and 'tripadvisor_data_for_handson_assignment_ONLY' are from online tripadvisor.com reviews of hotels (Location: USA; Year: 2012) and the lists of hotel samples showing in the reviews. 


## Performing analysis on 'cfpb_compliants 2500'

**Task 1: please count how many empty values there are in the column of Sub_product at the cfpb_complaints_2500; Then eeplace these empty value with NULL**<br>
```sql
Select count(*) from cfpb_complaints_2500 where Sub_product = '';
Update cfpb_complaints_2500 set Sub_product = NULL where Sub_product = '';
```

**Task 2: please produce a list of different companies and products, and count their frequencies(of received compliants) respectively**<br>
```sql 
select Company,Product,count(*) as freq from cfpb_complaints_2500 group by Company,Product;
```
![task1-sql](https://user-images.githubusercontent.com/78413872/230774478-e7ab97bf-3e38-4999-8c48-098393e0359d.png)


**Task 3: which month/weekday did the companies receive the largest amount of complaints?**<br>
```sql
/*month*/
select monthname(Data_received), count(Data_received) as freq
from cfpb_complaints_2500 
group by monthname(Data_received) 
order by freq desc;
/*weekday*/
Select dayname(Data_received), count(Data_received)as freq 
from cfpb_complaints_2500 
group by dayname(Data_received) 
order by freq desc;

```
![task3-sql-github](https://user-images.githubusercontent.com/78413872/230773103-389820ef-127e-417f-b4e2-ee3a50f73236.png)

**Task 4: please retrieve the records in which Issue are related to loan, saving or credit**<br>
```sql
select * from cfpb_complaints_2500
where Issue regexp 'loan|saving|credit';
```

**Task 5: count the frequencies of different Issues for different companies and show the starting date and ending date that different issues have been reported, and order the records based on first the company name from A-Z and then on frequencies in a descending manner. Save the results to a new table “Count_result"**<br>
```sql
select Company,Issue,count(*) as freq_issue,
min(Data_received) as starting_day,max(Data_sent_to_company) as ending_day
from cfpb_complaints_2500
group by Company,Issue
order by Company asc,freq_issue desc;
```
![task 5-sql-github](https://user-images.githubusercontent.com/78413872/230774417-ec59d8b7-ed96-48b8-b99a-d6be8d76e153.png)

**Task 5+: based on the results in the new table"Count_result", create a report showing<br> _which company has issues related to what for how many time(s) during date1 and date2_**

```sql
select GROUP_CONCAT('The company - ', Company, ' have issues related to ', Issue, ' for ', freq_issue, ' time(s) during ', starting_day, ' and ', ending_day, '.') AS sentence
from Count_result
group by Company, Issue
```
![sql-task5+](https://user-images.githubusercontent.com/78413872/230775840-18926e89-e1b1-4c3c-b028-fe3d9c882f99.png)

