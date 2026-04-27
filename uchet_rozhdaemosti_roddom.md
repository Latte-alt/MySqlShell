# Учет рождаемости в роддоме

## SQL-скрипт для многотабличной базы данных

```sql
CREATE DATABASE IF NOT EXISTS uchet_rozhdaemosti;
USE uchet_rozhdaemosti;

DROP TABLE IF EXISTS deti;
DROP TABLE IF EXISTS materi;
DROP TABLE IF EXISTS vrachi;
DROP TABLE IF EXISTS palaty;

-- 1. Палаты
CREATE TABLE palaty (
    id_palaty INT PRIMARY KEY,
    nomer_palaty VARCHAR(10) NOT NULL UNIQUE,
    kolichestvo_mest INT NOT NULL DEFAULT 2,
    tip_palaty VARCHAR(30) NOT NULL DEFAULT 'Обычная',
    CHECK (kolichestvo_mest BETWEEN 1 AND 4),
    CHECK (tip_palaty IN ('Обычная', 'Повышенный комфорт', 'Индивидуальная'))
);

-- 2. Врачи
CREATE TABLE vrachi (
    id_vracha INT PRIMARY KEY,
    familiya VARCHAR(50) NOT NULL,
    imya VARCHAR(50) NOT NULL,
    otchestvo VARCHAR(50),
    specializaciya VARCHAR(100) NOT NULL DEFAULT 'Акушер-гинеколог',
    stazh_let INT NOT NULL DEFAULT 5,
    CHECK (stazh_let >= 0)
);

-- 3. Матери
CREATE TABLE materi (
    id_materi INT PRIMARY KEY,
    familiya VARCHAR(50) NOT NULL,
    imya VARCHAR(50) NOT NULL,
    otchestvo VARCHAR(50),
    id_vracha INT NOT NULL,
    id_palaty INT NOT NULL,
    data_postupleniya DATE NOT NULL,
    FOREIGN KEY (id_vracha) REFERENCES vrachi(id_vracha),
    FOREIGN KEY (id_palaty) REFERENCES palaty(id_palaty)
);

-- 4. Дети
CREATE TABLE deti (
    id_rebenka INT PRIMARY KEY,
    id_materi INT NOT NULL,
    pol_rebenka VARCHAR(10) NOT NULL,
    ves_gramm INT NOT NULL,
    rost_sm INT NOT NULL,
    data_rozhdeniya DATE NOT NULL,
    vremya_rozhdeniya TIME NOT NULL DEFAULT '12:00:00',
    FOREIGN KEY (id_materi) REFERENCES materi(id_materi),
    CHECK (pol_rebenka IN ('Мальчик', 'Девочка')),
    CHECK (ves_gramm BETWEEN 500 AND 6000),
    CHECK (rost_sm BETWEEN 25 AND 70)
);

-- Заполнение таблиц

INSERT INTO palaty VALUES
(1, '101', 2, 'Обычная'),
(2, '102', 1, 'Индивидуальная'),
(3, '201', 2, 'Повышенный комфорт'),
(4, '202', 3, 'Обычная');

INSERT INTO vrachi VALUES
(1, 'Иванова', 'Наталья', 'Сергеевна', 'Акушер-гинеколог', 12),
(2, 'Петров', 'Алексей', 'Викторович', 'Акушер-гинеколог', 9),
(3, 'Смирнова', 'Елена', 'Олеговна', 'Неонатолог', 7);

INSERT INTO materi VALUES
(1, 'Кузнецова', 'Мария', 'Андреевна', 1, 1, '2025-04-10'),
(2, 'Васильева', 'Анна', 'Игоревна', 2, 2, '2025-04-11'),
(3, 'Соколова', 'Ольга', 'Павловна', 1, 3, '2025-04-12'),
(4, 'Ахметова', 'Лилия', 'Ринатовна', 2, 4, '2025-04-13'),
(5, 'Николаева', 'Татьяна', 'Владимировна', 1, 1, '2025-04-14');

INSERT INTO deti VALUES
(1, 1, 'Мальчик', 3400, 52, '2025-04-11', '08:35:00'),
(2, 2, 'Девочка', 3200, 50, '2025-04-11', '14:20:00'),
(3, 3, 'Мальчик', 3600, 53, '2025-04-12', '06:10:00'),
(4, 4, 'Девочка', 2900, 49, '2025-04-13', '19:45:00'),
(5, 5, 'Мальчик', 4100, 55, '2025-04-15', '11:05:00');

-- Проверка таблиц
SELECT * FROM palaty;
SELECT * FROM vrachi;
SELECT * FROM materi;
SELECT * FROM deti;

-- Основной запрос по заданию
SELECT
    m.familiya AS familiya_materi,
    m.imya AS imya_materi,
    m.otchestvo AS otchestvo_materi,
    d.pol_rebenka,
    d.ves_gramm,
    d.rost_sm,
    d.data_rozhdeniya,
    d.vremya_rozhdeniya,
    v.familiya AS familiya_vracha,
    v.imya AS imya_vracha,
    v.otchestvo AS otchestvo_vracha,
    p.nomer_palaty
FROM deti d
JOIN materi m ON d.id_materi = m.id_materi
JOIN vrachi v ON m.id_vracha = v.id_vracha
JOIN palaty p ON m.id_palaty = p.id_palaty
ORDER BY d.data_rozhdeniya, d.vremya_rozhdeniya;
```
