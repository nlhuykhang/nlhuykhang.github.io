---
layout: post
title: MySQL Logs
categories: [sql, logs]
tags: [sql, logs]
published: false
fullview: true
---

select prod_name, prod_price
from products
where vend_id in (1002, 1003)
order by prod_name;


select prod_id, prod_name
from products
where prod_name like '_ whatever';

select prod_id, prod_name
from products
where prod_name like '% whatever';

## Regular expression

select prod_id, prod_name
from products
where prod_name regexp '1000';

where prod_name regexp binary 'Something';

where prod_name regexp '1000|2000';

where prod_name regexp '[123] ton';

where vend_name regexp '\\.';

### to test regex in MySQL
select 'hello' regexp '[0-9]';

### concat

select concat(vend_name, ' ', vend_country)
from vendors;

select concat(rtrim(vend_name), ' ', vendor_country)
from vendors;


## group by

select vend_id, count(*) as num_prods
from products
group by vend_id with rollup;

select cust_id, count(*) as orders
from orders
group by cust_id
having count(*) >= 2;

select order_num, sum(quantity * item_price)
from orderitems
group by order_num
having sum(quantity * item_price) >= 50;

## join

select vend_name, prod_name, prod_price
from vendors inner join products on vendors.vend_id = products.vend_id;

select customers.cust_id, orders.order_num
from customers left outer join orders on customers.cust_id = orders.cust_id;

select customers.cust_id, orders.order_num
from customers right outer join orders on customers.cust_id = orders.cust_id;



## table size

```
SELECT
    table_name AS `Table`,
    round(((data_length + index_length) / 1024 / 1024), 2) `Size in MB`
FROM information_schema.TABLES
WHERE table_schema = "db_name" AND table_name = "table_name"
```
