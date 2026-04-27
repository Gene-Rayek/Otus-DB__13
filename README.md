# DML: агрегация и сортировка в MySQ

## **Задание:**
Создаем отчетную выборку

**Цель:**  
Создавать отчетную выборку.

**Описание/Пошаговая инструкция выполнения домашнего задания:**  
Предоставить следующий результат:

- группировки с использованием `CASE`, `HAVING`, `ROLLUP`, `GROUPING()`
- для магазина к списку товаров добавить максимальную и минимальную цену и количество предложений
- сделать выборку, показывающую самый дорогой и самый дешевый товар в каждой категории
- сделать `ROLLUP` с количеством товаров по категориям


## **Выполнено:**

В качестве проекта используется база данных `otus` со следующими таблицами:

- `categories`
- `products`
- `prices`
- `customers`
- `purchases`

### Подготовка рабочей базы

```mysql
USE otus;

SELECT * FROM categories;
SELECT * FROM products;
SELECT * FROM prices;
```

<img width="2416" height="826" alt="image" src="https://github.com/user-attachments/assets/8e933b28-2bda-448d-a925-a8df75ba6737" />


1. К списку товаров добавить максимальную и минимальную цену и количество предложений
 ```
SELECT
    IF(GROUPING(c.name), 'ИТОГО', c.name) AS category_name,
    MAX(pr.price) AS max_price,
    MIN(pr.price) AS min_price,
    COUNT(pr.price_id) AS total_offers
FROM categories c
LEFT JOIN products p
    ON c.category_id = p.category_id
LEFT JOIN prices pr
    ON p.product_id = pr.product_id
GROUP BY c.name WITH ROLLUP;
```

Что показывает запрос:

Для каждой категории выводится максимальная цена, минимальная цена и количество ценовых предложений.

Строка ИТОГО показывает общий итог по всем категориям.

<img width="777" height="198" alt="image" src="https://github.com/user-attachments/assets/da9530b9-c978-486a-a48d-40bca4c17824" />


2. Сделать выборку, показывающую самый дорогой и самый дешевый товар в каждой категории
```
SELECT
    c.name AS category_name,
    MAX(pr.price) AS max_price,
    MIN(pr.price) AS min_price
FROM categories c
JOIN products p
    ON c.category_id = p.category_id
JOIN prices pr
    ON p.product_id = pr.product_id
GROUP BY c.category_id, c.name
HAVING COUNT(pr.price_id) > 0
ORDER BY c.name;
```

Что показывает запрос:

Для каждой категории определяется максимальная и минимальная цена товара.

HAVING используется для исключения пустых категорий.

<img width="540" height="166" alt="image" src="https://github.com/user-attachments/assets/2c4ab33d-3e4d-4839-b7b7-cf431cc6e41b" />


3. Сделать rollup с количеством товаров по категориям
```
SELECT
    IF(GROUPING(c.name), 'ИТОГО', c.name) AS category_name,
    COUNT(p.product_id) AS total_products
FROM categories c
LEFT JOIN products p
    ON c.category_id = p.category_id
GROUP BY c.name WITH ROLLUP;
```

Что показывает запрос:

Выводит количество товаров по каждой категории и итоговое количество товаров по всем категориям.

<img width="499" height="201" alt="image" src="https://github.com/user-attachments/assets/70e7c3bc-191c-492a-abd9-cd7523cfcda4" />


Дополнительно: пример использования CASE
```
SELECT
    p.name AS product_name,
    pr.price,
    CASE
        WHEN pr.price < 5000 THEN 'Бюджетный товар'
        WHEN pr.price BETWEEN 5000 AND 20000 THEN 'Средний сегмент'
        ELSE 'Премиум'
    END AS price_group
FROM products p
JOIN prices pr
    ON p.product_id = pr.product_id
ORDER BY pr.price;
```


Что показывает запрос:

С помощью CASE товары распределяются по ценовым сегментам.


<img width="518" height="185" alt="image" src="https://github.com/user-attachments/assets/c34b7b8d-42e6-4519-b158-94562ff003ff" />



## Итог

В рамках домашнего задания выполнено:

- построены выборки с использованием GROUP BY

- использованы агрегатные функции MAX, MIN, COUNT

- применен фильтр HAVING

- использован ROLLUP

- показан пример использования CASE

- сформированы отчетные выборки по категориям товаров
