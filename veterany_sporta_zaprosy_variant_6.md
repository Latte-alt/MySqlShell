# Запросы для варианта 6 — Сведения о ветеранах спорта

```sql
USE veterany_sporta;

ALTER TABLE sportsmeny
ADD COLUMN pol_sportsmena VARCHAR(10) NOT NULL DEFAULT 'Мужской';

ALTER TABLE sportsmeny
ADD CONSTRAINT chk_pol_sportsmena
CHECK (pol_sportsmena IN ('Мужской', 'Женский'));

UPDATE sportsmeny SET pol_sportsmena = 'Мужской' WHERE id_sportsmena IN (1, 3, 4, 6);
UPDATE sportsmeny SET pol_sportsmena = 'Женский' WHERE id_sportsmena IN (2, 5);

-- 1. Число ветеранов по каждому виду спорта
SELECT
    vs.nazvanie_vida_sporta,
    COUNT(s.id_sportsmena) AS chislo_veteranov
FROM vidy_sporta vs
LEFT JOIN sportsmeny s ON vs.id_vida_sporta = s.id_vida_sporta
GROUP BY vs.id_vida_sporta, vs.nazvanie_vida_sporta
ORDER BY vs.nazvanie_vida_sporta;

-- 2. Ограничение числа отображаемых записей по фамилиям ветеранов спорта
--    с указанием пола спортсмена, возрастной группы, города и вида спорта
SELECT
    s.familiya,
    s.imya,
    s.otchestvo,
    s.pol_sportsmena,
    vg.nazvanie_gruppy AS vozrastnaya_gruppa,
    g.nazvanie_goroda,
    vs.nazvanie_vida_sporta
FROM sportsmeny s
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
ORDER BY s.familiya
LIMIT 3;

-- 3. Сортировка по названию города
SELECT
    s.familiya,
    s.imya,
    s.otchestvo,
    g.nazvanie_goroda,
    vg.nazvanie_gruppy,
    vs.nazvanie_vida_sporta
FROM sportsmeny s
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
ORDER BY g.nazvanie_goroda ASC;

-- 4. Поиск по виду спорта
SELECT
    s.familiya,
    s.imya,
    s.otchestvo,
    g.nazvanie_goroda,
    vg.nazvanie_gruppy,
    vs.nazvanie_vida_sporta
FROM sportsmeny s
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
WHERE vs.nazvanie_vida_sporta = 'Плавание';

-- 5. Список ветеранов спорта, сгруппированных по виду спорта,
--    с указанием ФИО спортсмена, города и возрастной группы
SELECT
    vs.nazvanie_vida_sporta,
    s.familiya,
    s.imya,
    s.otchestvo,
    g.nazvanie_goroda,
    vg.nazvanie_gruppy
FROM sportsmeny s
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
ORDER BY vs.nazvanie_vida_sporta, s.familiya, s.imya;

-- 6. Вывод списка ветеранов спорта из одного и того же города с данными о них
SELECT
    s.familiya,
    s.imya,
    s.otchestvo,
    s.pol_sportsmena,
    vg.nazvanie_gruppy AS vozrastnaya_gruppa,
    g.nazvanie_goroda,
    vs.nazvanie_vida_sporta
FROM sportsmeny s
JOIN vozrastnye_gruppy vg ON s.id_vozrastnoy_gruppy = vg.id_vozrastnoy_gruppy
JOIN goroda g ON s.id_goroda = g.id_goroda
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
WHERE g.nazvanie_goroda = 'Москва'
ORDER BY s.familiya;

-- 7. Вывод списка спортсменов, сгруппированных по виду спорта
SELECT
    vs.nazvanie_vida_sporta,
    CONCAT(s.familiya, ' ', s.imya, ' ', s.otchestvo) AS fio_sportsmena
FROM sportsmeny s
JOIN vidy_sporta vs ON s.id_vida_sporta = vs.id_vida_sporta
ORDER BY vs.nazvanie_vida_sporta, s.familiya, s.imya;
```
