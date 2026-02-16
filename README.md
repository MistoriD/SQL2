# Домашнее задание к занятию "`SQL. Часть 2`" - `Шмагин Максим`


### Задание 1.

```
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

```

Ответ:

## Задание 1 - Магазин с >300 покупателей

`Запрос:`

```sql USE sakila;
SELECT 
    CONCAT(s.first_name, ' ', s.last_name) AS staff_name,
    c.city AS city,
    COUNT(cust.customer_id) AS customer_count
FROM store st
JOIN staff s ON st.store_id = s.store_id
JOIN address a ON st.address_id = a.address_id  
JOIN city c ON a.city_id = c.city_id
JOIN customer cust ON st.store_id = cust.store_id
WHERE st.store_id IN (
    SELECT store_id 
    FROM customer 
    GROUP BY store_id 
    HAVING COUNT(*) > 300
)
GROUP BY st.store_id, s.staff_id, c.city
ORDER BY customer_count DESC;
```

`Результат запроса:`

```
+--------------+------------+----------------+
| staff_name   | city       | customer_count |
+--------------+------------+----------------+
| Mike Hillyer | Lethbridge |            326 |
+--------------+------------+----------------+
```
### Задание 2.

```
Установите и запустите memcached.

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

```

Ответ:

`Запрос:`

```sql USE sakila;

SELECT COUNT(*) AS films_longer_than_average
FROM film f
WHERE f.length > (
    SELECT AVG(length)
    FROM film
);
```

`Результат запроса:`

```
+---------------------------+
| films_longer_than_average |
+---------------------------+
|                       489 |
+---------------------------+
```



### Задание 3.

```
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.
```

Ответ:

`Запрос:`

```sql
USE sakila;

SELECT 
    DATE_FORMAT(p.payment_date, '%Y-%m') AS payment_month,
    ROUND(SUM(p.amount), 2) AS total_payments,
    COUNT(r.rental_id) AS rentals_count
FROM payment p
LEFT JOIN rental r ON p.rental_id = r.rental_id
GROUP BY DATE_FORMAT(p.payment_date, '%Y-%m')
ORDER BY total_payments DESC
LIMIT 1;
```

`Результат запроса:`

```
+---------------+----------------+---------------+
| payment_month | total_payments | rentals_count |
+---------------+----------------+---------------+
| 2005-07       |       28368.91 |          6709 |
+---------------+----------------+---------------+
```
