# Сведения о ветеранах спорта

## SQL-скрипт для многотабличной базы данных

```sql
CREATE DATABASE IF NOT EXISTS veterany_sporta;
USE veterany_sporta;

DROP TABLE IF EXISTS razmeshchenie;
DROP TABLE IF EXISTS sportsmeny;
DROP TABLE IF EXISTS komnaty;
DROP TABLE IF EXISTS gostinicy;
DROP TABLE IF EXISTS vozrastnye_gruppy;
DROP TABLE IF EXISTS goroda;
DROP TABLE IF EXISTS vidy_sporta;

-- 1. Виды спорта
CREATE TABLE vidy_sporta (
    id_vida_sporta INT PRIMARY KEY,
    nazvanie_vida_sporta VARCHAR(100) NOT NULL UNIQUE
);

-- 2. Города
CREATE TABLE goroda (
    id_goroda INT PRIMARY KEY,
    nazvanie_goroda VARCHAR(100) NOT NULL UNIQUE
);

-- 3. Возрастные группы
CREATE TABLE vozrastnye_gruppy (
    id_vozrastnoy_gruppy INT PRIMARY KEY,
    nazvanie_gruppy VARCHAR(50) NOT NULL UNIQUE,
    min_vozrast INT NOT NULL,
    max_vozrast INT NOT NULL,
    CHECK (min_vozrast >= 35),
    CHECK (max_vozrast > min_vozrast)
);

-- 4. Гостиницы
CREATE TABLE gostinicy (
    id_gostinicy INT PRIMARY KEY,
    nazvanie_gostinicy VARCHAR(100) NOT NULL,
    id_goroda INT NOT NULL,
    adres VARCHAR(150) NOT NULL,
    kategoriya INT NOT NULL DEFAULT 3,
    FOREIGN KEY (id_goroda) REFERENCES goroda(id_goroda),
    CHECK (kategoriya BETWEEN 1 AND 5)
);

-- 5. Комнаты в гостиницах
CREATE TABLE komnaty (
    id_komnaty INT PRIMARY KEY,
    id_gostinicy INT NOT NULL,
    nomer_komnaty VARCHAR(10) NOT NULL,
    kolichestvo_mest INT NOT NULL DEFAULT 2,
    FOREIGN KEY (id_gostinicy) REFERENCES gostinicy(id_gostinicy),
    CHECK (kolichestvo_mest BETWEEN 1 AND 4)
);

-- 6. Спортсмены
CREATE TABLE sportsmeny (
    id_sportsmena INT PRIMARY KEY,
    familiya VARCHAR(50) NOT NULL,
    imya VARCHAR(50) NOT NULL,
    otchestvo VARCHAR(50),
    id_vozrastnoy_gruppy INT NOT NULL,
    id_goroda INT NOT NULL,
    id_vida_sporta INT NOT NULL,
    status_uchastiya VARCHAR(20) NOT NULL DEFAULT 'Заявлен',
    FOREIGN KEY (id_vozrastnoy_gruppy) REFERENCES vozrastnye_gruppy(id_vozrastnoy_gruppy),
    FOREIGN KEY (id_goroda) REFERENCES goroda(id_goroda),
    FOREIGN KEY (id_vida_sporta) REFERENCES vidy_sporta(id_vida_sporta),
    CHECK (status_uchastiya IN ('Заявлен', 'Подтвержден', 'Размещен'))
);

-- 7. Размещение спортсменов
CREATE TABLE razmeshchenie (
    id_razmeshcheniya INT PRIMARY KEY,
    id_sportsmena INT NOT NULL UNIQUE,
    id_komnaty INT NOT NULL,
    data_zaezda DATE NOT NULL,
    data_vyezda DATE NOT NULL,
    FOREIGN KEY (id_sportsmena) REFERENCES sportsmeny(id_sportsmena),
    FOREIGN KEY (id_komnaty) REFERENCES komnaty(id_komnaty),
    CHECK (data_vyezda > data_zaezda)
);

-- Заполнение таблиц

INSERT INTO vidy_sporta VALUES
(1, 'Легкая атлетика'),
(2, 'Плавание'),
(3, 'Шахматы'),
(4, 'Настольный теннис');

INSERT INTO goroda VALUES
(1, 'Москва'),
(2, 'Казань'),
(3, 'Уфа'),
(4, 'Самара'),
(5, 'Екатеринбург');

INSERT INTO vozrastnye_gruppy VALUES
(1, '35-44', 35, 44),
(2, '45-54', 45, 54),
(3, '55-64', 55, 64),
(4, '65+', 65, 80);

INSERT INTO gostinicy VALUES
(1, 'Спорт-Отель', 2, 'ул. Центральная, 12', 4),
(2, 'Волна', 2, 'пр. Победы, 8', 3),
(3, 'Арена', 2, 'ул. Молодежная, 25', 5);

INSERT INTO komnaty VALUES
(1, 1, '101', 2),
(2, 1, '102', 2),
(3, 2, '201', 3),
(4, 2, '202', 2),
(5, 3, '301', 1),
(6, 3, '302', 2);

INSERT INTO sportsmeny VALUES
(1, 'Иванов', 'Сергей', 'Петрович', 2, 1, 1, 'Размещен'),
(2, 'Смирнова', 'Елена', 'Викторовна', 3, 3, 2, 'Размещен'),
(3, 'Кузнецов', 'Андрей', 'Николаевич', 1, 4, 4, 'Подтвержден'),
(4, 'Ахметов', 'Руслан', 'Ильдарович', 2, 2, 1, 'Размещен'),
(5, 'Васильева', 'Марина', 'Олеговна', 4, 5, 3, 'Заявлен'),
(6, 'Петров', 'Олег', 'Сергеевич', 3, 1, 2, 'Размещен');

INSERT INTO razmeshchenie VALUES
(1, 1, 1, '2025-10-10', '2025-10-15'),
(2, 2, 3, '2025-10-10', '2025-10-15'),
(3, 4, 2, '2025-10-10', '2025-10-15'),
(4, 6, 6, '2025-10-10', '2025-10-15');

-- Проверка таблиц
SELECT * FROM vidy_sporta;
SELECT * FROM goroda;
SELECT * FROM vozrastnye_gruppy;
SELECT * FROM gostinicy;
SELECT * FROM komnaty;
SELECT * FROM sportsmeny;
SELECT * FROM razmeshchenie;

-- Основной запрос по заданию
SELECT
    s.familiya,
    s.imya,
    s.otchestvo,
    vg.nazvanie_gruppy AS vozrastnaya_gruppa,
    g.nazvanie_goroda,
    vs.nazvanie_vida_sporta,
    gs.nazvanie_gostinicy,
    k.nomer_komnaty,
    k.kolichestvo_mest
FROM sportsmeny s
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
LEFT JOIN razmeshchenie r ON s.id_sportsmena = r.id_sportsmena
LEFT JOIN komnaty k ON r.id_komnaty = k.id_komnaty
LEFT JOIN gostinicy gs ON k.id_gostinicy = gs.id_gostinicy
ORDER BY s.familiya, s.imya;
```
