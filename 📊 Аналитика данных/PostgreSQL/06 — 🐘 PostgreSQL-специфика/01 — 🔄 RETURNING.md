---
tags: [postgresql, специфика, returning]
difficulty: intermediate
---

# 🔄 RETURNING — памятка

> `RETURNING` — уникальная возможность PostgreSQL: получить данные строк, затронутых операцией INSERT/UPDATE/DELETE, без отдельного SELECT. Работает с любой DML-операцией.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 RETURNING в INSERT|INSERT]]
- [[#🔵 RETURNING в UPDATE|UPDATE]]
- [[#🔴 RETURNING в DELETE|DELETE]]
- [[#🟣 RETURNING в CTE|В CTE]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `INSERT ... RETURNING id` | вернуть id вставленной строки |
| `INSERT ... RETURNING *` | вернуть всю вставленную строку |
| `UPDATE ... RETURNING *` | вернуть обновлённые строки |
| `DELETE ... RETURNING *` | вернуть удалённые строки |

---

## 🟢 RETURNING в INSERT

```sql
-- Получить id — самое частое использование
INSERT INTO Books (title, price)
VALUES ('1984', 5.99)
RETURNING id;

-- Получить всю строку (с вычисленными дефолтами)
INSERT INTO Books (title, price)
VALUES ('1984', 5.99)
RETURNING *;

-- Вставить несколько и получить все id
INSERT INTO Books (title, price)
VALUES ('1984', 5.99), ('Brave New World', 7.99)
RETURNING id, title;
```

---

## 🔵 RETURNING в UPDATE

```sql
-- Получить обновлённые строки
UPDATE Books
SET price = price * 1.1
WHERE author_id = 1
RETURNING id, title, price;

-- Проверить что изменилось
UPDATE Users
SET last_login = NOW()
WHERE id = 42
RETURNING id, email, last_login;
```

---

## 🔴 RETURNING в DELETE

```sql
-- Получить удалённые строки (например, для архива)
DELETE FROM Books
WHERE price < 1
RETURNING *;

DELETE FROM Sessions
WHERE expires_at < NOW()
RETURNING user_id, expires_at;
```

---

## 🟣 RETURNING в CTE

Мощный паттерн — перемещение данных между таблицами за один запрос:

```sql
-- Удалить из основной таблицы, вставить в архив
WITH deleted AS (
    DELETE FROM Books
    WHERE created_at < NOW() - INTERVAL '1 year'
    RETURNING *
)
INSERT INTO BooksArchive
SELECT * FROM deleted;

-- Вставить и сразу использовать id в другом INSERT
WITH new_author AS (
    INSERT INTO Authors (name) VALUES ('Orwell')
    RETURNING id
)
INSERT INTO Books (title, author_id)
SELECT '1984', id FROM new_author;
```

---

## ✅ Главные правила

✅ `RETURNING *` — вернуть все поля затронутых строк  
✅ Работает с INSERT, UPDATE, DELETE  
✅ В CTE позволяет цепочкой выполнять DML-операции  
✅ В MySQL нет аналога — только `LAST_INSERT_ID()` для INSERT  

---

## 🔗 Связанные темы

- [[../05 — 🛠 Администрирование/02 — ➕ INSERT]]
- [[../04 — 🚀 Продвинутые темы/03 — 🔁 CTE и рекурсия]]
- [[02 — ⚔️ ON CONFLICT (Upsert)]]

---

#postgresql/специфика #returning
