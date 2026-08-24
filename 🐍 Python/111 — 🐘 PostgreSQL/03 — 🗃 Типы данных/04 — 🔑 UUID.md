---
tags: [postgresql, специфика, uuid]
difficulty: intermediate
---

# 🔑 UUID — памятка

> UUID (Universally Unique Identifier) — 128-битный уникальный идентификатор. В PostgreSQL есть встроенный тип `UUID` и функции для его генерации. Полезен для распределённых систем, где автоинкремент может дать коллизии.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Использование UUID|Использование]]
- [[#🔵 Генерация UUID|Генерация]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `UUID` | тип данных |
| `gen_random_uuid()` | UUID v4 (случайный) |
| `uuid_generate_v4()` | UUID v4 (расширение uuid-ossp) |
| `CREATE EXTENSION "uuid-ossp"` | подключить расширение |

---

## 🟢 Использование UUID

```sql
-- UUID как первичный ключ
CREATE TABLE Users (
    id    UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    name  TEXT
);

-- Вставка без указания id
INSERT INTO Users (email, name) VALUES ('a@mail.com', 'Anna');

-- Вставка с явным UUID
INSERT INTO Users (id, email, name)
VALUES ('550e8400-e29b-41d4-a716-446655440000', 'b@mail.com', 'Ivan');
```

---

## 🔵 Генерация UUID

```sql
-- gen_random_uuid() — встроено с PostgreSQL 13 (расширение pgcrypto не нужно)
SELECT gen_random_uuid();   -- 'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11'

-- uuid_generate_v4() — через расширение (старый способ)
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
SELECT uuid_generate_v4();
```

---

## ✅ Главные правила

✅ `gen_random_uuid()` — встроен с PostgreSQL 13, не нужно расширение  
✅ UUID PK — хорошо для распределённых систем, хуже для производительности индекса  
✅ `SERIAL`/`BIGSERIAL` — быстрее для локальных систем  

---

## 🔗 Связанные темы

- [[01 — 🧱 Базовые типы данных]]
- [[../05 — 🛠 Администрирование/01 — 🏗 CREATE TABLE]]

---

#postgresql/типы-данных #uuid
