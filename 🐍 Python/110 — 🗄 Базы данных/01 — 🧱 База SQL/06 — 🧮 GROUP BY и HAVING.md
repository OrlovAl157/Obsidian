---
tags: [sql, group-by, having, группировка, агрегаты]
---

# 🧮 SQL GROUP BY и HAVING — группировка данных

> Краткая шпаргалка по GROUP BY и HAVING: группировать строки, считать статистику по группам, фильтровать результаты агрегации.

## Содержание

- [[#Справка по конструкциям|Справка по конструкциям]]
- [[#🔢 GROUP BY основы|GROUP BY основы]]
- [[#🧠 Что происходит внутри группы|Что происходит внутри группы]]
- [[#📊 Агрегатные функции с GROUP BY|Агрегатные функции с GROUP BY]]
- [[#🎯 Несколько столбцов в GROUP BY|Несколько столбцов в GROUP BY]]
- [[#🔍 HAVING фильтрация групп|HAVING фильтрация групп]]
- [[#❓ GROUP BY vs WHERE|GROUP BY vs WHERE]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]

## Справка по конструкциям

| Конструкция | Что делает | Синтаксис |
| --- | --- | --- |
| `GROUP BY col` | Группирует строки по столбцу | `GROUP BY column` |
| `GROUP BY col1, col2` | Группирует по нескольким столбцам | `GROUP BY col1, col2` |
| `HAVING condition` | Фильтрует группы (работает с агрегатами) | `HAVING COUNT(*) > 5` |
| `SUM()`, `COUNT()`, `AVG()` | Агрегатные функции для группы | `SUM(amount)` |
| `MIN()`, `MAX()` | Минимум и максимум в группе | `MIN(price)` |

---




## 🔢 GROUP BY основы

**Назначение:** сгруппировать строки по одному столбцу и выполнить функцию для каждой группы.

| Конструкция | Что делает |
| --- | --- |
| `GROUP BY col` | Создаёт одну группу для каждого уникального значения |

**Примеры:**

```sql
SELECT city, COUNT(*) as user_count
FROM Users
GROUP BY city;

-- Результат: количество пользователей по городам
-- city      | user_count
-- -----------+------------
-- New York  | 3
-- London    | 2
-- Paris     | 1
-- Tokyo     | 4
```

**С данными:**

```sql
-- Исходная таблица Users:
-- id | name    | city
-- ---+---------+----------
-- 1  | Alice   | New York
-- 2  | Bob     | London
-- 3  | Charlie | Paris
-- 4  | Diana   | New York
-- 5  | Eve     | Tokyo
-- 6  | Frank   | Tokyo
-- 7  | Grace   | New York
-- 8  | Hank    | London
-- 9  | Ivy     | Tokyo
-- 10 | Jack    | Tokyo

SELECT city, COUNT(*) as user_count
FROM Users
GROUP BY city;

-- New York: 3 (Alice, Diana, Grace)
-- London: 2 (Bob, Hank)
-- Paris: 1 (Charlie)
-- Tokyo: 4 (Eve, Frank, Ivy, Jack)
```

**Важное:** 
- После GROUP BY в SELECT можно выбирать только:
  - Столбец, по которому группируешь
  - Агрегатные функции (COUNT, SUM, AVG и т.д.)
- Все остальные столбцы нельзя выбирать (они неоднозначны)

---




## 🧠 Что происходит внутри группы

**Концепция:** после `GROUP BY` СУБД не "обрезает" строки до одной-двух колонок. Каждая группа — это полный набор **целых** исходных строк, просто разложенных по кучкам (по значению столбца группировки). Любая агрегатная функция внутри себя видит **все** колонки каждой строки своей группы, а не только то, что указано в `SELECT`.

### Пример 1 — выражение внутри простого агрегата

```sql
-- Таблица Employees:
-- emp_id | event_day  | in_time | out_time
-- 1      | 2024-01-01 | 9       | 12
-- 1      | 2024-01-01 | 13      | 17
-- 1      | 2024-01-02 | 10      | 18
-- 2      | 2024-01-01 | 9       | 16

SELECT emp_id, event_day, SUM(out_time - in_time) AS total_time
FROM Employees
GROUP BY emp_id, event_day;
```

Шаги:
1. `GROUP BY emp_id, event_day` склеивает строки по комбинации обоих столбцов → 3 группы (у emp_id=1 за 2024-01-01 было 2 строки).
2. Для **каждой строки группы** сначала вычисляется `out_time - in_time`.
3. `SUM` складывает полученные разности внутри группы.

```
Группа (1, 2024-01-01): (12-9) + (17-13) = 3 + 4 = 7
Группа (1, 2024-01-02): (18-10) = 8
Группа (2, 2024-01-01): (16-9) = 7
```

Результат:

| emp_id | event_day | total_time |
| --- | --- | --- |
| 1 | 2024-01-01 | 7 |
| 1 | 2024-01-02 | 8 |
| 2 | 2024-01-01 | 7 |

### Пример 2 — агрегат с условием (проверка "встречается ли значение в группе")

```sql
SELECT user_id
FROM Views
WHERE user_id NOT IN (SELECT user_id FROM Views WHERE video_id = 2)
GROUP BY user_id
HAVING SUM(video_id = 1) > 0 AND SUM(video_id = 3) > 0;
```

В MySQL условие (`video_id = 1`) внутри агрегата трактуется как число: `TRUE → 1`, `FALSE → 0`. Поэтому `SUM(video_id = 1)` — это "сколько строк группы имеют `video_id = 1`". Так можно проверить **наличие конкретного значения** в группе, а не просто количество строк (`COUNT(*) > 1` для этого не подходит — не отличает "видел 1 и 3" от "видел 1 дважды").

⚠️ В PostgreSQL так писать нельзя — булево значение не приводится к `integer` неявно, нужен `CASE WHEN ... THEN 1 ELSE 0 END` или явный `::int`.

### Пример 3 — вложенная конструкция внутри агрегата

```sql
-- Таблица Employees:
-- id | department_id | primary_flag
-- 1  | 10            | yes
-- 1  | 20            | no
-- 2  | 30            | yes
-- 3  | 40            | no
-- 3  | 50            | no

SELECT id, IF(COUNT(department_id) > 1,
              MAX(IF(primary_flag = 'yes', department_id, 0)),
              MAX(department_id))
FROM Employees
GROUP BY id;
```

Шаги для группы `id = 1` (строки `(10, yes)`, `(20, no)`):
1. `COUNT(department_id) = 2 > 1` → условие `TRUE` → выполняется ветка А.
2. Внутренний `IF(primary_flag = 'yes', department_id, 0)` вычисляется **для каждой строки отдельно**, до всякого `MAX`:
   - строка `(10, yes)` → `TRUE` → `10`
   - строка `(20, no)` → `FALSE` → `0`
3. Получается временный набор значений `[10, 0]`.
4. `MAX([10, 0]) = 10`.

Для группы `id = 3` (`(40, no)`, `(50, no)`): временный набор `[0, 0]` → `MAX = 0` (основного отдела нет).
Для группы `id = 2` (одна строка `(30, yes)`): `COUNT = 1`, не `> 1` → ветка Б, просто `MAX(department_id) = 30`.

**Главный вывод:** вложенная функция (`IF`) всегда выполняется **построчно, внутри каждой группы, раньше** внешней агрегатной функции. Агрегат (`MAX`, `SUM`) видит уже готовые промежуточные значения и сводит их в одно число.

---




## 📊 Агрегатные функции с GROUP BY

**Назначение:** вычислить статистику для каждой группы.

| Функция | Что делает | Пример |
| --- | --- | --- |
| `COUNT(*)` | Количество строк в группе | `COUNT(*)` |
| `SUM(col)` | Сумма значений в группе | `SUM(amount)` |
| `AVG(col)` | Среднее значение | `AVG(salary)` |
| `MIN(col)` | Минимальное значение | `MIN(price)` |
| `MAX(col)` | Максимальное значение | `MAX(price)` |

**Примеры:**

```sql
SELECT 
    department,
    COUNT(*) as employee_count,
    AVG(salary) as avg_salary,
    MIN(salary) as min_salary,
    MAX(salary) as max_salary
FROM Employees
GROUP BY department;

-- Результат: статистика по отделам
-- department | employee_count | avg_salary | min_salary | max_salary
-- -----------+----------------+------------+------------+------------
-- HR         | 5              | 45000      | 35000      | 60000
-- IT         | 8              | 75000      | 50000      | 120000
-- Sales      | 6              | 55000      | 40000      | 85000
```

```sql
SELECT 
    category,
    COUNT(*) as product_count,
    SUM(quantity) as total_quantity,
    AVG(price) as avg_price
FROM Products
GROUP BY category;

-- Результат:
-- category    | product_count | total_quantity | avg_price
-- ------------+---------------+----------------+-----------
-- Electronics | 12            | 450            | 599.99
-- Books       | 45            | 2300           | 24.99
-- Clothing    | 30            | 1200           | 49.99
```

**Важное:** 
- `COUNT(*)` считает все строки в группе (включая NULL)
- `COUNT(col)` считает только не-NULL значения
- `SUM()` игнорирует NULL (считает только не-NULL)

---




## 🎯 Несколько столбцов в GROUP BY

**Назначение:** группировать по нескольким критериям одновременно.

| Конструкция | Что делает |
| --- | --- |
| `GROUP BY col1, col2` | Создаёт отдельную группу для каждой комбинации значений |

**Примеры:**

```sql
SELECT 
    department,
    position,
    COUNT(*) as employee_count,
    AVG(salary) as avg_salary
FROM Employees
GROUP BY department, position;

-- Результат: статистика по отделам И должностям
-- department | position     | employee_count | avg_salary
-- -----------+--------------+----------------+------------
-- HR         | Manager      | 1              | 70000
-- HR         | Specialist   | 4              | 40000
-- IT         | Manager      | 2              | 95000
-- IT         | Developer    | 5              | 70000
-- IT         | Intern       | 1              | 40000
-- Sales      | Manager      | 1              | 80000
-- Sales      | Representative | 5            | 50000
```

```sql
SELECT 
    year,
    month,
    COUNT(*) as order_count,
    SUM(total_amount) as total_revenue
FROM Orders
GROUP BY year, month
ORDER BY year, month;

-- Результат: доход по месяцам и годам
-- year | month | order_count | total_revenue
-- -----+-------+-------------+---------------
-- 2022 | 1     | 15          | 5000.00
-- 2022 | 2     | 22          | 7200.00
-- 2022 | 3     | 18          | 6500.00
-- 2023 | 1     | 28          | 9200.00
-- 2023 | 2     | 35          | 11500.00
```

**Важное:** 
- Порядок столбцов в GROUP BY не влияет на результат логически
- Но может влиять на производительность
- Все столбцы в GROUP BY должны быть "один-к-одному" с строками исходной таблицы

---




## 🔍 HAVING фильтрация групп

**Назначение:** фильтровать группы по условию (работает с агрегатными функциями).

| Конструкция | Что делает | Синтаксис |
| --- | --- | --- |
| `HAVING condition` | Фильтрует группы после GROUP BY | `HAVING COUNT(*) > 5` |
| `HAVING aggregate function` | Условие с агрегатными функциями | `HAVING SUM(amount) > 1000` |

**Примеры:**

```sql
SELECT 
    city,
    COUNT(*) as user_count
FROM Users
GROUP BY city
HAVING COUNT(*) > 2;

-- Результат: только города с более чем 2 пользователями
-- city     | user_count
-- ---------+------------
-- New York | 3
-- Tokyo    | 4
```

```sql
SELECT 
    department,
    AVG(salary) as avg_salary
FROM Employees
GROUP BY department
HAVING AVG(salary) > 60000;

-- Результат: только отделы со средней зарплатой > 60000
-- department | avg_salary
-- -----------+------------
-- IT         | 75000
```

```sql
SELECT 
    category,
    COUNT(*) as product_count,
    SUM(quantity) as total_quantity
FROM Products
GROUP BY category
HAVING product_count > 10 AND total_quantity > 500;

-- Результат: категории с более чем 10 продуктами И итогом > 500
-- category    | product_count | total_quantity
-- ------------+---------------+----------------
-- Electronics | 12            | 450  ← НЕ попадет (450 < 500)
-- Books       | 45            | 2300 ← попадет
-- Clothing    | 30            | 1200 ← попадет
```

**Важное:** 
- HAVING работает ПОСЛЕ GROUP BY
- WHERE работает ДО GROUP BY
- В HAVING можно использовать псевдонимы (AS) но не все СУБД поддерживают

---




## ❓ GROUP BY vs WHERE

**Назначение:** понять разницу и когда использовать что.

| Конструкция | Когда выполняется | Что может делать | Пример |
| --- | --- | --- | --- |
| `WHERE` | До GROUP BY | Фильтрует отдельные строки | `WHERE salary > 50000` |
| `HAVING` | После GROUP BY | Фильтрует группы целиком | `HAVING COUNT(*) > 5` |

**Примеры разницы:**

```sql
-- WHERE: фильтруем отдельные строки ДО группировки
SELECT 
    department,
    COUNT(*) as employee_count
FROM Employees
WHERE salary > 50000        ← фильтруем отдельных сотрудников
GROUP BY department;

-- Результат: количество сотрудников с зарплатой > 50000 по отделам
-- department | employee_count
-- -----------+----------------
-- HR         | 1
-- IT         | 7
-- Sales      | 3
```

```sql
-- HAVING: фильтруем группы ПОСЛЕ группировки
SELECT 
    department,
    COUNT(*) as employee_count
FROM Employees
GROUP BY department
HAVING COUNT(*) > 5;        ← фильтруем группы целиком

-- Результат: отделы с более чем 5 сотрудниками
-- department | employee_count
-- -----------+----------------
-- IT         | 8
```

```sql
-- WHERE + HAVING вместе
SELECT 
    department,
    COUNT(*) as employee_count,
    AVG(salary) as avg_salary
FROM Employees
WHERE hire_date > '2020-01-01'  ← сначала отбираем новых сотрудников
GROUP BY department
HAVING COUNT(*) > 3;            ← потом отбираем отделы с > 3 новыми

-- Результат: отделы, где больше 3 новых (после 2020) сотрудников
```

**Важное:** 
- WHERE — для фильтрации СТРОК
- HAVING — для фильтрации ГРУПП
- Используй оба вместе для лучшего контроля

---




## ⚡ Быстрые примеры

```sql
-- Количество пользователей по городам
SELECT city, COUNT(*) as users
FROM Users
GROUP BY city;

-- Доход по продуктам (только > 1000)
SELECT product_id, SUM(quantity * price) as revenue
FROM OrderItems
GROUP BY product_id
HAVING revenue > 1000;

-- Средняя зарплата по отделам и должностям
SELECT department, position, AVG(salary)
FROM Employees
GROUP BY department, position;

-- Топ категорий по количеству товаров
SELECT category, COUNT(*) as product_count
FROM Products
GROUP BY category
ORDER BY product_count DESC
LIMIT 5;

-- Пользователи с 3+ заказами
SELECT user_id, COUNT(*) as order_count
FROM Orders
GROUP BY user_id
HAVING order_count >= 3;

-- Месячный доход (только активные заказы)
SELECT MONTH(order_date) as month, SUM(total)
FROM Orders
WHERE status = 'completed'
GROUP BY month;
```

---

## 🔀 Сортировка и порядок выполнения

Сгруппированные данные можно сортировать обычным `ORDER BY`.

**Примеры:**

```sql
SELECT 
    city,
    COUNT(*) as cnt
FROM Users
GROUP BY city
ORDER BY COUNT(*) DESC;
```

```sql
SELECT 
    city,
    COUNT(*) as cnt
FROM Users
GROUP BY city
ORDER BY cnt DESC
LIMIT 5;
```

**Порядок записи операторов в запросе:**

`SELECT -> FROM -> WHERE -> GROUP BY -> HAVING -> ORDER BY -> LIMIT`

**Порядок выполнения:**

`FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY -> LIMIT`

---

## 🧩 Полезные нюансы

- `NULL` в `GROUP BY` считается отдельной группой, и все `NULL`-значения попадают вместе
- в `GROUP BY`, `HAVING` и `ORDER BY` часто можно использовать псевдонимы, но поведение зависит от СУБД
- если псевдоним совпадает с именем столбца, некоторые СУБД могут выбрать именно столбец таблицы
- `GROUP BY` во многих случаях близок к `DISTINCT`

```sql
SELECT artist
FROM Songs
GROUP BY artist;

-- Почти то же самое:
SELECT DISTINCT artist
FROM Songs;
```

---

## 💡 Практические замечания

- **GROUP BY без агрегатов** — редко нужно, обычно есть COUNT() или другая агрегат
- **Производительность** — GROUP BY на большой таблице может быть медленным
- **NULL в GROUP BY** — NULL считается отдельной группой
- **HAVING vs WHERE** — WHERE быстрее (фильтрует до группировки)
- **Псевдонимы** — в HAVING лучше использовать саму функцию, не псевдоним
- **Индексы** — имеют смысл на столбцах в GROUP BY
- **Порядок выполнения** — WHERE → GROUP BY → HAVING → SELECT → ORDER BY

---

## ⚠️ Частые ошибки

**❌ ОШИБКА:**
```sql
SELECT 
    department,
    name,           ← какое имя выбрать из группы?
    COUNT(*) as cnt
FROM Employees
GROUP BY department;
```
❌ Нельзя выбирать неагрегированный столбец, который не в GROUP BY!

**✅ ВЕРНО:**
```sql
SELECT 
    department,
    COUNT(*) as cnt
FROM Employees
GROUP BY department;
```

---

**❌ ОШИБКА:**
```sql
SELECT 
    department,
    AVG(salary)
FROM Employees
WHERE AVG(salary) > 50000  ← WHERE не может использовать агрегаты!
GROUP BY department;
```
❌ WHERE выполняется до GROUP BY!

**✅ ВЕРНО:**
```sql
SELECT 
    department,
    AVG(salary) as avg_sal
FROM Employees
GROUP BY department
HAVING AVG(salary) > 50000;
```

---

**❌ ОШИБКА:**
```sql
SELECT 
    city,
    COUNT(*) as cnt
FROM Users
GROUP BY city
ORDER BY cnt DESC
LIMIT 5;

-- Может работать, но опасно!
-- COUNT(*) вычисляется ДО ORDER BY
```

**✅ ВЕРНО:**
```sql
SELECT 
    city,
    COUNT(*) as cnt
FROM Users
GROUP BY city
ORDER BY COUNT(*) DESC
LIMIT 5;

-- Или используй псевдоним, если БД поддерживает
SELECT 
    city,
    COUNT(*) as cnt
FROM Users
GROUP BY city
ORDER BY cnt DESC
LIMIT 5;
```

---

**❌ ОШИБКА:**
```sql
SELECT 
    category,
    COUNT(*)
FROM Products
GROUP BY category
HAVING category IN ('Electronics', 'Books');
```
❌ Это можно, но лучше использовать WHERE!

**✅ ВЕРНО:**
```sql
SELECT 
    category,
    COUNT(*)
FROM Products
WHERE category IN ('Electronics', 'Books')
GROUP BY category;
```
