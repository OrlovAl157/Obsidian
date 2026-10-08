---
tags: [sql, select, основы, выбор-данных]
---

# 📊 SQL SELECT — основы выборки данных

> Краткая шпаргалка по SELECT: как выбирать столбцы, выводить все данные, ограничивать результаты, использовать псевдонимы и сортировать результаты.

## Содержание

- [[#Справка по ключевым словам|Справка по ключевым словам]]
- [[#🎯 Выбор столбцов|Выбор столбцов]]
- [[#⭐ Все столбцы оператор *|Все столбцы (оператор *)]]
- [[#🏷 Псевдонимы столбцов AS|Псевдонимы столбцов (AS)]]
- [[#🔢 Ограничение результата LIMIT и OFFSET|Ограничение результата (LIMIT и OFFSET)]]
- [[#📑 Сортировка ORDER BY|Сортировка (ORDER BY)]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]

## Справка по ключевым словам

| Ключевое слово | Что делает | Синтаксис |
| --- | --- | --- |
| `SELECT` | Выбирает столбцы для вывода | `SELECT col1, col2 FROM table` |
| `FROM` | Указывает таблицу-источник | `FROM TableName` |
| `*` | Выбирает все столбцы | `SELECT *` |
| `AS` | Задаёт псевдоним столбцу | `SELECT col AS alias` |
| `LIMIT` | Ограничивает количество строк | `LIMIT 5` |
| `OFFSET` | Пропускает количество строк | `OFFSET 10` |
| `ORDER BY` | Сортирует результат | `ORDER BY col ASC/DESC` |
| `ASC` | Сортировка по возрастанию (по умолчанию) | `ORDER BY col ASC` |
| `DESC` | Сортировка по убыванию | `ORDER BY col DESC` |

---




## 🎯 Выбор столбцов

**Назначение:** выбрать конкретные столбцы из таблицы.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `SELECT col1, col2` | Выбирает конкретные столбцы | `SELECT name, email FROM Users` |
| `SELECT col1, col2, col3` | Можно выбирать несколько столбцов | `SELECT id, title, director FROM Films` |

**Примеры:**

```sql
SELECT name, email
FROM Users;

-- Результат:
-- name      | email
-- -----------+-----------------------
-- Alice     | alice@example.com
-- Bob       | bob@example.com
-- Charlie   | charlie@example.com
```

```sql
SELECT id, title, director, release_year
FROM Films;

-- Результат:
-- id | title              | director        | release_year
-- ---+--------------------+-----------------+-------------
-- 1  | Inception          | Christopher Nolan | 2010
-- 2  | The Matrix         | Wachowskis      | 1999
-- 3  | Interstellar       | Christopher Nolan | 2014
```

**Важное:** порядок столбцов в SELECT определяет порядок в результате запроса.

---




## ⭐ Все столбцы (оператор *)

**Назначение:** выбрать все столбцы из таблицы без необходимости перечислять их по одному.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `SELECT *` | Выбирает все столбцы | `SELECT * FROM Users` |
| `SELECT * FROM table` | Выводит все данные из таблицы | `SELECT * FROM Products` |

**Примеры:**

```sql
SELECT *
FROM Users;

-- Результат содержит все столбцы таблицы Users:
-- id | name    | email              | age | city
-- ---+---------+--------------------+-----+----------
-- 1  | Alice   | alice@example.com  | 28  | New York
-- 2  | Bob     | bob@example.com    | 34  | London
-- 3  | Charlie | charlie@example.com| 25  | Paris
```

**Важное:** хотя `SELECT *` удобен для быстрых проверок, в продакшене лучше явно указывать нужные столбцы для:
- Экономии пропускной способности (меньше данных передаётся)
- Явности кода (ясно какие столбцы используются)
- Защиты от добавления нежелательных новых столбцов

---




## 🏷 Псевдонимы столбцов (AS)

**Назначение:** дать столбцу новое имя для удобства чтения и работы в запросе.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `SELECT col AS alias` | Переименовывает столбец в результате | `SELECT name AS full_name` |
| `SELECT col AS 'New Name'` | Псевдоним с пробелами — в кавычках | `SELECT price AS 'Product Price'` |

**Примеры:**

```sql
SELECT 
    id AS user_id,
    name AS full_name,
    email AS email_address
FROM Users;

-- Результат:
-- user_id | full_name | email_address
-- --------+-----------+---------------------
-- 1       | Alice     | alice@example.com
-- 2       | Bob       | bob@example.com
```

```sql
SELECT 
    title AS 'Film Title',
    director AS 'Director Name',
    release_year AS 'Year'
FROM Films;

-- Результат:
-- Film Title  | Director Name   | Year
-- ------------+-----------------+------
-- Inception   | Christopher Nolan | 2010
-- The Matrix  | Wachowskis      | 1999
```

**Важное:** 
- Псевдоним не может быть использован в `WHERE` (WHERE выполняется ДО SELECT)
- Но псевдоним можно использовать в `ORDER BY`
- Ключевое слово `AS` опционально: `SELECT col alias` работает так же как `SELECT col AS alias`

---




## 🔢 Ограничение результата (LIMIT и OFFSET)

**Назначение:** контролировать количество возвращаемых строк и скипать строки.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `LIMIT n` | Выводит первые `n` строк | `LIMIT 5` |
| `OFFSET n` | Пропускает первые `n` строк | `OFFSET 10` |
| `LIMIT offset, count` | Альтернативный синтаксис: пропустить `offset`, вывести `count` | `LIMIT 10, 5` |

**Примеры:**

```sql
SELECT *
FROM Users
LIMIT 3;

-- Результат: первые 3 пользователя
-- id | name    | email
-- ---+---------+--------------------
-- 1  | Alice   | alice@example.com
-- 2  | Bob     | bob@example.com
-- 3  | Charlie | charlie@example.com
```

```sql
SELECT *
FROM Users
OFFSET 2 LIMIT 3;

-- Результат: строки 3, 4, 5 (пропускаем 2, берём 3)
-- id | name    | email
-- ---+---------+--------------------
-- 3  | Charlie | charlie@example.com
-- 4  | Diana   | diana@example.com
-- 5  | Eve     | eve@example.com
```

```sql
SELECT *
FROM Users
LIMIT 2, 3;

-- Альтернативный синтаксис: пропустить 2, вывести 3
-- id | name    | email
-- ---+---------+--------------------
-- 3  | Charlie | charlie@example.com
-- 4  | Diana   | diana@example.com
-- 5  | Eve     | eve@example.com
```

**Важное:** 
- `OFFSET` считается с 0, а не с 1
- `LIMIT` без `ORDER BY` может быть нестабилен (порядок не гарантирован)
- Для пагинации всегда используй `ORDER BY` перед `LIMIT`

---




## 📑 Сортировка (ORDER BY)

**Назначение:** упорядочить результаты по одному или нескольким столбцам.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `ORDER BY col` | Сортирует по возрастанию (ASC по умолчанию) | `ORDER BY name` |
| `ORDER BY col ASC` | Явная сортировка по возрастанию | `ORDER BY age ASC` |
| `ORDER BY col DESC` | Сортирует по убыванию | `ORDER BY age DESC` |
| `ORDER BY col1, col2` | Сортирует по нескольким столбцам | `ORDER BY city, name` |

**Примеры:**

```sql
SELECT name, age
FROM Users
ORDER BY age ASC;

-- Результат: сортировка по возрасту от меньшего к большему
-- name    | age
-- --------+-----
-- Charlie | 25
-- Alice   | 28
-- Bob     | 34
```

```sql
SELECT name, age
FROM Users
ORDER BY age DESC;

-- Результат: сортировка по возрасту от большего к меньшему
-- name    | age
-- --------+-----
-- Bob     | 34
-- Alice   | 28
-- Charlie | 25
```

```sql
SELECT name, city
FROM Users
ORDER BY city ASC, name ASC;

-- Результат: сортировка по городу, потом по имени
-- name    | city
-- --------+-----------
-- Bob     | London
-- Charlie | Paris
-- Alice   | New York
```

**Важное:** 
- `ASC` — по умолчанию, не нужно писать явно
- Можно сортировать по столбцам, которые не входят в SELECT
- Можно сортировать по псевдониму (AS): `SELECT age AS user_age FROM Users ORDER BY user_age`
- Для дат и чисел сортировка работает математически

---




## ⚡ Быстрые примеры

```sql
-- Выбрать 5 самых молодых пользователей
SELECT name, age
FROM Users
ORDER BY age ASC
LIMIT 5;

-- Выбрать 10-ый по 15-ый фильм по году выхода
SELECT title, release_year
FROM Films
ORDER BY release_year DESC
LIMIT 10, 5;

-- Выбрать уникальные города (с DISTINCT)
SELECT DISTINCT city
FROM Users
ORDER BY city;

-- Выбрать последние 3 добавленных продукта
SELECT id, title, created_at
FROM Products
ORDER BY created_at DESC
LIMIT 3;
```

---

## 💡 Практические замечания

- **SELECT ** vs SELECT col1, col2**: В продакшене всегда указывай конкретные столбцы
- **LIMIT без ORDER BY**: Опасно для критичных данных — порядок не гарантирован
- **Псевдонимы в WHERE**: Нельзя использовать, используй `HAVING` для агрегатов
- **Пагинация**: Всегда комбинируй `ORDER BY` + `LIMIT` + `OFFSET`
- **Производительность**: Большие OFFSET (10000+) могут быть медленными — рассмотри cursor-based pagination
- **Сортировка по вычисляемым полям**: Работает, но может быть медленней
- **NULL при сортировке**: В MySQL NULL считается минимальным значением

---

## ⚠️ Частые ошибки

**❌ ОШИБКА:**
```sql
SELECT name, age
WHERE age > 25
FROM Users;
```
❌ Синтаксис неверный! WHERE идёт ПОСЛЕ FROM

**✅ ВЕРНО:**
```sql
SELECT name, age
FROM Users
WHERE age > 25;
```

---

**❌ ОШИБКА:**
```sql
SELECT name AS user_name
FROM Users
WHERE user_name = 'Alice';
```
❌ Псевдонимы не доступны в WHERE!

**✅ ВЕРНО:**
```sql
SELECT name AS user_name
FROM Users
WHERE name = 'Alice';
```

---

**❌ ОШИБКА:**
```sql
SELECT *
FROM Users
LIMIT 10
ORDER BY age;
```
❌ ORDER BY идёт ПЕРЕД LIMIT!

**✅ ВЕРНО:**
```sql
SELECT *
FROM Users
ORDER BY age
LIMIT 10;
```
