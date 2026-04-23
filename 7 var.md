# 7 var

## База данных «Крупнейшие города мира»

```sql
CREATE DATABASE IF NOT EXISTS krupneyshie_goroda_mira;
USE krupneyshie_goroda_mira;

DROP TABLE IF EXISTS goroda_mira;

CREATE TABLE goroda_mira (
    id INT PRIMARY KEY,
    nazvanie_goroda VARCHAR(100) NOT NULL,
    strana VARCHAR(100) NOT NULL,
    materik VARCHAR(50) NOT NULL,
    naselenie_mln DECIMAL(5,2) NOT NULL,
    ploshad_km2 INT NOT NULL,
    god_osnovaniya INT,
    oficialnyy_yazyk VARCHAR(100) NOT NULL
);

INSERT INTO goroda_mira VALUES
(1, 'Токио', 'Япония', 'Азия', 37.40, 2194, 1603, 'Японский'),
(2, 'Дели', 'Индия', 'Азия', 32.10, 1484, 1649, 'Хинди'),
(3, 'Шанхай', 'Китай', 'Азия', 29.20, 6340, 1291, 'Китайский'),
(4, 'Сан-Паулу', 'Бразилия', 'Южная Америка', 22.60, 1521, 1554, 'Португальский'),
(5, 'Мехико', 'Мексика', 'Северная Америка', 22.10, 1485, 1325, 'Испанский'),
(6, 'Каир', 'Египет', 'Африка', 21.70, 3085, 969, 'Арабский'),
(7, 'Пекин', 'Китай', 'Азия', 21.30, 16411, 1045, 'Китайский'),
(8, 'Мумбаи', 'Индия', 'Азия', 20.90, 603, 1507, 'Маратхи'),
(9, 'Осака', 'Япония', 'Азия', 19.00, 225, 1889, 'Японский'),
(10, 'Нью-Йорк', 'США', 'Северная Америка', 18.80, 783, 1624, 'Английский');
```
