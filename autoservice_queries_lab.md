# SQL-запросы для базы данных «Автосервис»

Ниже приведены запросы для базы данных **`autoservice_db`** и таблицы **`service_orders`**.

Таблица содержит поля:
- `order_id`
- `order_number`
- `client_full_name`
- `client_phone`
- `car_brand`
- `car_model`
- `service_description`
- `master_full_name`
- `date_received`
- `total_cost`
- `repair_status`

---

## 1. Простая выборка и сортировка
**SELECT, WHERE, ORDER BY — 3 запроса**

### Запрос 1
Показать все заказы со статусом **«Выдан»**, отсортировать по дате приёма:

```sql
SELECT * 
FROM service_orders
WHERE repair_status = 'Выдан'
ORDER BY date_received;
```

### Запрос 2
Показать клиентов и машины мастера **Кузнецова Андрея Олеговича**, отсортировать по стоимости от большей к меньшей:

```sql
SELECT client_full_name, car_brand, car_model, total_cost
FROM service_orders
WHERE master_full_name = 'Кузнецов Андрей Олегович'
ORDER BY total_cost DESC;
```

### Запрос 3
Показать все заказы автомобиля марки **Toyota**, отсортировать по номеру заказа:

```sql
SELECT order_number, client_full_name, car_brand, car_model
FROM service_orders
WHERE car_brand = 'Toyota'
ORDER BY order_number;
```

---

## 2. Вычисления и фильтрация по диапазонам
**BETWEEN, LIKE, вычисляемые поля — 3 запроса**

### Запрос 4
Показать заказы со стоимостью от **5000 до 12000**:

```sql
SELECT order_number, client_full_name, total_cost
FROM service_orders
WHERE total_cost BETWEEN 5000 AND 12000;
```

### Запрос 5
Показать клиентов, у которых фамилия начинается на **«И»**:

```sql
SELECT client_full_name, client_phone
FROM service_orders
WHERE client_full_name LIKE 'И%';
```

### Запрос 6
Показать стоимость заказа и стоимость с наценкой 10 процентов:

```sql
SELECT order_number, total_cost, total_cost * 1.10 AS increased_cost
FROM service_orders;
```

---

## 3. Группировка и агрегатные функции
**GROUP BY, HAVING, MIN, MAX, AVG, SUM, COUNT — 3 запроса**

### Запрос 7
Посчитать, сколько заказов у каждого мастера:

```sql
SELECT master_full_name, COUNT(*) AS orders_count
FROM service_orders
GROUP BY master_full_name;
```

### Запрос 8
Показать среднюю стоимость заказов по каждому мастеру, но только если средняя стоимость больше 7000:

```sql
SELECT master_full_name, AVG(total_cost) AS avg_cost
FROM service_orders
GROUP BY master_full_name
HAVING AVG(total_cost) > 7000;
```

### Запрос 9
Показать минимальную, максимальную и общую стоимость заказов по каждому статусу ремонта:

```sql
SELECT repair_status,
       MIN(total_cost) AS min_cost,
       MAX(total_cost) AS max_cost,
       SUM(total_cost) AS total_sum
FROM service_orders
GROUP BY repair_status;
```

---

## 4. Комбинированные и практические задания
**2 запроса**

### Запрос 10
Показать мастеров, у которых больше одного заказа, и общую сумму их заказов:

```sql
SELECT master_full_name,
       COUNT(*) AS orders_count,
       SUM(total_cost) AS total_sum
FROM service_orders
GROUP BY master_full_name
HAVING COUNT(*) > 1
ORDER BY total_sum DESC;
```

### Запрос 11
Показать все невыданные машины со стоимостью больше 7000, отсортировать по стоимости:

```sql
SELECT order_number, client_full_name, car_brand, car_model, total_cost, repair_status
FROM service_orders
WHERE repair_status IN ('Принят', 'В работе', 'Готов')
  AND total_cost > 7000
ORDER BY total_cost DESC;
```

---

## Краткая информация для отчёта

**База данных:** `autoservice_db`  
**Таблица:** `service_orders`

Эти запросы демонстрируют:
- простую выборку данных
- сортировку
- фильтрацию по условию
- поиск по шаблону
- вычисляемые поля
- группировку
- агрегатные функции
- комбинирование нескольких условий в одном запросе
