# Запросы для варианта 7 — Учет рождаемости в роддоме

```sql
USE uchet_rozhdaemosti;

-- 1. Количество мальчиков и количество девочек в роддоме
SELECT
    pol_rebenka,
    COUNT(*) AS kolichestvo
FROM deti
GROUP BY pol_rebenka;

-- 2. Вывод списка рожениц, лечащихся у одного и того же врача,
--    с указанием ФИО матери, номера палаты и даты рождения ребенка
SELECT
    CONCAT(m.familiya, ' ', m.imya, ' ', m.otchestvo) AS fio_materi,
    p.nomer_palaty,
    d.data_rozhdeniya,
    CONCAT(v.familiya, ' ', v.imya, ' ', v.otchestvo) AS fio_vracha
FROM materi m
JOIN vrachi v ON m.id_vracha = v.id_vracha
JOIN palaty p ON m.id_palaty = p.id_palaty
JOIN deti d ON m.id_materi = d.id_materi
WHERE v.id_vracha = 1
ORDER BY m.familiya, m.imya;

-- 3. Сортировка по датам рождения
SELECT
    CONCAT(m.familiya, ' ', m.imya, ' ', m.otchestvo) AS fio_materi,
    d.pol_rebenka,
    d.data_rozhdeniya,
    d.vremya_rozhdeniya
FROM deti d
JOIN materi m ON d.id_materi = m.id_materi
ORDER BY d.data_rozhdeniya ASC, d.vremya_rozhdeniya ASC;

-- 4. Поиск по фамилии матери
SELECT
    m.familiya,
    m.imya,
    m.otchestvo,
    d.pol_rebenka,
    d.ves_gramm,
    d.rost_sm,
    d.data_rozhdeniya
FROM materi m
JOIN deti d ON m.id_materi = d.id_materi
WHERE m.familiya = 'Кузнецова';

-- 5. Список рожениц, сгруппированных по палатам,
--    с указанием ФИО матери, ФИО лечащего врача, даты рождения ребенка, пола ребенка,
--    а также количества рожениц в данной палате
SELECT
    p.nomer_palaty,
    CONCAT(m.familiya, ' ', m.imya, ' ', m.otchestvo) AS fio_materi,
    CONCAT(v.familiya, ' ', v.imya, ' ', v.otchestvo) AS fio_vracha,
    d.data_rozhdeniya,
    d.pol_rebenka,
    (
        SELECT COUNT(*)
        FROM materi m2
        WHERE m2.id_palaty = p.id_palaty
    ) AS kolichestvo_rozhenic_v_palate
FROM palaty p
JOIN materi m ON p.id_palaty = m.id_palaty
JOIN vrachi v ON m.id_vracha = v.id_vracha
JOIN deti d ON m.id_materi = d.id_materi
ORDER BY p.nomer_palaty, m.familiya, m.imya;

-- 6. Вывод списка детей с весом выше некоторой величины
--    пример: выше 3500 грамм
SELECT
    CONCAT(m.familiya, ' ', m.imya, ' ', m.otchestvo) AS fio_materi,
    d.pol_rebenka,
    d.ves_gramm,
    d.rost_sm,
    d.data_rozhdeniya
FROM deti d
JOIN materi m ON d.id_materi = m.id_materi
WHERE d.ves_gramm > 3500
ORDER BY d.ves_gramm DESC;

-- 7. Вывод списка рожениц, лежащих в одной и той же палате,
--    с указанием даты рождения ребенка и ФИО лечащего врача
--    пример: палата 101
SELECT
    CONCAT(m.familiya, ' ', m.imya, ' ', m.otchestvo) AS fio_materi,
    p.nomer_palaty,
    d.data_rozhdeniya,
    CONCAT(v.familiya, ' ', v.imya, ' ', v.otchestvo) AS fio_vracha
FROM materi m
JOIN palaty p ON m.id_palaty = p.id_palaty
JOIN vrachi v ON m.id_vracha = v.id_vracha
JOIN deti d ON m.id_materi = d.id_materi
WHERE p.nomer_palaty = '101'
ORDER BY m.familiya, m.imya;
```
