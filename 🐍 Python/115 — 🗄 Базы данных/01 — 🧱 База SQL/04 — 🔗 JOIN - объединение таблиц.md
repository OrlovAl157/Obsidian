---
tags: [sql, join, соединение-таблиц, объединение]
---

# 🔗 SQL JOIN — объединение таблиц

> Краткая шпаргалка по JOIN: все типы объединений таблиц, когда использовать, примеры с диаграммами и частые ошибки.

## Содержание

- [[#Справка по типам JOIN|Справка по типам JOIN]]
- [[#📊 Диаграммы JOIN|Диаграммы JOIN]]
- [[#🟢 INNER JOIN|INNER JOIN]]
- [[#🔵 LEFT JOIN|LEFT JOIN]]
- [[#🔴 RIGHT JOIN|RIGHT JOIN]]
- [[#⚪ FULL OUTER JOIN|FULL OUTER JOIN]]
- [[#❌ CROSS JOIN|CROSS JOIN]]
- [[#🔀 Несколько JOIN в одном запросе|Несколько JOIN в одном запросе]]
- [[#📌 Условия JOIN ON|Условия JOIN (ON)]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]

## Справка по типам JOIN

| Тип | Что делает | Синтаксис |
| --- | --- | --- |
| `INNER JOIN` | Возвращает только совпадающие строки | `INNER JOIN table2 ON condition` |
| `LEFT JOIN` | Все строки из левой таблицы + совпадения | `LEFT JOIN table2 ON condition` |
| `RIGHT JOIN` | Все строки из правой таблицы + совпадения | `RIGHT JOIN table2 ON condition` |
| `FULL OUTER JOIN` | Все строки из обеих таблиц | `FULL OUTER JOIN table2 ON condition` |
| `CROSS JOIN` | Декартово произведение (все комбинации) | `CROSS JOIN table2` |

---

## 📊 Диаграммы JOIN

Если у тебя есть две таблицы:

```
USERS:               ORDERS:
id | name           id | user_id | amount
-----------         ----------------
1  | Alice          1  | 1       | 100
2  | Bob            2  | 1       | 200
3  | Charlie        3  | 2       | 150
```

**INNER JOIN** — только совпадающие:
```
Alice (id=1) ✓ есть заказы
Bob (id=2)   ✓ есть заказ
Charlie (id=3) ✗ нет заказов → исключен
```

**LEFT JOIN** — все из левой таблицы:
```
Alice (id=1)   ✓ есть заказы
Bob (id=2)     ✓ есть заказ
Charlie (id=3) ✓ нет заказов → NULL
```

---




## 🟢 INNER JOIN

**Назначение:** выбрать только строки, где есть совпадение в обеих таблицах.

| Конструкция | Что делает |
| --- | --- |
| `INNER JOIN table2 ON condition` | Возвращает только совпадающие строки |
| `JOIN table2 ON condition` | INNER — ключевое слово по умолчанию |

**Примеры:**

```sql
SELECT 
    u.name,
    o.amount
FROM Users u
INNER JOIN Orders o ON u.id = o.user_id;

-- Результат: только пользователи с заказами
-- name  | amount
-- ------+--------
-- Alice | 100
-- Alice | 200
-- Bob   | 150
```

**С реальными таблицами:**

```sql
SELECT 
    u.id,
    u.name,
    u.email,
    o.order_id,
    o.total_price
FROM Users u
JOIN Orders o ON u.id = o.user_id
ORDER BY u.name;

-- Результат:
-- id | name  | email           | order_id | total_price
-- ---+-------+-----------------+----------+-------------
-- 1  | Alice | alice@example.com | 101   | 500.00
-- 1  | Alice | alice@example.com | 102   | 300.00
-- 2  | Bob   | bob@example.com   | 103   | 150.00
```

**Важное:** 
- INNER JOIN возвращает МЕНЬШЕ всего строк (только пересечение)
- Самый быстрый JOIN
- Используется когда нужны ТОЛЬКО связанные данные

---




## 🔵 LEFT JOIN

**Назначение:** выбрать все строки из левой таблицы + совпадения из правой. Если совпадения нет — NULL.

| Конструкция | Что делает |
| --- | --- |
| `LEFT JOIN table2 ON condition` | Все из левой, NULL если нет совпадения |
| `LEFT OUTER JOIN table2 ON condition` | OUTER — опционально, одно и то же |

**Примеры:**

```sql
SELECT 
    u.name,
    o.amount
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id;

-- Результат: ВСЕ пользователи, даже без заказов
-- name    | amount
-- --------+--------
-- Alice   | 100
-- Alice   | 200
-- Bob     | 150
-- Charlie | NULL ← нет заказов!
```

**С реальными таблицами:**

```sql
SELECT 
    u.name,
    COUNT(o.order_id) AS order_count
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id
GROUP BY u.id, u.name;

-- Результат:
-- name    | order_count
-- --------+-------------
-- Alice   | 2
-- Bob     | 1
-- Charlie | 0 ← LEFT JOIN позволяет видеть нулевые значения!
```

**Важное:** 
- LEFT JOIN возвращает ВСЕ строки из левой таблицы
- Правая таблица заполняется NULL где нет совпадений
- Используется для поиска "чего не хватает" (напр. пользователи без заказов)

---




## 🔴 RIGHT JOIN

**Назначение:** противоположность LEFT JOIN — все из правой таблицы + совпадения из левой.

| Конструкция | Что делает |
| --- | --- |
| `RIGHT JOIN table2 ON condition` | Все из правой, NULL если нет совпадения |

**Примеры:**

```sql
SELECT 
    u.name,
    o.amount
FROM Users u
RIGHT JOIN Orders o ON u.id = o.user_id;

-- Результат: ВСЕ заказы, даже если пользователь удалён
-- name    | amount
-- --------+--------
-- Alice   | 100
-- Alice   | 200
-- Bob     | 150
-- NULL    | 999 ← заказ от удаленного пользователя
```

**Важное:** 
- RIGHT JOIN редко используется (можно переписать как LEFT с переменой таблиц)
- Проще читать LEFT JOIN, чем RIGHT JOIN
- Используется когда правая таблица — "основной источник"

---




## ⚪ FULL OUTER JOIN

**Назначение:** объединить обе таблицы полностью — все строки из обеих, с NULL где нет совпадений.

| Конструкция | Что делает |
| --- | --- |
| `FULL OUTER JOIN table2 ON condition` | Все из обеих таблиц, NULL где нет совпадения |

**Важное:** 
- MySQL **НЕ ПОДДЕРЖИВАЕТ** FULL OUTER JOIN!
- Нужно эмулировать через `UNION` (LEFT JOIN + RIGHT JOIN)

**Эмуляция FULL OUTER JOIN в MySQL:**

```sql
SELECT 
    u.name,
    o.amount
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id

UNION

SELECT 
    u.name,
    o.amount
FROM Users u
RIGHT JOIN Orders o ON u.id = o.user_id;

-- Результат: ВСЕ пользователи И ВСЕ заказы
-- name    | amount
-- --------+--------
-- Alice   | 100
-- Alice   | 200
-- Bob     | 150
-- Charlie | NULL
-- NULL    | 999 ← заказ без пользователя
```

---




## ❌ CROSS JOIN

**Назначение:** Декартово произведение — все возможные комбинации строк из обеих таблиц.

| Конструкция | Что делает |
| --- | --- |
| `CROSS JOIN table2` | Каждая строка левой × каждая строка правой |

**Примеры:**

```sql
SELECT 
    u.name,
    p.product_name
FROM Users u
CROSS JOIN Products p;

-- Если Users: 3 строки, Products: 2 строки
-- Результат: 3 × 2 = 6 строк

-- name    | product_name
-- --------+---------------
-- Alice   | Laptop
-- Alice   | Mouse
-- Bob     | Laptop
-- Bob     | Mouse
-- Charlie | Laptop
-- Charlie | Mouse
```

**Важное:** 
- CROSS JOIN **НЕ имеет ON условия**
- Результат ОГРОМЕН: если в левой 1000 строк, в правой 1000 — результат 1,000,000 строк!
- Используется редко: для создания всех комбинаций, калькулятора дат и т.д.

---




## 🔀 Несколько JOIN в одном запросе

**Назначение:** объединить несколько таблиц в одном запросе.

**Примеры:**

```sql
SELECT 
    u.name,
    o.order_id,
    p.product_name,
    oi.quantity
FROM Users u
JOIN Orders o ON u.id = o.user_id
JOIN OrderItems oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.id
ORDER BY u.name;

-- Результат:
-- name  | order_id | product_name | quantity
-- ------+----------+--------------+---------
-- Alice | 101      | Laptop       | 1
-- Alice | 102      | Mouse        | 2
-- Bob   | 103      | Keyboard     | 1
```

**С смешиванием типов JOIN:**

```sql
SELECT 
    u.name,
    o.order_id,
    p.product_name
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id      ← LEFT
JOIN Products p ON o.product_id = p.id      ← INNER
ORDER BY u.name;

-- LEFT сохраняет всех пользователей
-- INNER отфильтровывает продукты только связанные
```

**Важное:** 
- Порядок JOIN-ов часто не важен для логики, но влияет на производительность
- Можно смешивать разные типы JOIN
- Для больших запросов используй алиасы таблиц (u, o, p и т.д.)

---




## 📌 Условия JOIN (ON)

**Назначение:** описать как связаны две таблицы — какие столбцы сравнивать.

| Конструкция | Что делает | Пример |
| --- | --- | --- |
| `ON table1.id = table2.id` | Основное условие | `ON u.id = o.user_id` |
| `ON table1.id = table2.id AND table1.status = 'active'` | Несколько условий | `ON u.id = o.user_id AND u.status = 'active'` |
| `ON table1.id = table2.id OR table1.alt_id = table2.id` | Альтернативные ключи | Редко, но возможно |

**Примеры:**

```sql
SELECT *
FROM Users u
JOIN Orders o ON u.id = o.user_id
WHERE u.status = 'active';
-- ON определяет как связать таблицы
-- WHERE фильтрует результат
```

```sql
SELECT *
FROM Employees e
JOIN Departments d ON e.dept_id = d.id AND e.is_active = 1
-- ON может содержать логику
```

**Важное:** 
- `ON` и `WHERE` похожи, но имеют разное назначение
- `ON` выполняется ДО WHERE (влияет на какие строки соединяются)
- `WHERE` выполняется ПОСЛЕ JOIN (фильтрует результат)

---




## ⚡ Быстрые примеры

```sql
-- INNER JOIN: пользователи с заказами
SELECT u.name, COUNT(o.id) as order_count
FROM Users u
JOIN Orders o ON u.id = o.user_id
GROUP BY u.id;

-- LEFT JOIN: все пользователи и их заказы
SELECT u.name, o.order_id
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id
ORDER BY u.name;

-- Несколько таблиц
SELECT u.name, o.order_id, p.product_name
FROM Users u
JOIN Orders o ON u.id = o.user_id
JOIN OrderItems oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.id;

-- LEFT + INNER комбо
SELECT u.name, o.order_id, p.product_name
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id
JOIN Products p ON o.product_id = p.id;
```

---

## 💡 Практические замечания

- **INNER JOIN** — когда нужны только связанные данные (самый быстрый)
- **LEFT JOIN** — когда нужны все из левой таблицы (напр. все пользователи, включая без заказов)
- **Избегай RIGHT JOIN** — лучше переписать как LEFT с изменением порядка таблиц
- **Алиасы таблиц** — используй всегда для читаемости: `FROM Users u`
- **ON vs WHERE** — ON связывает таблицы, WHERE фильтрует результат
- **Производительность** — JOIN часто быстрее подзапросов
- **NULL в JOIN** — NULL = NULL вернёт FALSE, используй `IS NULL`

---

## ⚠️ Частые ошибки

**❌ ОШИБКА:**
```sql
SELECT *
FROM Users u
JOIN Orders o WHERE u.id = o.user_id;
```
❌ Условие в WHERE вместо ON!

**✅ ВЕРНО:**
```sql
SELECT *
FROM Users u
JOIN Orders o ON u.id = o.user_id;
```

---

**❌ ОШИБКА:**
```sql
SELECT *
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id
WHERE o.order_id IS NOT NULL;
```
❌ WHERE превращает LEFT JOIN в INNER JOIN!

**✅ ВЕРНО (если нужен LEFT):**
```sql
SELECT *
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id;
```

---

**❌ ОШИБКА:**
```sql
SELECT *
FROM Users u
JOIN Orders o ON u.id = o.user_id AND o.amount > 100;
```
❌ Логика может быть неверной! WHERE читается проще:

**✅ ВЕРНО:**
```sql
SELECT *
FROM Users u
JOIN Orders o ON u.id = o.user_id
WHERE o.amount > 100;
```

---

**❌ ОШИБКА (MySQL):**
```sql
SELECT *
FROM Users u
FULL OUTER JOIN Orders o ON u.id = o.user_id;
```
❌ MySQL не поддерживает FULL OUTER JOIN!

**✅ ВЕРНО:**
```sql
SELECT * FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id
UNION
SELECT * FROM Users u
RIGHT JOIN Orders o ON u.id = o.user_id;
```
