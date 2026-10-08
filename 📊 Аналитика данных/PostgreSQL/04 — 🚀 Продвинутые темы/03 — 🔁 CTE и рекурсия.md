---
tags: [postgresql, sql, cte]
difficulty: intermediate
---

# 🔁 CTE и рекурсия — памятка

> CTE (Common Table Expression) — временный именованный результат запроса, доступный в рамках одного SQL-выражения. В PostgreSQL CTE можно использовать не только в SELECT, но и в INSERT/UPDATE/DELETE. Рекурсивные CTE позволяют работать с иерархическими данными.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Базовый CTE|Базовый CTE]]
- [[#🔵 Несколько CTE|Несколько CTE]]
- [[#🔴 CTE с DML|CTE с DML]]
- [[#🟣 Рекурсивный CTE|Рекурсивный CTE]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `WITH name AS (SELECT ...)` | обычный CTE |
| `WITH RECURSIVE name AS (...)` | рекурсивный CTE |
| CTE в INSERT/UPDATE/DELETE | изменяющий CTE |

---

## 🟢 Базовый CTE

```sql
-- Простой CTE вместо подзапроса
WITH expensive_books AS (
    SELECT * FROM Books WHERE price > 15
)
SELECT title, price FROM expensive_books
ORDER BY price DESC;

-- CTE делает сложные запросы читаемее
WITH
    avg_price AS (SELECT AVG(price) AS avg FROM Books),
    expensive AS (SELECT * FROM Books WHERE price > (SELECT avg FROM avg_price))
SELECT title, price FROM expensive;
```

---

## 🔵 Несколько CTE

```sql
WITH
    top_authors AS (
        SELECT author_id, COUNT(*) AS book_count
        FROM Books
        GROUP BY author_id
        ORDER BY book_count DESC
        LIMIT 5
    ),
    author_details AS (
        SELECT a.id, a.name, ta.book_count
        FROM Authors a
        JOIN top_authors ta ON a.id = ta.author_id
    )
SELECT * FROM author_details;
```

---

## 🔴 CTE с DML

В PostgreSQL CTE можно использовать с INSERT, UPDATE, DELETE:

```sql
-- Переместить строки: удалить из одной таблицы, вставить в другую
WITH deleted AS (
    DELETE FROM Books
    WHERE price < 1
    RETURNING *
)
INSERT INTO Archive SELECT * FROM deleted;

-- UPDATE с RETURNING в CTE
WITH updated AS (
    UPDATE Books
    SET price = price * 1.1
    WHERE author_id = 1
    RETURNING id, title, price
)
SELECT * FROM updated;
```

---

## 🟣 Рекурсивный CTE

```sql
-- Иерархия сотрудников (менеджер → подчинённые)
WITH RECURSIVE employee_tree AS (
    -- Базовый случай: директор (без менеджера)
    SELECT id, name, manager_id, 0 AS level
    FROM Employees
    WHERE manager_id IS NULL

    UNION ALL

    -- Рекурсивный шаг: подчинённые
    SELECT e.id, e.name, e.manager_id, et.level + 1
    FROM Employees e
    JOIN employee_tree et ON e.manager_id = et.id
)
SELECT level, name FROM employee_tree
ORDER BY level, name;
```

---

## ✅ Главные правила

✅ CTE улучшают читаемость сложных запросов  
✅ CTE в PostgreSQL можно использовать с INSERT/UPDATE/DELETE  
✅ `WITH RECURSIVE` — для иерархических данных (деревья, графы)  
✅ Рекурсия: базовый случай `UNION ALL` рекурсивный шаг  

---

## 🔗 Связанные темы

- [[06 — 🔎 Подзапросы]]
- [[02 — 🪟 Оконные функции]]

---

#postgresql/sql #cte
