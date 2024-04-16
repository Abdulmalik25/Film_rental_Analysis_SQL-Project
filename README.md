# Film_rental_Analysis_SQL-Project
Ready to save the day for my friend's film rental store? Dive into the world of DVD rentals and SQL queries with me as we make crucial business decisions together! Let's leverage our expertise to shape the future of this business and drive success! Time to step up and make a difference!

# Introduction
There is a film rental store that provides DVDs on a rental basis. The owner of this store is your friend and he is not at all computer savvy. To manage his business he has software that helps him to manage the business.

He also has one helper who understands computers well and as a part-time job, he manages the software and helps the owner by providing different types of reports using SQL queries.

Today, unfortunately, this helper is not available. So your friend has called you to help him with some of the reports that he needs to make important business decisions.

# Data Model
## Overview of Tables.
| **Info**          | **Associated Tables**            |
|-------------------|----------------------------------|
| Actor Info        | Actor, Film_Actor                |
| Film Info         | Film, Film_Category, Category, Language |
| Customer Info     | Customer, Address, City, Country |
| Store Info        | Store, Staff, Rental, Payment    |

## Tables Structure
|                   Actor                  |               Film_Actor             |
|------------------------------------------|-------------------------------------|
| ● actor_id                               | ● actor_id                          |
| ● first_name                             | ● film_id                           |
| ● last_name                              |                                     |
|                                          |                                     |
|               Language                   |            Film_category            |
|------------------------------------------|-------------------------------------|
| ● language_id                            | ● film_id                           |
| ● name                                   | ● category_id                       |
|                                          |                                     |
|                   Film                   |                 Staff               |
|------------------------------------------|-------------------------------------|
| ● film_id                                | ● staff_id                          |
| ● title                                  | ● first_name                        |
| ● description                            | ● last_name                         |
| ● language_id                            | ● address_id                        |
| ● rental_duration                        | ● email                             |
| ● rental_rate                            | ● store_id                          |
| ● length                                 |                                     |
| ● replacement_cost                       |                                     |
| ● rating                                 |                                     |
|                                          |                                     |
|                Category                  |               Country               |
|------------------------------------------|-------------------------------------|
| ● category_id                            | ● country_id                        |
| ● name                                   | ● country                           |
|                                          |                                     |
|                Customer                  |               Address               |
|------------------------------------------|-------------------------------------|
| ● customer_id                            | ● address_id                        |
| ● store_id                               | ● district                          |
| ● first_name                             | ● city_id                           |
| ● last_name                              | ● postal_code                       |
| ● email                                  | ● phone                             |
| ● address_id                             | ● location                          |
|                                          |                                     |
|                  City                    |                Store                |
|------------------------------------------|-------------------------------------|
| ● city_id                                | ● store_id                          |
| ● city                                   | ● manager_staff_id                  |
| ● country_id                             | ● address_id                        |
|                                          |                                     |
|                  Rental                  |               Payment               |
|------------------------------------------|-------------------------------------|
| ● rental_id                              | ● payment_id                        |
| ● rental_date                            | ● customer_id                       |
| ● inventory_id                           | ● staff_id                          |
| ● customer_id                            | ● rental_id                         |
| ● return_date                            | ● amount                            |
| ● staff_id                               | ● payment_date                      |


## ER- Diagram
![2024-04-16-23-50-05](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/eb05ec54-786e-4f08-8e2f-ef8833ab093a)


# Getting Started 
## Questions and query.
1. What is the total revenue generated from all rentals in the database? 
``` sql
select sum(amount) as total_revenue 
from rental 
join payment p using(rental_id);
```
![2a](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/f2ba8728-a636-4f62-bc1c-3cf763783760)

2. How many rentals were made in each month_name?
 ``` sql
select monthname(rental_date) as Month_Name,count(*)as No_of_Rentals 
 from rental 
 group by 1 ;
```
![2b](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/062be1ec-a466-47e1-a237-b9d7b05dd68e)

3.  What is the rental rate of the film with the longest title in the database? 
``` sql
select distinct title,character_length(title) as Length_of_title,rental_rate 
from film 
where character_length(title)=(select max(character_length(title)) from film);
```
![2c](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/e442b87a-b40f-4457-b613-89716f393e69)

4. What is the average rental rate for films that were taken from the last 30 days from the date("2005-05-05 22:04:30")? Days between '2005-05-05 to 2005-06-05'
``` sql
with table1 as (select film.*,date(date_format(rental_date, '%Y-%m-%d %H:%i:%s')) as converted_date from film
join inventory using (film_id)
join rental using (inventory_id)
order by converted_date) select '2005-05-05 to 2005-06-05' as Period,avg(rental_rate) as Average_RentalRate from table1 where converted_date between '2005-05-05' and '2005-06-05';
```
![2d](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/238d1a69-8bd8-47ad-9c47-13ea1034ad85)

5. What is the most popular category of films in terms of the number of rentals? 
``` sql
select distinct c.name,count(*) as no_of_rentals 
from film f 
join inventory i using(film_id) 
join rental r using (inventory_id)
join film_category fc using(film_id)
join category c using(category_id)
group by 1
order by 2 desc
limit 1;
```
![2e](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/b373368a-615b-4c0d-89ae-e763f943b11e)

6. Find the longest movie duration from the list of films that have not been rented by any customer.
``` sql
select title,f.length as longest_movie_duration
from film f
left join inventory i using(film_id)
left join rental r using(inventory_id)
where r.rental_id is null
group by 1,2
order by f.length desc 
limit 1;
```
![2f](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/9a5a3537-2ddb-4c5a-b1db-86a170febc38)


7.  What is the average rental rate for films, broken down by category? 
``` sql
select distinct name,AVG(rental_rate) as Average_RentalRate 
from film f 
join inventory i using(film_id) 
join rental r using (inventory_id)
join film_category fc using(film_id)
join category using(category_id)
group by 1;
```
![2g](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/0f42b52e-9c7c-4721-9f3a-fd21a81f8d83)

8. What is the total revenue generated from rentals for each actor in the database? 
``` sql
select distinct concat_ws(' ',first_name,last_name) as Actor_name,sum(p.amount) as total_amount 
from film f 
join inventory i using(film_id) 
join rental r using (inventory_id)
join film_actor fa using(film_id)
join actor a using(actor_id)
join payment p using(rental_id)
group by 1;
```
![2h](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/a2533311-db38-4eed-b531-9bcef14604e9)

9. Show all the actresses who worked in a film having a "Wrestler" in the description. 
``` sql
select distinct concat_ws(' ',first_name,last_name) as name
from film f 
join film_actor fa using(film_id)
join actor using(actor_id)
where description like'%wrestler%';
```
![2i](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/437b4e90-97fc-4113-bb45-7007ebab807f)

10.  Which customers have rented the same film more than once?
``` sql
select c.customer_id,concat_ws(' ',first_name,last_name) as Name,f.film_id,f.title,count(*) as RentalCount
from rental r
join customer c USING(customer_id)
join inventory i using(inventory_id)
join film f using(film_id)
group by 1, 3, 4
having RentalCount > 1
order by  RentalCount desc;
```
![2j](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/4b4d10a0-dbd2-44df-ab08-78d9d9129a87)


11. How many films in the comedy category have a rental rate higher than the average rental rate?
``` sql
select 'Comedy' as category, count(f.film_id)as No_of_film 
from category c 
join film_category using (category_id) 
join film f  using (film_id) 
where c.name like "%Comedy%"and (rental_rate>(select avg(rental_rate)from film));
```
![2k](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/12482216-6ed7-41f9-ab56-4275f1a37387)


12. Which films have been rented the most by customers living in each city?
``` sql
With temp1 as (select distinct title,city,count(*) as No_of_Rentals,row_number() over(partition by city order by count(*) desc) as Ranked
from rental r
join customer c USING(customer_id)
join inventory i using(inventory_id)
join film f using(film_id)
join address using(address_id)
join city using (city_id)
group by 1,2)
select title,city,No_of_rentals from temp1 where Ranked=1 order by 3 desc;
```
![2l](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/9999ed5c-cf67-4a51-98ae-3224458d1faf)

13. What is the total amount spent by customers whose rental payments exceed $200?
``` sql
with table1 as
(select distinct customer_id,sum(amount)as amount 
from customer c 
join payment p using(customer_id)
group by 1
having sum(amount)>200) 
select 'Total_amount_spent_by_customers' as Rental_exceeds_200,sum(amount) as Total_amount from table1;
```
![2m](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/297335cd-2fdb-480d-9907-01d4b4ae9fce)

14.  Display the fields which are having foreign key constraints related to the "rental" table.
``` sql
select  constraint_name,column_name,referenced_table_name,referenced_column_name
from information_schema.key_column_usage
where table_name= 'rental' and referenced_table_name is not null;
```
![2n](https://github.com/Abdulmalik25/Film_rental_Analysis_SQL-Project/assets/153974173/b44c95ef-0254-4f4b-ae74-f42057edfe7d)

15. Create a View for the total revenue generated by each staff member, broken down by store city with the country name.
``` sql
create view Total_revenue as 
(select distinct so.store_id,city,country,sum(amount)as Total_revenue 
from address a 
join city c using(city_id) 
join country co using(country_id) 
join staff s using(address_id)
join store so on s.staff_id=so.manager_staff_id 
join payment using(staff_id) group by 1);
```
16. Create a view based on rental information consisting of visiting_day, customer_name, the title of the film, no_of_rental_days, the amount paid by the customer along with the percentage of customer spending.
``` sql
create view rental_information as select dayname(rental_date) as Visiting_day, concat_ws(' ',first_name,last_name) as cutomer_name,title,datediff(return_date,rental_date) as No_of_days,amount,
(p.amount/(select sum(amount) from payment where customer_id=r.customer_id)) *100 as spending_percentange
from rental r
join customer c USING(customer_id)
join inventory i using(inventory_id)
join film f using(film_id)
join payment p using(rental_id);
```
17.  Display the customers who paid 50% of their total rental costs within one day. 
``` sql
with customerpayments as (
select
c.customer_id,
concat_ws(' ', c.first_name, c.last_name) as customer_name,
sum(p.amount) as total_payment
from rental r
join customer c using(customer_id)
join payment p using(rental_id)
join inventory i using(inventory_id)
join film f using (film_id)
group by c.customer_id
),
 customerdaypayments as (
select c.customer_id,
concat_ws(' ', c.first_name, c.last_name) as customer_name,
date(r.rental_date) as rental_date,
sum(p.amount) as total_amount_per_date,
sum(p.amount) + coalesce(lead(sum(p.amount)) over (partition by c.customer_id order by rental_date), 0) as total_amount_next_date
from rental r
join customer c using(customer_id)
join payment p using(rental_id)
join inventory i using(inventory_id)
join film f using(film_id)
group by c.customer_id, rental_date
),
ranked_payment as (
select  cp.customer_id,cp.customer_name,rental_date,total_amount_per_date,total_amount_next_date,
        row_number() over (partition by customer_id order by total_amount_next_date desc) as rank1
from customerdaypayments cdp
join CustomerPayments cp using(customer_id)
)
select
    rp.customer_id,
    rp.customer_name,
    rp.rental_date,
    rp.total_amount_per_date,
    rp.total_amount_next_date,
    cp.total_payment
from ranked_payment rp
join CustomerPayments cp using(customer_id)
where rank1 = 1 and (total_amount_next_date>=0.5*total_payment)
order by customer_name;
```

# Conclusion
With well-organized tables and SQL queries, this project helps manage film rental store data for better business decisions. By structuring data and providing insightful reports, it aims to streamline operations and ensure continued success.
