# Запросы для БД «Крупнейшие города мира» — вариант 7

```sql
USE krupneyshie_goroda_mira;

-- 1. Простая выборка и сортировка (SELECT, WHERE, ORDER BY)

-- 1.1. Вывести города Азии
SELECT nazvanie_goroda, strana, materik
FROM goroda_mira
WHERE materik = 'Азия';

-- 1.2. Вывести города с населением больше 20 млн, отсортировать по убыванию населения
SELECT nazvanie_goroda, strana, naselenie_mln
FROM goroda_mira
WHERE naselenie_mln > 20
ORDER BY naselenie_mln DESC;

-- 1.3. Вывести города Северной Америки, отсортировать по названию
SELECT nazvanie_goroda, strana, ploshad_km2
FROM goroda_mira
WHERE materik = 'Северная Америка'
ORDER BY nazvanie_goroda ASC;

-- 2. Вычисления и фильтрация по диапазонам (BETWEEN, LIKE, вычисляемые поля)

-- 2.1. Вывести города, у которых население от 20 до 30 млн
SELECT nazvanie_goroda, strana, naselenie_mln
FROM goroda_mira
WHERE naselenie_mln BETWEEN 20 AND 30;

-- 2.2. Вывести города, в названии которых есть буква "о"
SELECT nazvanie_goroda, strana
FROM goroda_mira
WHERE nazvanie_goroda LIKE '%о%';

-- 2.3. Вывести город, страну и плотность населения как вычисляемое поле
SELECT
    nazvanie_goroda,
    strana,
    naselenie_mln,
    ploshad_km2,
    ROUND((naselenie_mln * 1000000) / ploshad_km2, 2) AS plotnost_naseleniya
FROM goroda_mira;

-- 3. Группировка и агрегатные функции (GROUP BY, HAVING, MIN, MAX, AVG, SUM, COUNT)

-- 3.1. Посчитать количество городов по материкам
SELECT materik, COUNT(*) AS kolichestvo_gorodov
FROM goroda_mira
GROUP BY materik;

-- 3.2. Найти максимальное и минимальное население по материкам
SELECT
    materik,
    MAX(naselenie_mln) AS max_naselenie,
    MIN(naselenie_mln) AS min_naselenie
FROM goroda_mira
GROUP BY materik;

-- 3.3. Вывести материки, где среднее население городов больше 20 млн
SELECT
    materik,
    AVG(naselenie_mln) AS srednee_naselenie
FROM goroda_mira
GROUP BY materik
HAVING AVG(naselenie_mln) > 20;

-- 4. Комбинированные и практические задания

-- 4.1. Вывести страны, у которых больше одного города в таблице
SELECT
    strana,
    COUNT(*) AS kolichestvo_gorodov
FROM goroda_mira
GROUP BY strana
HAVING COUNT(*) > 1;

-- 4.2. Вывести города, основанные раньше 1600 года и с населением больше 20 млн
SELECT
    nazvanie_goroda,
    strana,
    god_osnovaniya,
    naselenie_mln
FROM goroda_mira
WHERE god_osnovaniya < 1600
  AND naselenie_mln > 20
ORDER BY naselenie_mln DESC;
```
