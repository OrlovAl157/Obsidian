---
tags: [postgresql, select, sql]
difficulty: beginner
---

# 📊 SELECT — памятка

> `SELECT` извлекает данные из таблицы. В PostgreSQL поддерживает все стандартные конструкции плюс ряд расширений: `DISTINCT ON`, `FETCH`, `NULLS FIRST/LAST`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Базовый синтаксис|Базовый синтаксис]]
- [[#🔵 Фильтрация WHERE|WHERE]]
- [[#🔴 Сортировка ORDER BY|ORDER BY]]
- [[#🟡 Ограничение LIMIT и OFFSET|LIMIT и OFFSET]]
- [[#🟣 DISTINCT и DISTINCT ON|DISTINCT]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает |
|---|---|
| `SELECT col FROM t` | извлечь данные |
| `SELECT *` | все столбцы |
| `WHERE condition` | фильтрация строк |
| `ORDER BY col ASC/DESC` | сортировка |
| `LIMIT n` | ограничить количество строк |
| `OFFSET n` | пропустить n строк |
| `DISTINCT` | убрать дубликаты |
| `DISTINCT ON (col)` | уникальные по полю (PostgreSQL!) |
| `AS` | псевдоним |

---

## 🟢 Базовый синтаксис

```sql
SELECT column1, column2
FROM table_name;

-- Все столбцы
SELECT * FROM Books;

-- Псевдонимы
SELECT title AS book_title, price AS cost
FROM Books;

-- Вычисляемые столбцы
SELECT title, price * 0.9 AS discounted_price
FROM Books;
```

---

## 🔵 Фильтрация WHERE

```sql
SELECT * FROM Books WHERE price > 10;
SELECT * FROM Books WHERE author = 'King' AND price < 20;
SELECT * FROM Books WHERE price BETWEEN 5 AND 15;
SELECT * FROM Books WHERE author IN ('King', 'Orwell');
SELECT * FROM Books WHERE title LIKE '%Ring%';
SELECT * FROM Books WHERE title ILIKE '%ring%';  -- регистронезависимо (PostgreSQL!)
SELECT * FROM Books WHERE description IS NULL;
SELECT * FROM Books WHERE description IS NOT NULL;
```

---

## 🔴 Сортировка ORDER BY

```sql
SELECT * FROM Books ORDER BY price ASC;
SELECT * FROM Books ORDER BY price DESC;
SELECT * FROM Books ORDER BY author ASC, price DESC;

-- NULLS FIRST/LAST — управление позицией NULL (PostgreSQL!)
SELECT * FROM Books ORDER BY price ASC NULLS LAST;
SELECT * FROM Books ORDER BY price DESC NULLS FIRST;
```

---

## 🟡 Ограничение LIMIT и OFFSET

```sql
-- Первые 10 записей
SELECT * FROM Books LIMIT 10;

-- Пропустить 20, взять следующие 10 (пагинация)
SELECT * FROM Books LIMIT 10 OFFSET 20;

-- Стандартный SQL-синтаксис (PostgreSQL поддерживает оба варианта)
SELECT * FROM Books
FETCH FIRST 10 ROWS ONLY;
```

---

## 🟣 DISTINCT и DISTINCT ON

```sql
-- DISTINCT — убрать полные дубликаты строк
SELECT DISTINCT author FROM Books;

-- DISTINCT ON — уникальные по полю, оставить первую строку в группе (PostgreSQL!)
-- Для каждого автора — самая дорогая книга
SELECT DISTINCT ON (author) author, title, price
FROM Books
ORDER BY author, price DESC;
```

**Важно:** при `DISTINCT ON` обязателен `ORDER BY`, и первые поля `ORDER BY` должны совпадать с полями в `DISTINCT ON`.

---

## ⚡ Быстрые примеры

```sql
-- Топ-5 самых дорогих книг
SELECT title, price FROM Books
ORDER BY price DESC
LIMIT 5;

-- Авторы без дублей
SELECT DISTINCT author FROM Books ORDER BY author;

-- Поиск без учёта регистра
SELECT * FROM Books WHERE title ILIKE '%lord%';

-- Нулевые цены в конце
SELECT title, price FROM Books ORDER BY price ASC NULLS LAST;
```

---

## ⚠️ Частые ошибки

```sql
-- ❌ ILIKE есть только в PostgreSQL, не работает в MySQL
SELECT * FROM Books WHERE title ILIKE '%lord%';   -- ✅ PostgreSQL
-- В MySQL: WHERE LOWER(title) LIKE LOWER('%lord%')

-- ❌ DISTINCT ON — только PostgreSQL
SELECT DISTINCT ON (author) * FROM Books;         -- ✅ PostgreSQL
```

---

## ✅ Главные правила

✅ `ILIKE` — регистронезависимый поиск без `LOWER()`  
✅ `DISTINCT ON` — уникальные по полю с сохранением полной строки  
✅ `NULLS FIRST/LAST` — явное управление позицией NULL при сортировке  
✅ `FETCH FIRST n ROWS ONLY` — стандартный SQL вместо `LIMIT`

---

## 🔗 Связанные темы

- [[01 — ⚖️ Операторы и условия]]
- [[03 — 🔗 JOIN]]
- [[05 — 🧮 GROUP BY и HAVING]]
- [[06 — 🔎 Подзапросы]]

---

#postgresql/sql #select
