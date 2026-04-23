# БД «Поваренная книга» — вариант 6

Ниже приведён SQL-скрипт для создания базы данных, таблиц по структуре из задания и заполнения их тестовыми данными.  
Также добавлены запросы для проверки содержимого таблиц, просмотра связей между блюдами и продуктами и отдельные запросы по заданиям варианта 6.

## SQL-скрипт

```sql
CREATE DATABASE IF NOT EXISTS povarennaya_kniga;
USE povarennaya_kniga;

DROP TABLE IF EXISTS bludo_produkty;
DROP TABLE IF EXISTS bluda;
DROP TABLE IF EXISTS produkty;

-- 1. Таблица "Блюда"
CREATE TABLE bluda (
    kod_blyuda INT PRIMARY KEY,
    tip_blyuda VARCHAR(50) NOT NULL,
    ves_blyuda INT NOT NULL,
    poryadok_prigotovleniya TEXT NOT NULL,
    kolichestvo_kaloriy INT NOT NULL,
    kolichestvo_uglevodov DECIMAL(6,2) NOT NULL
);

-- 2. Таблица "Продукты"
CREATE TABLE produkty (
    kod_produkta INT PRIMARY KEY,
    nazvanie_produkta VARCHAR(100) NOT NULL,
    ed_izmereniya VARCHAR(20) NOT NULL
);

-- 3. Связующая таблица
CREATE TABLE bludo_produkty (
    kod_blyuda INT NOT NULL,
    kod_produkta INT NOT NULL,
    obem_produkta DECIMAL(8,2) NOT NULL,
    PRIMARY KEY (kod_blyuda, kod_produkta),
    FOREIGN KEY (kod_blyuda) REFERENCES bluda(kod_blyuda),
    FOREIGN KEY (kod_produkta) REFERENCES produkty(kod_produkta)
);

-- Заполнение таблицы "Блюда"
INSERT INTO bluda VALUES
(1, 'Суп', 350, 'Нарезать картофель, морковь и лук. Добавить курицу в воду, варить 20 минут. Добавить овощи и довести до готовности.', 180, 15.50),
(2, 'Второе', 300, 'Отварить рис. Куриное филе обжарить на сковороде. Смешать с рисом и посолить.', 320, 28.00),
(3, 'Салат', 200, 'Нарезать помидоры и огурцы, добавить соль и масло, перемешать.', 110, 7.20),
(4, 'Гарнир', 250, 'Очистить картофель, сварить, размять в пюре, добавить масло и соль.', 170, 24.00),
(5, 'Завтрак', 180, 'Отварить яйца, нарезать овощи, выложить на тарелку.', 150, 5.00);

-- Заполнение таблицы "Продукты"
INSERT INTO produkty VALUES
(1, 'Картофель', 'г'),
(2, 'Морковь', 'г'),
(3, 'Лук', 'г'),
(4, 'Куриное филе', 'г'),
(5, 'Рис', 'г'),
(6, 'Соль', 'г'),
(7, 'Растительное масло', 'мл'),
(8, 'Помидор', 'г'),
(9, 'Огурец', 'г'),
(10, 'Яйцо', 'шт');

-- Заполнение связующей таблицы
INSERT INTO bludo_produkty VALUES
-- Суп
(1, 1, 100),
(1, 2, 50),
(1, 3, 30),
(1, 4, 120),
(1, 6, 5),

-- Рис с курицей
(2, 4, 120),
(2, 5, 100),
(2, 6, 4),
(2, 7, 10),

-- Салат
(3, 8, 80),
(3, 9, 80),
(3, 6, 2),
(3, 7, 8),

-- Пюре
(4, 1, 180),
(4, 6, 3),
(4, 7, 10),

-- Яйца с овощами
(5, 10, 2),
(5, 8, 60),
(5, 9, 60),
(5, 6, 1);

-- =========================
-- Запросы для проверки
-- =========================

-- Все блюда
SELECT * FROM bluda;

-- Все продукты
SELECT * FROM produkty;

-- Все связи блюда и продукта
SELECT * FROM bludo_produkty;

-- Состав каждого блюда
SELECT
    b.kod_blyuda,
    b.tip_blyuda,
    p.nazvanie_produkta,
    bp.obem_produkta,
    p.ed_izmereniya
FROM bludo_produkty bp
JOIN bluda b ON bp.kod_blyuda = b.kod_blyuda
JOIN produkty p ON bp.kod_produkta = p.kod_produkta
ORDER BY b.kod_blyuda, p.nazvanie_produkta;

-- Количество продуктов в каждом блюде
SELECT
    b.kod_blyuda,
    b.tip_blyuda,
    COUNT(bp.kod_produkta) AS kolichestvo_produktov
FROM bluda b
LEFT JOIN bludo_produkty bp ON b.kod_blyuda = bp.kod_blyuda
GROUP BY b.kod_blyuda, b.tip_blyuda
ORDER BY b.kod_blyuda;

-- =========================
-- Задания варианта 6
-- =========================

-- 1. Вывести список блюд с весом больше указанного числа
SELECT *
FROM bluda
WHERE ves_blyuda > 250;

-- 2. Вывести список продуктов, в названии которых встречается указанный фрагмент слова
SELECT *
FROM produkty
WHERE nazvanie_produkta LIKE '%то%';

-- 3. Вывести объем продукта, название блюда, название продукта
--    с кодом блюда от указанного начального значения по конечное значение
SELECT
    b.kod_blyuda,
    bp.obem_produkta,
    b.tip_blyuda,
    p.nazvanie_produkta
FROM bludo_produkty bp
JOIN bluda b ON bp.kod_blyuda = b.kod_blyuda
JOIN produkty p ON bp.kod_produkta = p.kod_produkta
WHERE b.kod_blyuda BETWEEN 2 AND 4
ORDER BY b.kod_blyuda, p.nazvanie_produkta;

-- 4. Вывести порядок приготовления блюда и название блюда
--    с количеством углеводов больше определенного значения
--    или количеством калорий больше указанного значения
SELECT
    tip_blyuda,
    poryadok_prigotovleniya,
    kolichestvo_uglevodov,
    kolichestvo_kaloriy
FROM bluda
WHERE kolichestvo_uglevodov > 20
   OR kolichestvo_kaloriy > 200;
```
