# SQL-Data-Analysis
Using sql queries, this project explores relational databases and performs analysis using sql queries to answer specific business questions.

## Steps
1. Import the database from sql files and explore the SQL database entities schema to organize and connect relationships between tables
2. Check/Assign primary keys, NOT NULL/DEFAULT values, dataframe(rows, columns, business context) of each table.
3. Create SQL queries to clean, organize and extract series of data for analytical purpose.


## Lists of tablebase
* ['cfpb_compliants 2500'](https://github.com/Shuangyi-im/sql-data-analysis/blob/74b6cb7d75a3c902005f7e15cea8448b7e69f86b/databases/cfpb_compliants%202500.sql)  has 2500 rows and 15 columns. Consumer Financial Protection Bureau (CFPB) launched a public Consumer Complaint Database on credit cards, mortgages, private student loan etc. The data are from [Consumer Complaint Database](https://www.consumerfinance.gov/data-research/consumer-complaints/) of an official website of the US government, recording the complaint issues on banking activities between 2011-01-01 and 2021-12-31.

 
* 'tripadvisor_hotel_sample' and 'tripadvisor_data_for_handson_assignment_ONLY' are from online tripadvisor.com reviews of hotels (Hotel Location: USA; accommodation Year: 2012) and the lists of hotel samples showing in the reviews. 

* 'sql_store' database is provided by [CodeWithMosh>Complete SQL Mastery course](https://codewithmosh.com/p/complete-sql-mastery), which includes seven tables: customers,order_item_notes,order_items,orders,order_statuses,products,shippers.<br>
_For data safety reasons, I have only uploaded the 'cfpb_compliants 2500' data file, which is publicly accessible, and included a partial customer table from the 'sql_store' database._


## Performing analysis on 'cfpb_compliants 2500'

**Task 1: please count how many empty values there are in the column of Sub_product at the cfpb_complaints_2500; Then replace these empty value with NULL**<br>
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
**Question 1: in TripAdvisor dataset, which location (locality) receives the largest amount of hotel reviews and which one receives the lowest amount of reviews?**
```sql
select b.locality, count(a.id) as fre from
tripadvisor_data_for_handson_assignment_ONLY a 
join tripadivsor_hotel_sample b 
using(hotel_id)
group by b.locality 
order by fre desc
```

**Question 2: please calculate the number of the reviews that hotels received, and only consider those hotels that received reviews having at least one review of an overall rating of 5, 3 and 1, respectively. If all the reviews given to a hotel XXX ONLY having an overall rating of either 4 or 5, it will be excluded, because it does not have any reviews offering an overall rating of 3 or 1.**
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

**Question 3: please calculate the proportions of reviews giving an overall rating of 5 and 3 and 1 respectively, and only consider those hotels that received reviews having at least one review of an overall rating of 5, 3 and 1, respectively. Excluding those hotels with less than 10 reviews from the analysis. Please obtain following result via one command**
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


## Explore the data modelling, and perform analysis on sql_store database through sql syntax
### Data modelling for sql_store
When importing the database from sql files, the model has been pre-designed and embedded in the current schema Diagram. Therefore, I used Database → Reverse Engineer to visualize the entities and their relationships, in order to understand the concepts and logics behind the diagram.

![sql_store_data modelling](https://user-images.githubusercontent.com/78413872/230797482-bfa168ef-5857-4a55-8e74-400805ffb00c.png)

**Question 1: we need to get insights about the total sales&profits by each customer, here the info we need are stored on three tables: _customers, orders,order_items_.**

```sql
select 
    c.customer_id,
    c.first_name,
    c.last_name,
    sum(oi.quantity * oi.unit_price) as total_sales,
    sum((oi.unit_price - p.unit_price)*oi.quantity) as profit
from customers as c
join orders as o using(customer_id)
join order_items as oi using(order_id)
group by 
    c.customer_id,
    c.first_name,
    c.last_name
```

**Question 2: if our manager wants to see a list of customers who have purchased lettuce(product_id =3), including customer id, first name and last name.**<br>
```sql
select distinct customer_id, first_name, last_name
from customers
left join orders USING (customer_id)
left join order_items USING (order_id)
where product_id = 3
```

**Question 3: find those products that are never been purchased from the _products_ table**
```sql
select *
from products
where product_id not in(
select distinct product_id
from order_items
```

**Question 4: Classify customers into three categories: 'Bronze', 'Silver', 'Gold' based on their points:<2k、 2k~3k、>3k respectively.**
```sql
SELECT
    CONCAT(first_name, ' ', last_name) AS customer,
    points,
    CASE
        WHEN points < 2000 THEN 'Bronze'
        WHEN points BETWEEN 2000 AND 3000 THEN 'Silver'
        WHEN points > 3000 THEN 'Gold'
        -- ELSE null
    END AS category
FROM customers
ORDER BY points DESC
```
![sql-q4-github-customer points](https://user-images.githubusercontent.com/78413872/230854215-7ca7dd8b-bd67-4dca-a222-0ee84c343708.png)


