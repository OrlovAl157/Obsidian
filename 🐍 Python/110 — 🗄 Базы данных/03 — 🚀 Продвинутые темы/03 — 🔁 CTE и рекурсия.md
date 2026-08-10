---
tags: [sql, cte, recursive, with]
difficulty: intermediate
---

# 🔁 CTE и рекурсия — памятка

> CTE (Common Table Expression) — временная именованная таблица определённая внутри запроса через `WITH`. Делает сложные запросы читаемыми, позволяет ссылаться на себя (рекурсия) и переиспользовать подзапросы.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Простое CTE|Простое CTE]]
- [[#🔵 Несколько CTE|Несколько CTE]]
- [[#🔴 CTE внутри CTE|CTE внутри CTE]]
- [[#🟡 CTE vs подзапрос|CTE vs подзапрос]]
- [[#🟣 Рекурсивное CTE|Рекурсивное CTE]]
- [[#⚙️ Настройка глубины рекурсии|Глубина рекурсии]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `WITH name AS (SELECT ...)` | простое CTE |
| `WITH a AS (...), b AS (...)` | несколько CTE |
| `WITH b AS (SELECT ... FROM a)` | CTE использует другое CTE |
| `WITH RECURSIVE name AS (... UNION ALL ...)` | рекурсивное CTE |
| `SET @@cte_max_recursion_depth := N` | изменить глубину рекурсии |

---

## 📊 Общая схема

```
WITH cte_name (col1, col2) AS (
    SELECT ...
    FROM ...
    WHERE ...
)
SELECT * FROM cte_name;

CTE живёт только внутри одного запроса.
После ; — исчезает.

Рекурсивное CTE:
WITH RECURSIVE cte AS (
    SELECT ...          ← якорь (начальное условие)
    UNION ALL
    SELECT ...          ← рекурсивный шаг
    FROM cte            ← ссылается на себя
    WHERE ...           ← условие выхода
)
SELECT * FROM cte;
```

---

## 🟢 Простое CTE

```sql
-- Базовый синтаксис
WITH FirstBooks AS (
    SELECT title, release_year
    FROM Books
    LIMIT 3
)
SELECT * FROM FirstBooks;

-- Результат:
-- title                   | release_year
-- ------------------------+-------------
-- The Shining             | 1977
-- Fight Club              | 1996
-- The Catcher in the Rye  | 1951

-- С именованными столбцами
WITH FirstBooks (bookname, year) AS (
    SELECT title, release_year
    FROM Books
    LIMIT 3
)
SELECT bookname, year FROM FirstBooks;

-- CTE с фильтрацией после
WITH ExpensiveBooks AS (
    SELECT title, author_id, price
    FROM Books
    WHERE price > 10
)
SELECT b.title, a.author, b.price
FROM ExpensiveBooks b
JOIN Authors a ON b.author_id = a.id
ORDER BY b.price DESC;
```

---

## 🔵 Несколько CTE

```sql
-- Несколько CTE через запятую
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books
    INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' AND Authors.surname = 'King'
),
JeromeSalingerBooks AS (
    SELECT title, release_year
    FROM Books
    INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Jerome' AND Authors.surname = 'Salinger'
)
SELECT * FROM StephenKingBooks
UNION
SELECT * FROM JeromeSalingerBooks;

-- Результат:
-- The Shining            | 1977
-- The Green Mile         | 1996
-- The Catcher in the Rye | 1951
-- Franny and Zooey       | 1961
```

---

## 🔴 CTE внутри CTE

Последующие CTE могут ссылаться на предыдущие:

```sql
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books
    INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' AND Authors.surname = 'King'
),
-- Второй CTE использует первый
StephenKingBooks1996 AS (
    SELECT title, release_year
    FROM StephenKingBooks          -- ← ссылка на первый CTE
    WHERE release_year = 1996
)
SELECT * FROM StephenKingBooks1996;

-- Результат:
-- The Green Mile | 1996
```

---

## 🟡 CTE vs подзапрос

```sql
-- Подзапрос — трудно читать
SELECT b.title, a.author
FROM (
    SELECT id, title, author_id
    FROM Books
    WHERE price > (SELECT AVG(price) FROM Books)
) b
JOIN Authors a ON b.author_id = a.id;

-- CTE — читабельно
WITH ExpensiveBooks AS (
    SELECT id, title, author_id
    FROM Books
    WHERE price > (SELECT AVG(price) FROM Books)
)
SELECT b.title, a.author
FROM ExpensiveBooks b
JOIN Authors a ON b.author_id = a.id;
```

| Критерий | Подзапрос | CTE |
|---|---|---|
| Читаемость | ❌ вложенность | ✅ линейно |
| Переиспользование | ❌ нельзя | ✅ да (в одном запросе) |
| Рекурсия | ❌ нет | ✅ да |
| Производительность | одинаково | одинаково |

---

## 🟣 Рекурсивное CTE

**Синтаксис:**

```sql
WITH RECURSIVE cte_name AS (
    -- Якорь: начальные данные (выполняется один раз)
    SELECT ...

    UNION ALL  -- или UNION DISTINCT

    -- Рекурсивный шаг (выполняется пока есть строки)
    SELECT ...
    FROM cte_name          -- ссылка на себя
    WHERE условие_выхода   -- без него — бесконечный цикл!
)
SELECT * FROM cte_name;
```

**Пример 1 — числа от 1 до 10:**

```sql
WITH RECURSIVE NaturalNumbers AS (
    SELECT 1 AS number          -- якорь: начинаем с 1
    UNION ALL
    SELECT number + 1           -- добавляем 1 каждый шаг
    FROM NaturalNumbers
    WHERE number < 10           -- условие выхода
)
SELECT * FROM NaturalNumbers;

-- Результат: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10
```

**Пример 2 — числа Фибоначчи:**

```sql
WITH RECURSIVE Fibonacci AS (
    SELECT 1 AS fib_prev,
           1 AS fib_cur
    UNION ALL
    SELECT fib_cur,
           fib_cur + fib_prev
    FROM Fibonacci
    LIMIT 10                    -- ограничение через LIMIT
)
SELECT fib_prev AS number FROM Fibonacci;

-- Результат: 1, 1, 2, 3, 5, 8, 13, 21, 34, 55
```

**Пример 3 — иерархия сотрудников (дерево):**

```sql
-- Таблица: id, name, manager_id (NULL у корня)
WITH RECURSIVE EmployeeHierarchy AS (
    -- Якорь: руководители верхнего уровня
    SELECT id, name, manager_id, 1 AS level
    FROM Employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Рекурсия: подчинённые каждого уровня
    SELECT e.id, e.name, e.manager_id, h.level + 1
    FROM EmployeeHierarchy h
    INNER JOIN Employees e ON h.id = e.manager_id
)
SELECT id, name, level FROM EmployeeHierarchy
ORDER BY level, id;

-- Результат:
-- id | name     | level
-- ---+----------+-------
--  1 | Matthew  | 1      ← директор
--  2 | Caroline | 2      ← его подчинённые
--  3 | Tom      | 2
--  4 | Sam      | 3      ← подчинённые Caroline и Tom
--  5 | Ann      | 3
```

**Пример 4 — генерация дат:**

```sql
WITH RECURSIVE DateRange AS (
    SELECT '2025-01-01' AS dt
    UNION ALL
    SELECT DATE_ADD(dt, INTERVAL 1 DAY)
    FROM DateRange
    WHERE dt < '2025-01-31'
)
SELECT dt FROM DateRange;
-- Генерирует все даты января 2025
```

---

## ⚙️ Настройка глубины рекурсии

По умолчанию максимальная глубина рекурсии = 1000 шагов:

```sql
-- Просмотр текущего значения
SELECT @@cte_max_recursion_depth;

-- Изменить для текущей сессии
SET @@cte_max_recursion_depth := 10000;

-- При превышении глубины — ошибка:
-- ERROR 3636: Recursive query aborted after 1001 iterations
```

---

## ⚡ Быстрые примеры

```sql
-- Простое CTE
WITH TopBooks AS (SELECT * FROM Books WHERE price > 10)
SELECT * FROM TopBooks ORDER BY price DESC;

-- Два CTE
WITH a AS (SELECT ...), b AS (SELECT ... FROM a)
SELECT * FROM b;

-- Числа 1-100
WITH RECURSIVE n AS (
    SELECT 1 AS num
    UNION ALL
    SELECT num + 1 FROM n WHERE num < 100
)
SELECT * FROM n;

-- Дерево категорий
WITH RECURSIVE tree AS (
    SELECT id, name, parent_id, 1 AS depth
    FROM Categories WHERE parent_id IS NULL
    UNION ALL
    SELECT c.id, c.name, c.parent_id, t.depth + 1
    FROM Categories c JOIN tree t ON c.parent_id = t.id
)
SELECT * FROM tree;

-- Топ сотрудников с CTE + оконная функция
WITH ranked AS (
    SELECT *, RANK() OVER (PARTITION BY dept ORDER BY salary DESC) AS rnk
    FROM Employees
)
SELECT * FROM ranked WHERE rnk = 1;
```

---

## 💡 Практические замечания

- CTE живёт только в рамках одного запроса — после `;` исчезает
- Несколько CTE через запятую — последующие могут использовать предыдущие
- Рекурсивное CTE — обязательно условие выхода, иначе бесконечный цикл
- `UNION ALL` быстрее `UNION DISTINCT` — используй если дубликаты не важны
- CTE удобно комбинировать с оконными функциями — сначала считаем в CTE, потом фильтруем

---

## ⚠️ Частые ошибки

**❌ Рекурсия без условия выхода:**
```sql
WITH RECURSIVE bad AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM bad   -- ❌ нет WHERE — бесконечный цикл!
)
SELECT * FROM bad;
-- ERROR 3636: Recursive query aborted after 1001 iterations
```

**❌ Ссылка на CTE вне запроса:**
```sql
WITH temp AS (SELECT * FROM Books WHERE price > 10)
SELECT * FROM temp;

SELECT * FROM temp;  -- ❌ temp уже не существует!
```

**❌ Изменение данных через CTE:**
```sql
-- CTE только для чтения — нельзя UPDATE/DELETE через CTE напрямую
-- Используй INSERT ... WITH или субзапрос
```

---

## ✅ Главные правила

✅ `WITH name AS (SELECT ...)` — определить CTE перед основным запросом  
✅ Несколько CTE — через запятую, последующие видят предыдущие  
✅ `WITH RECURSIVE` — обязательно условие выхода в рекурсивной части  
✅ `UNION ALL` — быстрее чем `UNION DISTINCT` для рекурсии  
✅ CTE живёт только в рамках одного запроса  
✅ Рекурсия по умолчанию ограничена 1000 шагами — меняй через `@@cte_max_recursion_depth`  
✅ CTE + оконные функции — мощная комбинация для сложной аналитики  

---

## 🔗 Связанные темы

- [[02 — 🪟 Оконные функции]]
- [[07 — 🔎 Подзапросы]]

---

#sql/cte #recursive #with
