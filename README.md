# SQL-Data-analysis
Using sql queries, this project aims to build/explore a schema model and analyze the relevant data,to answer specific business questions.

## Approach
1. Import the database from sql files and explore the SQL database entities schema to organize and connect relationships between tables. Check/Assign primary keys, not-null/default values, dataframe(rows, columns, business context) of each table.

2. Create SQL queries to clean, organize and extract series of data for analytical purpose.

3. Use Power BI to Create visualization on key characteristics leading to higher sales. Built a dashboard to summarize the results.

## Lists of tables
* 'cfpb_compliants 2500'  has 2500 rows and 15 columns. The data are from [Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/) of an official website of US government, recording the complaint issues on banking activities in 2011.
 
* 'tripadvisor_hotel_sample' and 'tripadvisor_data_for_handson_assignment_ONLY' are from online tripadvisor.com reviews of hotels (Hotel Location: USA; accommodation Year: 2012) and the lists of hotel samples showing in the reviews. 


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

![sql-task5+ github](https://user-images.githubusercontent.com/78413872/230776148-7b79fe4c-cc1e-4067-a01b-9bb58b6cba25.png)

## Performing analysis on TripAdvisor reviews
**Question 1: In TripAdvisor dataset, which location (locality) receives the largest amount of hotel reviews and which one receives the lowest amount of reviews?**
```sql
select b.locality, count(a.id) as fre from
tripadvisor_data_for_handson_assignment_ONLY a 
join tripadivsor_hotel_sample b 
using(hotel_id)
group by b.locality 
order by fre desc
```

**Question 2:please calculate the number of the reviews that hotels received, and only consider those hotels that received reviews having at least one review of an overall rating of 5, 3 and 1, respectively. If all the reviews given to a hotel XXX ONLY having an overall rating of either 4 or 5, it will be excluded, because it does not have any reviews offering an overall rating of 3 or 1.**
```sql
select rw5.*, rw3.Num_of_3_star_rating, rw1.Num_of_1_star_rating from (select hotel_id, count(*) as Num_of_5_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 5 group by hotel_id ) rw5
join
(select hotel_id,  count(*) as Num_of_3_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 3 group by hotel_id ) rw3
on rw5.hotel_id = rw3.hotel_id
join
(select hotel_id, count(*) as  Num_of_1_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 1 group by hotel_id ) rw1
on rw1.hotel_id = rw3.hotel_id 
```
![q1-github-sql](https://user-images.githubusercontent.com/78413872/230779379-8ba80d4a-fa47-485a-bfd1-749cf25a1a84.png)

**Question 3:, please calculate the proportions of reviews giving an overall rating of 5 and 3 and 1 respectively, and only consider those hotels that received reviews having at least one review of an overall rating of 5, 3 and 1, respectively. Excluding those hotels with less than 10 reviews from the analysis. Please obtain following result via one command**
```sql
select tb1.hotel_id, totalcount.Num_of_total_review,
tb1.Num_of_5_star_rating/totalcount.Num_of_total_review as ratio_of_5_star_rating,
tb2.Num_of_3_star_rating/totalcount.Num_of_total_review as ratio_of_3_star_rating,
tb3.Num_of_1_star_rating/totalcount.Num_of_total_review as ratio_of_1_star_rating from
(select hotel_id, count(*) as  Num_of_total_review from tripadvisor_data_for_handson_assignment_ONLY  group by hotel_id ) totalcount
join
(select hotel_id, count(*) as Num_of_5_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 5 group by hotel_id ) tb1
on tb1.hotel_id = totalcount.hotel_id
join
(select hotel_id,  count(*) as Num_of_3_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 3 group by hotel_id ) tb2
on tb1.hotel_id = tb2.hotel_id
join
(select hotel_id, count(*) as  Num_of_1_star_rating from tripadvisor_data_for_handson_assignment_ONLY where overall_rating = 1 group by hotel_id ) tb3
on tb3.hotel_id = tb2.hotel_id where totalcount.Num_of_total_review >= 10
order by Num_of_total_review desc;
```
![question3-sql-github](https://user-images.githubusercontent.com/78413872/230779715-0cc51bbc-39c8-4d42-8598-b47cbdce1869.png)


