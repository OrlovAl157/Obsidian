---
tags: [postgresql, dml, insert]
difficulty: beginner
---

# ➕ INSERT — памятка

> `INSERT` добавляет записи в таблицу. В PostgreSQL есть мощное расширение `ON CONFLICT` для upsert и `RETURNING` для получения данных после вставки.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Базовый синтаксис|Базовый синтаксис]]
- [[#🔵 INSERT SELECT|INSERT SELECT]]
- [[#🟣 RETURNING — вернуть данные|RETURNING]]
- [[#🔗 ON CONFLICT — upsert|ON CONFLICT]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `INSERT INTO t (cols) VALUES (...)` | добавить одну запись |
| `INSERT INTO t VALUES (...), (...)` | добавить несколько записей |
| `INSERT INTO t SELECT ...` | добавить из другой таблицы |
| `INSERT ... RETURNING col` | вернуть значения после вставки |
| `ON CONFLICT DO NOTHING` | пропустить при конфликте |
| `ON CONFLICT DO UPDATE` | обновить при конфликте (upsert) |

---

## 🟢 Базовый синтаксис

```sql
-- Одна запись
INSERT INTO Books (title, author, price)
VALUES ('It', 'Stephen King', 7.99);

-- Несколько записей
INSERT INTO Books (title, author, price)
VALUES
    ('It', 'Stephen King', 7.99),
    ('Fight Club', 'Palahniuk', 9.99),
    ('1984', 'Orwell', 5.99);
```

---

## 🔵 INSERT SELECT

```sql
-- Перенести данные из другой таблицы
INSERT INTO Archive (title, author, price)
SELECT title, author, price
FROM Books
WHERE price > 15;

-- INSERT SELECT с JOIN
INSERT INTO Report (title, author_name, price)
SELECT b.title, a.name, b.price
FROM Books b
JOIN Authors a ON b.author_id = a.id
WHERE a.country = 'UK';
```

---

## 🟣 RETURNING — вернуть данные

`RETURNING` — уникальная возможность PostgreSQL: получить данные вставленной строки сразу после `INSERT`, без отдельного `SELECT`:

```sql
-- Получить id вставленной записи
INSERT INTO Books (title, price)
VALUES ('1984', 5.99)
RETURNING id;

-- Получить всю строку
INSERT INTO Books (title, price)
VALUES ('1984', 5.99)
RETURNING *;

-- Использовать в CTE
WITH inserted AS (
    INSERT INTO Books (title, price)
    VALUES ('1984', 5.99)
    RETURNING id, title
)
SELECT * FROM inserted;
```

---

## 🔗 ON CONFLICT — upsert

`ON CONFLICT` — вставить или обновить при конфликте:

```sql
-- Пропустить если запись уже существует
INSERT INTO Books (id, title, price)
VALUES (1, '1984', 5.99)
ON CONFLICT DO NOTHING;

-- Обновить при конфликте
INSERT INTO Books (id, title, price)
VALUES (1, '1984', 5.99)
ON CONFLICT (id) DO UPDATE
SET title = EXCLUDED.title,
    price = EXCLUDED.price;
```

**`EXCLUDED`** — специальная таблица с данными, которые пытались вставить но конфликтовали.

---

## ⚠️ Частые ошибки

```sql
-- ❌ INSERT IGNORE — MySQL, не PostgreSQL
INSERT IGNORE INTO Books ...
-- ✅ PostgreSQL:
INSERT INTO Books ... ON CONFLICT DO NOTHING;

-- ❌ REPLACE — MySQL, не PostgreSQL
REPLACE INTO Books ...
-- ✅ PostgreSQL:
INSERT INTO Books ... ON CONFLICT (id) DO UPDATE SET ...;
```

---

## ✅ Главные правила

✅ `RETURNING` — получить данные после INSERT без лишнего SELECT  
✅ `ON CONFLICT DO NOTHING` — аналог INSERT IGNORE  
✅ `ON CONFLICT DO UPDATE` — upsert (вставить или обновить)  
✅ `EXCLUDED` — данные которые пытались вставить, но конфликтовали  
✅ Нет `INSERT IGNORE`, нет `REPLACE` — используй `ON CONFLICT`

---

## 🔗 Связанные темы

- [[01 — 🏗 CREATE TABLE]]
- [[03 — ✏️ UPDATE]]
- [[../06 — 🐘 PostgreSQL-специфика/01 — 🔄 RETURNING]]
- [[../06 — 🐘 PostgreSQL-специфика/02 — ⚔️ ON CONFLICT (Upsert)]]

---

#postgresql/dml #insert
