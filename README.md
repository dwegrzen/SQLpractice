# SQLpractice
SQL exercises


Explorer Mode

1. How many users are there?
select count(users.first_name) from users;
count(users.first_name)
50

2. What are the 5 most expensive items?
sqlite> select * from items order by price desc LIMIT 5;
select title, price from items order by price desc LIMIT 5;
title|price
Small Cotton Gloves|9984
Small Wooden Computer|9859
Awesome Granite Pants|9790
Sleek Wooden Hat|9390
Ergonomic Steel Car|9341

3. What's the cheapest book? (Does that change for "category is exactly 'book'" versus "category contains 'book'"?)
No result when category == 'book'

when category = 'Books':
select title, category, price from items where category = 'Books' order by price;
Ergonomic Granite Chair|Books|1496
Practical Plastic Hat|Books|3056
Fantastic Rubber Shoes|Books|8904
Fantastic Steel Chair|Books|9246

when category contains 'book':
select title, category, price from items where category like '%book%' order by price;
Ergonomic Granite Chair|Books|1496
Small Cotton Hat|Beauty & Books|1727
Incredible Granite Computer|Books, Toys & Tools|2377
Practical Plastic Hat|Books|3056
Fantastic Plastic Gloves|Beauty, Movies & Books|6584
Fantastic Rubber Shirt|Toys & Books|6720
Fantastic Rubber Shoes|Books|8904
Fantastic Steel Chair|Books|9246
Ergonomic Steel Car|Books & Outdoors|9341
Awesome Granite Pants|Toys & Books|9790

Still the cheapest 'book' though.

4. Who lives at "6439 Zetta Hills, Willmouth, WY"? Do they have another address?

select first_name, last_name from users inner join addresses on users.id = addresses.user_id where street = '6439 Zetta Hills' and city = 'Willmouth' and state = 'WY';
first_name|last_name
Corrine|Little

Correct Virginie Mitchell's address to "New York, NY, 10108".

update addresses set city = 'New York', state = 'NY', zip = '10108'  where exists (select * from users where user_id = users.id and first_name='Virginie' and last_name = 'Mitchell');

5. How much would it cost to buy one of each tool?
select sum(price) from items where category like '%Tools%';
sum(price)
46477

6. How many total items did we sell?
select sum(quantity) from orders;
sum(quantity)
2125

7. How much was spent on books?
select sum(price * quantity) from items inner join orders on items.id = orders.item_id where category like '%books%';
sum(price * quantity)
1081352

8. Simulate buying an item by inserting a User for yourself and an Order for that User.
insert into users (first_name, last_name, email) values ('Daniel','Wegrzen', 'dwegrzen@gmail.com');
insert into addresses (user_id, street, city, state, zip) values (51, '16806 Willow Oak Ln','Round Rock','TX',78681);
insert into orders (user_id, item_id, quantity, created_at) values (51, 18, 3, '2016-07-11 14:56:26');

Adventure Mode

1a. What item was ordered most often?
select sum(quantity),item_id,title from orders inner join items on items.id = item_id group by item_id order by sum(quantity) desc limit 5;
sum(quantity)|item_id|title
72|65|Incredible Granite Car
54|41|Practical Granite Car
53|46|Practical Rubber Computer
50|90|Practical Wooden Shoes
44|24|Rustic Steel Shirt

1b. Grossed the most money?
select sum(quantity*price), item_id,sum(quantity), title, price from orders inner join items on items.id = item_id group by item_id order by sum(quantity*price) desc limit 5;
sum(quantity*price)|item_id|sum(quantity)|title|price
525240|65|72|Incredible Granite Car|7295
449496|41|54|Practical Granite Car|8324
444750|90|50|Practical Wooden Shoes|8895
399302|46|53|Practical Rubber Computer|7534
329076|45|44|Ergonomic Cotton Chair|7479

What user spent the most?
select sum(quantity*price),first_name,last_name,count(users.id) from users inner join orders on users.id = user_id inner join items on items.id = orders.item_id group by users.id order by sum(quantity*price) desc limit 5;
sum(quantity*price)|first_name|last_name|count(users.id)
639386|Hassan|Runte|15
454343|Mekhi|Lakin|15
412642|Cleta|Adams|14
405918|Phoebe|Kshlerin|13
342594|Monserrate|Legros|15

What were the top 3 highest grossing categories?
select sum(quantity*price), category from orders inner join items on items.id = orders.item_id group by category order by sum(quantity*price) desc limit 3;
sum(quantity*price)|category
525240|Music, Sports & Clothing
449496|Beauty, Toys & Sports
448410|Sports
