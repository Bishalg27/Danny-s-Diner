# Danny-s-Diner

![Danny's Diner](https://github.com/Bishalg27/Danny-s-Diner/assets/107564589/24f53689-7593-4f11-b31f-0396e2a482a7)


# Introduction

Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.

Danny’s Diner is in need of your assistance to help the restaurant stay afloat - the restaurant has captured some very basic data from their few months of operation but have no idea how to use their data to help them run the business

# Problem Statement

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

Danny has provided you with a sample of his overall customer data due to privacy issues - but he hopes that these examples are enough for you to write fully functioning SQL queries to help him answer his questions!

## Relationship 

 It shows the relation among the tables in the Database

 ![Relationship](https://github.com/Bishalg27/Danny-s-Diner/assets/107564589/f8ff3577-1962-4590-aca8-0d63fa49f288)



## Case Study Questions
   
 **1. What is the total amount each customer spent at the restaurant?**

```
SELECT customer_id,sum(price) as amount_spent FROM sales 
JOIN menu on sales.product_id = menu.product_id
group by customer_id;
```

**2. How many days has each customer visited the restaurant?**

```
SELECT customer_id,count(distinct(order_date)) as num_days FROM sales
group by customer_id;
```

**3. What was the first item from the menu purchased by each customer?**

```
SELECT customer_id,product_name FROM 
	 (	SELECT sales.*,product_name,row_number() over (partition by customer_id order by order_date)  as rowno
		FROM sales 
		join menu on sales.product_id = menu.product_id) Final
        where rowno = 1;
```
        
**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**

```
SELECT sales.product_id,product_name,count(*) as num_purchase FROM sales
join menu on sales.product_id = menu.product_id 
group by sales.product_id,product_name
order by num_purchase desc;
```

**5. Which item was the most popular for each customer?**

```
Select customer_id,product_name,total_orders FROM

(select customer_id,sales.product_id,product_name,count(*) total_orders,row_number() over (partition by customer_id order by count(*)  desc) rowno
 FROM sales
 join menu on sales.product_id = menu.product_id
group by customer_id,sales.product_id,product_name
order by total_orders desc) final
where rowno = 1;
```

**6. Which item was purchased first by the customer after they became a member?**

```
select customer_id,product_id,product_name,join_date,order_date FROM (
SELECT sales.customer_id,sales.product_id,product_name,join_date,order_date,row_number() over (partition by sales.customer_id order by order_date)rowno  FROM  members
JOIN sales
on members.join_date <= sales.order_date and members.customer_id = sales.customer_id
join menu on sales.product_id = menu.product_id) final
where rowno = 1;
```

**7. Which item was purchased just before the customer became a member?**

```
select customer_id,product_id,product_name,join_date,order_date FROM (
SELECT sales.customer_id,sales.product_id,product_name,join_date,order_date,row_number() over (partition by sales.customer_id order by order_date)rowno  FROM  members
JOIN sales
on members.join_date > sales.order_date and members.customer_id = sales.customer_id
join menu on sales.product_id = menu.product_id) final
where rowno != 1;
```

**8. What is the total items and amount spent for each member before they became a member?**

```
select sales.customer_id,count(*) quantity_ordered,sum(price) total_bill FROM sales 
join members on sales.order_date < members.join_date and sales.customer_id = members.customer_id
join menu on menu.product_id = sales.product_id
group by sales.customer_id;
```

**9.  If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**
```
create or replace view a as  
( select sales.customer_id,sales.product_id,product_name,price,(price*10) points FROM sales 
join menu on menu.product_id = sales.product_id
order by sales.customer_id ) ;

SELECT customer_id,
sum(case product_name
when 'sushi' then points*2 else points end) final_points
FROM a 
group by customer_id;

```





**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi  how many points do customer A and B have at the end of January?**

```
Create or Replace view b as 

( SELECT sales.*,product_name,price,join_date,date_add(join_date,interval 7 day) offer_date FROM members 
join sales on sales.customer_id = members.customer_id 
join menu on menu.product_id = sales.product_id );

select customer_id
,sum(case when order_date between join_date and offer_date THEN price*2 
else price*1 end ) total_points
FROM b
where order_date < '2021-01-31'
group by customer_id;
```


## Learnings : 

``` Data Analyzing ```

This project enhanced my ability to analyze data and derive insights from it. I acquired valuable skills in comprehending information, identifying patterns, and addressing problems effectively. It bolstered my confidence in tackling data-related challenges, enabling me to make well-informed decisions.


## Tech Stack 
`` MYSQL`` Used for solving this queries.


## Files Information : 
- Questions: It contains the questions that have been solved.
- Solution: It contains the queries.
- Relationship: It shows the schema, the relation among the tables.
- Table Script: It contains the table script of the problems.

## License : 
For questions or feedback, please contact: vishugoswami27@gmail.com

Enjoy exploring the project!










