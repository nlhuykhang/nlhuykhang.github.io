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
