# Однотабличная база данных по предметной области «Предприятие "Автосервис"» для MySQL Shell

Ниже приведён **упрощённый вариант** однотабличной базы данных по предметной области **Предприятие "Автосервис"** для **MySQL Shell**.

В этой версии уменьшено количество полей примерно в 2 раза, чтобы таблица была компактнее и её было удобнее показывать преподавателю.

## Как запускать в MySQL Shell

Сначала перейдите в SQL-режим:

```sql
\sql
```

---

## Как удалить предыдущую таблицу

Если таблица уже была создана раньше, удалите её так:

```sql
DROP TABLE IF EXISTS service_orders;
```

Если нужно удалить всю старую базу целиком:

```sql
DROP DATABASE IF EXISTS autoservice_db;
```

---

## 1. Создание базы данных

```sql
DROP DATABASE IF EXISTS autoservice_db;
CREATE DATABASE autoservice_db
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

USE autoservice_db;
```

---

## 2. Создание таблицы

```sql
CREATE TABLE service_orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    order_number VARCHAR(20) NOT NULL UNIQUE,
    client_full_name VARCHAR(100) NOT NULL,
    client_phone VARCHAR(20) NOT NULL,
    car_brand VARCHAR(50) NOT NULL,
    car_model VARCHAR(50) NOT NULL,
    service_description VARCHAR(255) NOT NULL,
    master_full_name VARCHAR(100) NOT NULL,
    date_received DATE NOT NULL,
    total_cost DECIMAL(10,2) NOT NULL,
    repair_status ENUM('Принят', 'В работе', 'Готов', 'Выдан') NOT NULL
);
```

---

## 3. Добавление данных

```sql
INSERT INTO service_orders (
    order_number, client_full_name, client_phone,
    car_brand, car_model, service_description,
    master_full_name, date_received, total_cost, repair_status
) VALUES
('AS-2026-001', 'Иванов Сергей Петрович', '+7-901-111-22-33',
 'Toyota', 'Camry', 'Замена масла и фильтров',
 'Кузнецов Андрей Олегович', '2026-03-01', 6700.00, 'Выдан'),

('AS-2026-002', 'Петров Алексей Викторович', '+7-902-222-33-44',
 'Hyundai', 'Solaris', 'Замена тормозных колодок',
 'Смирнов Денис Игоревич', '2026-03-02', 11800.00, 'Выдан'),

('AS-2026-003', 'Сидорова Марина Олеговна', '+7-903-333-44-55',
 'Kia', 'Rio', 'Замена стоек стабилизатора',
 'Волков Илья Сергеевич', '2026-03-04', 6100.00, 'Выдан'),

('AS-2026-004', 'Кузьмина Ольга Андреевна', '+7-904-444-55-66',
 'Volkswagen', 'Polo', 'Диагностика двигателя и замена свечей',
 'Орлов Павел Николаевич', '2026-03-05', 5200.00, 'Выдан'),

('AS-2026-005', 'Федоров Николай Васильевич', '+7-905-555-66-77',
 'Renault', 'Duster', 'Замена аккумулятора',
 'Лебедев Артем Константинович', '2026-03-06', 8700.00, 'Выдан'),

('AS-2026-006', 'Морозов Дмитрий Сергеевич', '+7-906-666-77-88',
 'Lada', 'Vesta', 'Замена сцепления',
 'Кузнецов Андрей Олегович', '2026-03-07', 19700.00, 'Готов'),

('AS-2026-007', 'Егорова Наталья Павловна', '+7-907-777-88-99',
 'Skoda', 'Octavia', 'Заправка кондиционера',
 'Смирнов Денис Игоревич', '2026-03-08', 5200.00, 'Выдан'),

('AS-2026-008', 'Васильев Артем Романович', '+7-908-888-99-00',
 'Ford', 'Focus', 'Замена рулевой тяги',
 'Волков Илья Сергеевич', '2026-03-09', 7800.00, 'Выдан'),

('AS-2026-009', 'Никитин Павел Геннадьевич', '+7-909-999-00-11',
 'Nissan', 'X-Trail', 'Замена задних амортизаторов',
 'Орлов Павел Николаевич', '2026-03-10', 14800.00, 'Выдан'),

('AS-2026-010', 'Орлова Елена Сергеевна', '+7-912-303-33-44',
 'Mercedes-Benz', 'C200', 'Замена датчика ABS',
 'Кузнецов Андрей Олегович', '2026-03-13', 11000.00, 'В работе');
```

---

## 4. Проверка данных

```sql
SELECT * FROM service_orders;
```

---

## Что хранится в таблице

Таблица `service_orders` содержит:
- номер заказа
- ФИО клиента
- телефон клиента
- марку автомобиля
- модель автомобиля
- описание услуги
- мастера
- дату приёма
- итоговую стоимость
- статус ремонта

---

## Полезные запросы для проверки

### Показать все записи

```sql
SELECT * FROM service_orders;
```

### Показать машины, которые ещё не выданы

```sql
SELECT order_number, client_full_name, car_brand, car_model, repair_status
FROM service_orders
WHERE repair_status IN ('Принят', 'В работе', 'Готов');
```

### Показать все заказы конкретного мастера

```sql
SELECT order_number, client_full_name, service_description, total_cost
FROM service_orders
WHERE master_full_name = 'Кузнецов Андрей Олегович';
```

### Посчитать общую сумму по всем заказам

```sql
SELECT SUM(total_cost) AS total_sum
FROM service_orders;
```
