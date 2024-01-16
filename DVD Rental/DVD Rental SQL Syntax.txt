---DVD RENTAL DATA ANALYSIS---


--Identify the top 10 customers and their email so we can reward them
select customer.customer_id, concat(first_name,' ',last_name) as
full_name, customer.email, sum(payment.amount) as total_amount
from customer inner join payment on
customer.customer_id=payment.customer_id
group by customer.customer_id
order by total_amount desc
limit 10;


--Identify the bottom 10 customers and their emails
select customer.customer_id, concat(first_name,' ',last_name) as
full_name, customer.email, sum(payment.amount) as total_amount
from customer inner join payment on
customer.customer_id=payment.customer_id
group by customer.customer_id
order by total_amount asc
limit 10;


--What are the most profitable movie genres (ratings)?
select category.name as genre, count(customer.customer_id) as
total_demand, sum(payment.amount) as total_sales from category
inner join film_category on category.category_id =
film_category.category_id
inner join film on film_category.film_id = film.film_id
inner join inventory on film.film_id = inventory.film_id
inner join rental on inventory.inventory_id = rental.inventory_id
inner join customer on rental.customer_id = customer.customer_id
inner join payment on rental.rental_id = payment.rental_id
group by genre
order by total_demand desc;


--How many rented movies were returned late, early, and on time?
select case
when rental_duration > date_part('day',return_date-rental_date )
then 'returned early'
when rental_duration = date_part('day',return_date-rental_date )
then 'returned on time'
else 'returned late'
end as status_of_return,
count(*) total_no_of_films
from film
inner join inventory on film.film_id = inventory.film_id
inner join rental on inventory.inventory_id = rental.inventory_id
group by status_of_return
order by total_no_of_films desc;


--What is the customer base in the countries where we have a presence?
select country, count(distinct customer_id) as customer_base
from country
join city using (country_id)
join address using (city_id)
join customer using (address_id)
group by country
order by customer_base desc;


--Which country is the most profitable for the business?
select country, count(*) as total_no_customer, sum(amount) as
total_sales
from country
join city using (country_id)
join address using (city_id)
join customer using (address_id)
join payment using (customer_id)
group by country
order by total_no_customer desc;


--What is the average rental rate per movie genre (rating)?
select category.name as movie_genre, round(avg(film.rental_rate),2) as
average_rental_rate
from category
join film_category using (category_id)
join film using (film_id)
group by movie_genre
order by average_rental_rate desc;