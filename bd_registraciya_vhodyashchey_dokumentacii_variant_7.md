# БД «Регистрация входящей документации» — вариант 7

Ниже приведён SQL-скрипт для создания таблиц по структуре из задания и заполнения их тестовыми данными.  
Также добавлены запросы для проверки содержимого таблиц и просмотра связей между документами, регистраторами и организациями-отправителями.

## SQL-скрипт

```sql
DROP TABLE IF EXISTS dokumenty;
DROP TABLE IF EXISTS registratory;
DROP TABLE IF EXISTS organizacii_otpraviteli;

-- 1. Таблица "Регистраторы"
CREATE TABLE registratory (
    kod_registratora INT PRIMARY KEY,
    familiya VARCHAR(50) NOT NULL,
    imya VARCHAR(50) NOT NULL,
    otchestvo VARCHAR(50) NOT NULL,
    dolzhnost VARCHAR(100) NOT NULL,
    data_priema_na_rabotu DATE NOT NULL
);

-- 2. Таблица "Организации-отправители"
CREATE TABLE organizacii_otpraviteli (
    kod_organizacii_otpravitelya INT PRIMARY KEY,
    sokrashchennoe_nazvanie VARCHAR(100) NOT NULL,
    polnoe_nazvanie VARCHAR(255) NOT NULL,
    yuridicheskiy_adres VARCHAR(255) NOT NULL,
    telefon VARCHAR(30) NOT NULL,
    fio_rukovoditelya VARCHAR(150) NOT NULL
);

-- 3. Таблица "Документы"
CREATE TABLE dokumenty (
    kod_dokumenta INT PRIMARY KEY,
    nomer_dokumenta VARCHAR(50) NOT NULL,
    data_registracii DATE NOT NULL,
    kratkoe_soderzhanie_dokumenta TEXT NOT NULL,
    tip_dokumenta VARCHAR(50) NOT NULL,
    kod_organizacii_otpravitelya INT NOT NULL,
    kod_registratora INT NOT NULL,
    FOREIGN KEY (kod_organizacii_otpravitelya)
        REFERENCES organizacii_otpraviteli(kod_organizacii_otpravitelya),
    FOREIGN KEY (kod_registratora)
        REFERENCES registratory(kod_registratora)
);

-- Заполнение таблицы "Регистраторы"
INSERT INTO registratory VALUES
(1, 'Иванова', 'Мария', 'Сергеевна', 'Секретарь', '2021-03-15'),
(2, 'Петров', 'Алексей', 'Николаевич', 'Делопроизводитель', '2020-09-01'),
(3, 'Сидорова', 'Елена', 'Викторовна', 'Архивариус', '2022-01-20'),
(4, 'Кузнецов', 'Игорь', 'Павлович', 'Специалист по документации', '2019-11-11');

-- Заполнение таблицы "Организации-отправители"
INSERT INTO organizacii_otpraviteli VALUES
(1, 'ООО Альфа', 'Общество с ограниченной ответственностью Альфа', 'г. Москва, ул. Ленина, д. 10', '+7-495-123-45-67', 'Смирнов Андрей Олегович'),
(2, 'ЗАО Вектор', 'Закрытое акционерное общество Вектор', 'г. Казань, ул. Центральная, д. 5', '+7-843-222-11-33', 'Козлов Дмитрий Игоревич'),
(3, 'АО Прогресс', 'Акционерное общество Прогресс', 'г. Самара, пр. Победы, д. 18', '+7-846-555-66-77', 'Орлова Наталья Петровна'),
(4, 'ИП Соколов', 'Индивидуальный предприниматель Соколов Роман Сергеевич', 'г. Тверь, ул. Молодежная, д. 7', '+7-4822-44-55-66', 'Соколов Роман Сергеевич');

-- Заполнение таблицы "Документы"
INSERT INTO dokumenty VALUES
(1, 'ВХ-001', '2024-02-01', 'Письмо о сотрудничестве и предложении деловой встречи.', 'Письмо', 1, 1),
(2, 'ВХ-002', '2024-02-03', 'Коммерческое предложение на поставку офисной техники.', 'Коммерческое предложение', 2, 2),
(3, 'ВХ-003', '2024-02-05', 'Запрос на предоставление копий договоров за прошлый год.', 'Запрос', 3, 1),
(4, 'ВХ-004', '2024-02-06', 'Жалоба на задержку исполнения обязательств по контракту.', 'Жалоба', 1, 3),
(5, 'ВХ-005', '2024-02-08', 'Уведомление об изменении юридического адреса организации.', 'Уведомление', 4, 2),
(6, 'ВХ-006', '2024-02-10', 'Приглашение на отраслевую конференцию.', 'Приглашение', 3, 4);

-- =========================
-- Запросы для проверки
-- =========================

-- Все регистраторы
SELECT * FROM registratory;

-- Все организации-отправители
SELECT * FROM organizacii_otpraviteli;

-- Все документы
SELECT * FROM dokumenty;

-- Документы с данными об отправителе и регистраторе
SELECT
    d.kod_dokumenta,
    d.nomer_dokumenta,
    d.data_registracii,
    d.tip_dokumenta,
    d.kratkoe_soderzhanie_dokumenta,
    o.sokrashchennoe_nazvanie AS organizaciya_otpravitel,
    CONCAT(r.familiya, ' ', r.imya, ' ', r.otchestvo) AS registrator
FROM dokumenty d
JOIN organizacii_otpraviteli o
    ON d.kod_organizacii_otpravitelya = o.kod_organizacii_otpravitelya
JOIN registratory r
    ON d.kod_registratora = r.kod_registratora
ORDER BY d.kod_dokumenta;

-- Количество документов по типам
SELECT
    tip_dokumenta,
    COUNT(*) AS kolichestvo
FROM dokumenty
GROUP BY tip_dokumenta
ORDER BY kolichestvo DESC, tip_dokumenta;

-- Какие документы пришли от каждой организации
SELECT
    o.sokrashchennoe_nazvanie,
    d.nomer_dokumenta,
    d.tip_dokumenta,
    d.data_registracii
FROM organizacii_otpraviteli o
JOIN dokumenty d
    ON o.kod_organizacii_otpravitelya = d.kod_organizacii_otpravitelya
ORDER BY o.sokrashchennoe_nazvanie, d.data_registracii;

-- Сколько документов зарегистрировал каждый сотрудник
SELECT
    CONCAT(r.familiya, ' ', r.imya, ' ', r.otchestvo) AS registrator,
    COUNT(d.kod_dokumenta) AS kolichestvo_dokumentov
FROM registratory r
LEFT JOIN dokumenty d
    ON r.kod_registratora = d.kod_registratora
GROUP BY r.kod_registratora, r.familiya, r.imya, r.otchestvo
ORDER BY kolichestvo_dokumentov DESC, registrator;
```
