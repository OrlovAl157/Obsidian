---
tags: [postgresql, sql, group-by]
difficulty: beginner
---

# 🧮 GROUP BY и HAVING — памятка

> `GROUP BY` группирует строки по значению поля. `HAVING` фильтрует группы (как `WHERE`, но после агрегации). В PostgreSQL можно группировать по псевдониму из SELECT и использовать `GROUPING SETS`, `ROLLUP`, `CUBE`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 GROUP BY|GROUP BY]]
- [[#🔵 HAVING|HAVING]]
- [[#🔴 GROUPING SETS / ROLLUP / CUBE|GROUPING SETS]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает |
|---|---|
| `GROUP BY col` | группировать по полю |
| `HAVING condition` | фильтровать группы |
| `GROUPING SETS` | несколько группировок за раз |
| `ROLLUP` | иерархические итоги |
| `CUBE` | все комбинации группировок |

---

## 🟢 GROUP BY

```sql
-- Количество книг по авторам
SELECT author, COUNT(*) AS book_count
FROM Books
GROUP BY author;

-- Средняя цена по авторам (только авторы с книгами дороже 5)
SELECT author, AVG(price) AS avg_price, MAX(price) AS max_price
FROM Books
WHERE price > 5
GROUP BY author
ORDER BY avg_price DESC;

-- Группировка по нескольким полям
SELECT author, genre, COUNT(*) AS count
FROM Books
GROUP BY author, genre;
```

---

## 🔵 HAVING

```sql
-- Авторы с более чем 3 книгами
SELECT author, COUNT(*) AS book_count
FROM Books
GROUP BY author
HAVING COUNT(*) > 3;

-- WHERE — до группировки, HAVING — после
SELECT author, AVG(price) AS avg_price
FROM Books
WHERE price > 0          -- фильтр строк до группировки
GROUP BY author
HAVING AVG(price) > 10;  -- фильтр групп после агрегации
```

---

## 🔴 GROUPING SETS / ROLLUP / CUBE

```sql
-- GROUPING SETS — несколько группировок в одном запросе
SELECT author, genre, COUNT(*)
FROM Books
GROUP BY GROUPING SETS (
    (author, genre),   -- по обоим полям
    (author),          -- только по автору
    (genre),           -- только по жанру
    ()                 -- итого по всем
);

-- ROLLUP — иерархические итоги (от детального к общему)
SELECT year, month, SUM(sales)
FROM Sales
GROUP BY ROLLUP (year, month);
-- year + month → year → итого

-- CUBE — все комбинации
SELECT country, city, SUM(sales)
FROM Sales
GROUP BY CUBE (country, city);
```

---

## ✅ Главные правила

✅ `WHERE` — фильтр до группировки, `HAVING` — после  
✅ В SELECT без агрегации можно указывать только поля из GROUP BY  
✅ `ROLLUP`, `CUBE`, `GROUPING SETS` — только PostgreSQL (нет в SQLite)  
✅ `COUNT(*)` считает все строки, `COUNT(col)` — только не-NULL  

---

## 🔗 Связанные темы

- [[02 — 📊 SELECT]]
- [[../02 — ⚙️ Функции/03 — 📊 Агрегатные функции]]
- [[../04 — 🚀 Продвинутые темы/02 — 🪟 Оконные функции]]

---

#postgresql/sql #group-by
