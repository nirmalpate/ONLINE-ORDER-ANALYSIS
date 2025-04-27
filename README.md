# ONLINE-ORDER-ANALYSIS

I HAVE ANALYSED THE COVID DATA AND PERFORMED THE ONLINE-ORDER-ANALYSIS. I HAVE USED VARIOUS DATA AND PROVIDED INSIGHTS TO STAKEHOLDERS. I HAVE USED BASIC, INERMEDIATE AND ADVANCED SQL.
with Max_Orders as(
SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1
FROM pop.online), Dimention as(
select  order_id, customer_id, gender, Category, Price_1  ,quantity, order_confirmation, Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat, payment_method, max(rnk) over(partition by customer_id) as Max_Or, sum(Price_1) over(partition  by customer_id) as Shop_Customer, sum(quantity) over(partition by customer_id) as Total_quantity
from Max_Orders)
select  *
from Dimention;


with Fact as(
SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1,Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat
FROM pop.online), dim_tab as(
select  order_id, customer_id, gender, category, quantity, order_confirmation, payment_method, Price_1, Dat
from Fact)
select    payment_method, sum(Price_1) as Total_sum
from dim_tab
group by   payment_method;


with Fact as(
SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1,Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat
FROM pop.online), dim_tab as(
select  order_id, customer_id, gender, category, quantity, order_confirmation, payment_method, Price_1, Dat
from Fact)
select    category, sum(Price_1) as Total_sum
from dim_tab
group by   category;


with Max_Orders as(
SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1
FROM pop.online), Dimention as(
select  order_id, customer_id, gender, Category, Price_1  ,quantity, order_confirmation, Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat, payment_method, max(rnk) over(partition by customer_id) as Max_Or, sum(Price_1) over(partition  by customer_id) as Shop_Customer, sum(quantity) over(partition by customer_id) as Total_quantity
from Max_Orders)
select  *, row_number() over(partition by Shop_Customer, Max_Or, Total_quantity) as Indx
from Dimention;

with uniq as(
select *, row_number() over(partition by order_id) as rnk
from pop.online)
select *
from uniq
where rnk = 1;

 select *
 from pop.online
 where payment_method = 'Paypal';

 select *
 from pop.online
 where payment_method = 'Cash On Delivery';
 
  select *
 from pop.online
 where payment_method = 'Debit Card';
 
with Dim as(
 SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1,Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat
FROM pop.online), Final as(
select *, row_number() over(partition by Category order by Price_1 desc) as sp_cat
from Dim)
select *
from Final
where sp_cat = 1;


with Dim as(
 SELECT *, row_number() over(partition by customer_id) as rnk, regexp_replace(Price, '[^0-9]','') as Price_1,Date(str_to_date((order_confirmation_time), '%d-%m-%Y')) as Dat
FROM pop.online), Final as(
select *, row_number() over(partition by payment_method order by Price_1 desc) as sp_cat
from Dim)
select *
from Final
where sp_cat = 1;
