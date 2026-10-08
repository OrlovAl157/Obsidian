---
tags: [postgresql, sql, join]
difficulty: intermediate
---

# 🔗 JOIN — памятка

> JOIN объединяет строки из двух или более таблиц по условию. В PostgreSQL все стандартные виды JOIN работают полностью. Синтаксис идентичен стандарту SQL.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 INNER JOIN|INNER JOIN]]
- [[#🔵 LEFT / RIGHT JOIN|LEFT / RIGHT JOIN]]
- [[#🔴 FULL JOIN|FULL JOIN]]
- [[#🟡 CROSS JOIN|CROSS JOIN]]
- [[#🟣 Самосоединение|Самосоединение]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Тип | Что возвращает |
|---|---|
| `INNER JOIN` | только совпадающие строки из обеих таблиц |
| `LEFT JOIN` | все строки левой + совпадающие из правой |
| `RIGHT JOIN` | все строки правой + совпадающие из левой |
| `FULL JOIN` | все строки из обеих таблиц |
| `CROSS JOIN` | декартово произведение |

---

## 📊 Общая схема

```
A        B          INNER      LEFT       RIGHT      FULL
┌──┐    ┌──┐        ┌──┐       ┌──┐       ┌──┐       ┌──┐
│  │    │  │        │██│       │██│       │  │       │██│
│██│    │██│   →    │██│       │██│       │██│       │██│
│  │    │  │        └──┘       │  │       │██│       │  │
└──┘    └──┘                   └──┘       └──┘       └──┘
```

---

## 🟢 INNER JOIN

```sql
SELECT b.title, a.name
FROM Books b
INNER JOIN Authors a ON b.author_id = a.id;

-- JOIN = INNER JOIN (можно опустить INNER)
SELECT b.title, a.name
FROM Books b
JOIN Authors a ON b.author_id = a.id;

-- Несколько JOIN
SELECT b.title, a.name, g.name AS genre
FROM Books b
JOIN Authors a ON b.author_id = a.id
JOIN Genres g ON b.genre_id = g.id;
```

---

## 🔵 LEFT / RIGHT JOIN

```sql
-- LEFT JOIN — все книги, даже без автора
SELECT b.title, a.name
FROM Books b
LEFT JOIN Authors a ON b.author_id = a.id;

-- Найти книги БЕЗ автора
SELECT b.title
FROM Books b
LEFT JOIN Authors a ON b.author_id = a.id
WHERE a.id IS NULL;

-- RIGHT JOIN — все авторы, даже без книг
SELECT b.title, a.name
FROM Books b
RIGHT JOIN Authors a ON b.author_id = a.id;
```

---

## 🔴 FULL JOIN

```sql
-- FULL JOIN — все строки из обеих таблиц
SELECT b.title, a.name
FROM Books b
FULL JOIN Authors a ON b.author_id = a.id;

-- Найти несвязанные записи в обеих таблицах
SELECT b.title, a.name
FROM Books b
FULL JOIN Authors a ON b.author_id = a.id
WHERE b.id IS NULL OR a.id IS NULL;
```

---

## 🟡 CROSS JOIN

```sql
-- Декартово произведение — каждая строка с каждой
SELECT a.name, b.title
FROM Authors a
CROSS JOIN Books b;
-- Осторожно: 100 авторов × 1000 книг = 100 000 строк!
```

---

## 🟣 Самосоединение

```sql
-- Найти сотрудников и их менеджеров
SELECT e.name AS employee, m.name AS manager
FROM Employees e
LEFT JOIN Employees m ON e.manager_id = m.id;
```

---

## ✅ Главные правила

✅ `INNER JOIN` = `JOIN` — можно сокращать  
✅ `LEFT JOIN` — все строки левой таблицы, `NULL` если нет пары  
✅ `FULL JOIN` — поддерживается в PostgreSQL, нет в MySQL  
✅ `WHERE a.id IS NULL` после LEFT JOIN — найти "сироты"  

---

## 🔗 Связанные темы

- [[02 — 📊 SELECT]]
- [[03.1 — 🔗 JOIN шпаргалка]]
- [[05 — 🧮 GROUP BY и HAVING]]

---

#postgresql/sql #join
