---
tags: [sql, основы, select, where, like]
difficulty: beginner
---

# 📊 Основы SQL — памятка

> Базовые команды SQL для извлечения, фильтрации и сортировки данных. `SELECT` и `FROM` — обязательная основа любого запроса. `WHERE` фильтрует строки, `ORDER BY` сортирует, `LIMIT` ограничивает количество. `LIKE` ищет по шаблону, `DISTINCT` убирает дубликаты.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Порядок выполнения запроса|Порядок выполнения]]
- [[#🟢 SELECT и FROM — извлечение данных|SELECT и FROM]]
- [[#🔵 WHERE — фильтрация|WHERE]]
- [[#🔴 AND / OR / IN / NOT|AND / OR / IN / NOT]]
- [[#🟡 ORDER BY — сортировка|ORDER BY]]
- [[#🟣 LIMIT — ограничение строк|LIMIT]]
- [[#✂️ LIKE — поиск по шаблону|LIKE]]
- [[#🔤 Регистрозависимость|Регистрозависимость]]
- [[#📐 Вычисляемые поля|Вычисляемые поля]]
- [[#🔗 CONCAT и CONCAT_WS|CONCAT и CONCAT_WS]]
- [[#⚡ DISTINCT — уникальные значения|DISTINCT]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Команда | Назначение |
|---|---|
| `SELECT` | выбрать столбцы |
| `FROM` | указать таблицу |
| `WHERE` | отфильтровать строки |
| `AND / OR` | объединить условия |
| `IN / NOT IN` | проверить вхождение в список |
| `BETWEEN` | проверить диапазон (включая границы) |
| `IS NULL / IS NOT NULL` | проверить NULL |
| `LIKE` | поиск по шаблону |
| `ORDER BY ... ASC/DESC` | сортировать результат |
| `LIMIT n` | ограничить количество строк |
| `LIMIT offset, n` | пропустить offset строк, взять n |
| `DISTINCT` | убрать дубликаты |
| `CONCAT(...)` | объединить строки |
| `CONCAT_WS(sep, ...)` | объединить строки через разделитель |

---

## 📊 Порядок выполнения запроса

```
SELECT столбцы          ← 3. выбираются столбцы
FROM таблица            ← 1. выбирается таблица
WHERE условие           ← 2. фильтруются строки
ORDER BY поле           ← 5. сортируется результат
LIMIT количество;       ← 6. ограничивается количество

Порядок обработки:
FROM → WHERE → SELECT → DISTINCT → ORDER BY → LIMIT
```

**Важное:** псевдонимы из `SELECT AS` нельзя использовать в `WHERE` — там они ещё не существуют. В `ORDER BY` — можно.

---

## 🟢 SELECT и FROM — извлечение данных

```sql
-- Все столбцы
SELECT * FROM Films;

-- Конкретные столбцы
SELECT title, director FROM Films;

-- С псевдонимом
SELECT title AS name, director AS author FROM Films;

-- Вычисляемый псевдоним
SELECT title, release_year AS year FROM Films;
```

**Пример — вывести название и режиссёра первых 5 фильмов:**

```sql
SELECT title, director
FROM Films
LIMIT 5;
```

```
title            | director
-----------------+----------------------
Inception        | Christopher Nolan
The Matrix       | Lana Wachowski
Interstellar     | Christopher Nolan
Pulp Fiction     | Quentin Tarantino
The Dark Knight  | Christopher Nolan
```

**Важное:** порядок строк без `ORDER BY` не гарантирован. Порядок столбцов в результате совпадает с порядком в `SELECT`, не с порядком в таблице.

---

## 🔵 WHERE — фильтрация

```sql
-- Операторы сравнения
SELECT * FROM Songs WHERE streams > 1000000;
SELECT * FROM Films WHERE release_year != 2020;
SELECT * FROM Films WHERE release_year <> 2020;  -- то же что !=

-- BETWEEN — диапазон (включая границы)
SELECT * FROM Films WHERE release_year BETWEEN 2010 AND 2020;
-- эквивалентно: WHERE release_year >= 2010 AND release_year <= 2020

-- NULL — специальная проверка
SELECT * FROM Users WHERE email IS NULL;
SELECT * FROM Users WHERE email IS NOT NULL;
-- ❌ WHERE email = NULL — не работает!
```

**Пример — найти популярные песни:**

```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

```
trackname        | artist      | streams
-----------------+-------------+------------
Blinding Lights  | The Weeknd  | 3400000000
Shape of You     | Ed Sheeran  | 2500000000
```

**Пример с BETWEEN:**

```sql
SELECT title, release_year
FROM Films
WHERE release_year BETWEEN 2010 AND 2020;
```

---

```sql
-- AND — все условия должны быть истинны
SELECT * FROM Songs
WHERE streams > 50000 AND place <= 3;

-- OR — хотя бы одно условие
SELECT * FROM Songs
WHERE artist = 'Heart' OR artist = 'Kate Bush';

-- IN — вместо нескольких OR
SELECT * FROM Songs
WHERE artist IN ('Heart', 'Kate Bush', 'Fleetwood Mac');

-- NOT IN — исключить список
SELECT * FROM Songs
WHERE artist NOT IN ('Heart', 'Kate Bush');

-- NOT — отрицание
SELECT * FROM Songs
WHERE NOT artist = 'The Sounds';

-- Скобки для явного порядка
SELECT * FROM Songs
WHERE streams > 50000 AND (artist = 'Heart' OR artist = 'Kate Bush');
```

**Приоритет:** `IN`, `NOT IN` → `NOT` → `AND` → `OR`. Используй скобки чтобы не ошибиться.

---

## 🟡 ORDER BY — сортировка

```sql
-- По возрастанию (по умолчанию)
SELECT title FROM Films ORDER BY release_year;
SELECT title FROM Films ORDER BY release_year ASC;  -- то же самое

-- По убыванию
SELECT title FROM Films ORDER BY release_year DESC;

-- По нескольким полям
SELECT title, release_year FROM Films
ORDER BY release_year DESC, title ASC;
-- сначала по году (новые вперёд), при одинаковом годе — по названию A-Z

-- По псевдониму (можно в ORDER BY)
SELECT title AS name FROM Films ORDER BY name;
-- По исходному полю тоже можно
SELECT title AS name FROM Films ORDER BY title;
```

---

## 🟣 LIMIT — ограничение строк

```sql
-- Первые 5 строк
SELECT * FROM Films LIMIT 5;

-- Пропустить 10, взять следующие 5
SELECT * FROM Films LIMIT 10, 5;
-- эквивалентно: LIMIT 5 OFFSET 10

-- Топ-3 самых прослушиваемых песни
SELECT title, streams FROM Songs
ORDER BY streams DESC
LIMIT 3;
```

---

## ✂️ LIKE — поиск по шаблону

| Метасимвол | Значение |
|---|---|
| `%` | любая последовательность символов (0 и более) |
| `_` | ровно один любой символ |

```sql
-- Начинается с 'The'
SELECT * FROM Films WHERE title LIKE 'The%';

-- Содержит слово 'You'
SELECT * FROM Songs WHERE trackname LIKE '%You%';

-- Заканчивается на 'ing'
SELECT * FROM Songs WHERE trackname LIKE '%ing';

-- Ровно 3 символа потом пробел
SELECT * FROM Songs WHERE trackname LIKE '___ %';

-- Поиск самого метасимвола % через ESCAPE
SELECT * FROM Data WHERE value LIKE '%/%' ESCAPE '/';
-- / перед % означает: % — это буквальный символ, не метасимвол

-- LIKE не учитывает регистр по умолчанию
WHERE title LIKE 'the%'   -- найдёт 'The', 'THE', 'the'
```

---

## 🔤 Регистрозависимость

По умолчанию сравнения в SQL **не учитывают регистр**. Для учёта регистра — `BINARY`:

```sql
-- Найти только с заглавной 'Y'
SELECT * FROM Songs WHERE trackname LIKE BINARY '%You%';
-- 'You' найдётся, 'you' и 'YOU' — нет

-- Три способа (эквивалентны):
WHERE trackname LIKE BINARY '%You%'
WHERE trackname LIKE CAST('%You%' AS BINARY)
WHERE trackname LIKE CONVERT('%You%', BINARY)

-- Работает и для оператора =
WHERE artist = BINARY 'The Weeknd'   -- только точное совпадение регистра
```

| Синтаксис | Когда использовать |
|---|---|
| `BINARY` | кратко, в рабочем коде |
| `CAST(... AS BINARY)` | стандартный SQL, наглядно |
| `CONVERT(..., BINARY)` | MySQL-синтаксис, альтернатива CAST |

---

## 📐 Вычисляемые поля

```sql
-- Арифметика прямо в SELECT
SELECT item,
       price * quantity AS revenue
FROM VibeStore;

-- Вычисление с несколькими полями
SELECT name,
       2026 - birth_year AS age,
       salary * 12 AS annual_salary
FROM Employees;

-- ❌ Псевдоним нельзя в WHERE
SELECT price * quantity AS revenue FROM VibeStore
WHERE revenue > 5000;   -- ошибка!

-- ✅ Повторяй выражение в WHERE
SELECT price * quantity AS revenue FROM VibeStore
WHERE price * quantity > 5000;

-- ✅ Псевдоним можно в ORDER BY
SELECT item, price * quantity AS revenue FROM VibeStore
ORDER BY revenue DESC;
```

**Важное:** если один из операндов `NULL` — результат `NULL`.

---

## 🔗 CONCAT и CONCAT_WS

```sql
-- CONCAT — объединить строки
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM Users;
-- 'John' + ' ' + 'Doe' → 'John Doe'

-- Если хотя бы один аргумент NULL — результат NULL
SELECT CONCAT('Hello', NULL, '!');   -- → NULL

-- CONCAT_WS — с разделителем, игнорирует NULL
SELECT CONCAT_WS(', ', item, color, size) AS description FROM Clothes;
-- 'Hoodie', 'black', 'M' → 'Hoodie, black, M'

-- CONCAT_WS игнорирует NULL значения (не разделитель!)
SELECT CONCAT_WS(' | ', artist, trackname, genre) FROM Songs;
-- если genre = NULL → 'Artist | Track' (без лишнего |)
```

---

## ⚡ DISTINCT — уникальные значения

```sql
-- Уникальные исполнители
SELECT DISTINCT artist FROM Songs ORDER BY artist;

-- Уникальные комбинации
SELECT DISTINCT artist, genre FROM Songs ORDER BY artist;

-- С COUNT
SELECT COUNT(DISTINCT artist) AS unique_artists FROM Songs;

-- DISTINCT обрабатывается после WHERE
SELECT DISTINCT artist FROM Songs
WHERE streams > 100000;   -- уникальные исполнители популярных песен
```

---

## ⚡ Быстрые примеры

```sql
-- Базовый запрос
SELECT name, age FROM Users WHERE age > 18 ORDER BY age DESC LIMIT 10;

-- С IN и BETWEEN
SELECT * FROM Songs
WHERE artist IN ('The Weeknd', 'Ed Sheeran')
  AND streams BETWEEN 1000000 AND 5000000;

-- Вычисляемое поле + сортировка
SELECT item, price * quantity AS revenue
FROM VibeStore
ORDER BY revenue DESC
LIMIT 5;

-- LIKE с ESCAPE
SELECT * FROM Products WHERE code LIKE '%\_%' ESCAPE '\';

-- DISTINCT + COUNT
SELECT COUNT(DISTINCT country) AS countries FROM Users;

-- Объединение строк
SELECT CONCAT_WS(' - ', artist, trackname) AS song FROM Songs;

-- Регистрозависимый поиск
SELECT * FROM Users WHERE email LIKE BINARY '%@Gmail.com';
```

---

## ⚠️ Частые ошибки

**❌ Сравнение с NULL через =:**
```sql
WHERE email = NULL    -- ❌ всегда FALSE
WHERE email IS NULL   -- ✅
```

**❌ Псевдоним в WHERE:**
```sql
SELECT price * 0.9 AS discounted FROM Books
WHERE discounted < 10;     -- ❌ ошибка!
WHERE price * 0.9 < 10;    -- ✅ повтори выражение
```

**❌ Приоритет AND и OR без скобок:**
```sql
WHERE age > 18 OR city = 'Moscow' AND country = 'Russia'
-- AND выполнится первым — результат неожиданный!
WHERE age > 18 OR (city = 'Moscow' AND country = 'Russia')  -- ✅
```

**❌ LIKE без % — работает как =:**
```sql
WHERE title LIKE 'Inception'   -- найдёт только точное совпадение
WHERE title LIKE '%Inception%' -- найдёт где встречается
```

---

## ✅ Главные правила

✅ `SELECT` и `FROM` — обязательны в каждом запросе  
✅ Порядок выполнения: `FROM → WHERE → SELECT → DISTINCT → ORDER BY → LIMIT`  
✅ Псевдоним из `SELECT AS` — нельзя в `WHERE`, можно в `ORDER BY`  
✅ `NULL` проверяй через `IS NULL` / `IS NOT NULL`, не через `=`  
✅ `IN` — короче нескольких `OR`, читабельнее  
✅ `AND` приоритетнее `OR` — используй скобки при сомнении  
✅ `LIKE` не учитывает регистр — добавь `BINARY` если важно  
✅ `%` — любая последовательность, `_` — один символ  
✅ `CONCAT` + `NULL` = `NULL`, `CONCAT_WS` игнорирует `NULL`  
✅ `DISTINCT` по нескольким столбцам — уникальность по комбинации  

---

## 🔗 Связанные темы

- [[02 — ⚖️ Операторы и условия]]
- [[03 — 📊 SELECT - основы]]
- [[01 — 🔤 Текстовые функции]]
- [[06 — 🧮 GROUP BY и HAVING]]

---

#sql/основы #select #where #like
