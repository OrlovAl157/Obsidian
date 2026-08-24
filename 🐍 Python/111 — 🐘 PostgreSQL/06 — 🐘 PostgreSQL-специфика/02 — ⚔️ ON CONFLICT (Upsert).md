---
tags: [postgresql, специфика, upsert]
difficulty: intermediate
---

# ⚔️ ON CONFLICT (Upsert) — памятка

> `ON CONFLICT` — PostgreSQL-способ сделать upsert: вставить строку, а если произошёл конфликт (дубликат PRIMARY KEY или UNIQUE) — либо пропустить, либо обновить. Это аналог `INSERT IGNORE` и `REPLACE` из MySQL, но гибче.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 DO NOTHING|DO NOTHING]]
- [[#🔵 DO UPDATE|DO UPDATE]]
- [[#🔴 Практические примеры|Практические примеры]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Аналог в MySQL | Что делает |
|---|---|---|
| `ON CONFLICT DO NOTHING` | `INSERT IGNORE` | пропустить при конфликте |
| `ON CONFLICT (col) DO UPDATE SET ...` | `REPLACE` / `ON DUPLICATE KEY UPDATE` | обновить при конфликте |
| `EXCLUDED` | — | данные которые пытались вставить |

---

## 🟢 DO NOTHING

```sql
-- Пропустить строку если конфликт по любому уникальному ограничению
INSERT INTO Books (id, title) VALUES (1, '1984')
ON CONFLICT DO NOTHING;

-- Пропустить если конфликт по конкретному полю
INSERT INTO Users (email, name) VALUES ('a@mail.com', 'Anna')
ON CONFLICT (email) DO NOTHING;
```

---

## 🔵 DO UPDATE

```sql
-- Обновить все поля при конфликте
INSERT INTO Books (id, title, price)
VALUES (1, '1984', 9.99)
ON CONFLICT (id) DO UPDATE
SET title = EXCLUDED.title,
    price = EXCLUDED.price;

-- Обновить только если новое значение лучше
INSERT INTO Products (sku, price, stock)
VALUES ('ABC-123', 9.99, 100)
ON CONFLICT (sku) DO UPDATE
SET price = EXCLUDED.price,
    stock = stock + EXCLUDED.stock   -- накапливать остаток!
WHERE EXCLUDED.price < Products.price;  -- только если цена снизилась

-- Обновить timestamp при конфликте (паттерн "seen at")
INSERT INTO UserSessions (user_id, last_seen)
VALUES (42, NOW())
ON CONFLICT (user_id) DO UPDATE
SET last_seen = EXCLUDED.last_seen;
```

**`EXCLUDED`** — это виртуальная таблица с данными, которые пытались вставить. Используется только внутри `DO UPDATE SET`.

---

## 🔴 Практические примеры

```sql
-- Счётчик просмотров — создать или обновить
INSERT INTO PageViews (url, views)
VALUES ('/home', 1)
ON CONFLICT (url) DO UPDATE
SET views = PageViews.views + 1;

-- Синхронизация данных из внешнего источника
INSERT INTO Products (external_id, name, price)
SELECT external_id, name, price FROM staging_products
ON CONFLICT (external_id) DO UPDATE
SET name  = EXCLUDED.name,
    price = EXCLUDED.price;
```

---

## ✅ Главные правила

✅ `DO NOTHING` — аналог `INSERT IGNORE`  
✅ `DO UPDATE` — аналог `ON DUPLICATE KEY UPDATE` (MySQL)  
✅ `EXCLUDED` — данные новой строки, недоступные после конфликта  
✅ В `DO UPDATE` можно использовать `WHERE` для условного обновления  
✅ Нет `REPLACE` в PostgreSQL — используй `ON CONFLICT DO UPDATE`  

---

## 🔗 Связанные темы

- [[../05 — 🛠 Администрирование/02 — ➕ INSERT]]
- [[01 — 🔄 RETURNING]]

---

#postgresql/специфика #upsert #on-conflict
