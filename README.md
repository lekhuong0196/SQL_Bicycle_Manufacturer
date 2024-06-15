# SQL_Bicycle_Manufacturer
## 1.	Introduction

Using SQL in Big Query to collect, organize, and connect data from dataset of AdventureWorks2019 to calculate accurate information for different requirements

## 2.	The business questions that needed to solve:

1.	Quantity of items, Sales value & Order quantity by each Subcategory in L12M

2.	Percentage of year over year growth rate by SubCategory & release top 3 cat with highest grow rate

3.	Top 3 TeritoryID with biggest Order quantity of every year. If there's TerritoryID with same quantity in a year

4.	Total Discount Cost belongs to Seasonal Discount for each SubCategory

5.	Retention rate of Customer in 2014 with status of Successfully Shipped (Cohort Analysis)

6.	Trend of Stock level & month over month different percentage by all products in 2011

7.	Ratio of Stock / Sales in 2011 by product name, by month

8.	Number of order and value at Pending status in 2014

## 3.	Exploring the Dataset

In this project, I will write 08 query in Bigquery base on Google Analytics dataset

**Q1: Calc Quantity of items, Sales value & Order quantity by each Subcategory in L12M ELECT distinct**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/d936c25e-32ed-45bc-85e1-0433661d0a0d)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/84be4b6f-ff7e-41a6-8e22-f967f507a66a)

**Q2: Calc % YoY growth rate by Category & release top 3 cat with highest grow rate. Can use metric: quantity_item. Round results to 2 decimal**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/f913f14d-90e8-4f46-bc8c-65f5a770fb86)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/41e3c0b3-4dc4-4deb-968b-ba9c0b255213)

**Q3: Ranking Top 3 TeritoryID with biggest Order quantity of every year. If there's TerritoryID with same quantity in a year, do not skip the rank number**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/c4ebdc67-076d-4ca4-adbc-dd60845c8d0c)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/bc418e68-f9cc-43f1-a175-b3930528d8a2)

**Q4: Calc Total Discount Cost belongs to Seasonal Discount for each SubCategory**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/57154974-6f3b-44d3-bda8-495d9beeaf7f)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/1313b96c-94cb-489d-8f18-81c4ca65517b)

**Q5: Retention rate of Customer in 2014 with status of Successfully Shipped (Cohort Analysis)**

```
with info as (
select distinct 
 extract(month from ModifiedDate) as month_no
 ,  extract(year from ModifiedDate) as year_no
, CustomerID
, count(Distinct SalesOrderID) as order_cnt

from `adventureworks2019.Sales.SalesOrderHeader`
where FORMAT_TIMESTAMP("%Y", ModifiedDate) = '2014'
and Status = 5
group by 1,2,3
order by 3,1 
)
, row_num as (
select *
, row_number() over (partition by CustomerID order by month_no) as row_numb
from info 
)
, first_order as (
select *
  from row_num
  where row_numb = 1
)
, month_gap as (
select a.CustomerID
, b.month_no as month_join
, a.month_no as month_order
, a.order_cnt
, concat('M - ',a.month_no - b.month_no) as month_diff
from info a 
left join first_order b on a.CustomerID = b.CustomerID
order by 1,3
)
select month_join
, month_diff 
, count(distinct CustomerID) as customer_cnt
from month_gap
group by 1,2
order by 1,2
;
```
**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/cee98c2e-4de2-4ee7-a34a-82d981b728f4)

**Q6:Trend of Stock level & MoM diff % by all product in 2011. If %gr rate is null then 0. Round to 1 decimal**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/c9305b07-5afc-4315-92b7-a3c2f406dfef)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/65075696-c32e-4e67-ae30-b4ca898ada1a)

**Q7: Calc MoM Ratio of Stock / Sales in 2011 by product name**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/93771bf0-e2e3-434e-82a5-4d21cc969610)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/675b0d66-6dda-4b63-9927-4eeb0421d073)

**Q8: No of order and value at Pending status in 2014**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/774d72de-a1ba-48e2-92db-d2b30afd640b)

**Query results**

![image](https://github.com/lekhuong0196/SQL_Bicycle_Manufacturer/assets/138196501/661e1b2b-b459-4fe8-9fda-b1518b9e8f36)

## 4. Conclusion

The dataset offers valuable insights into customer behavior, product performance, marketing effectiveness, supply chain optimization, and sales forecasting. Users can segment customers, analyze product trends, assess marketing campaigns, identify inventory bottlenecks, and develop data-driven sales forecasts.

Overall, AdventureWorks2019 is a versatile tool for those interested in relational databases, SQL, and business data analysis. Its strengths lie in its comprehensive coverage, diverse data points, and ease of use. By acknowledging the limitations, users can gain valuable knowledge and practical skills from this dataset.


