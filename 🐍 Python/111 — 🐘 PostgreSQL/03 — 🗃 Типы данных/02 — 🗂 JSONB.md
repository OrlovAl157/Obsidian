---
tags: [postgresql, типы-данных, jsonb]
difficulty: intermediate
---

# 🗂 JSONB — памятка

> `JSONB` — бинарное хранение JSON в PostgreSQL. В отличие от `JSON` (хранит как текст), `JSONB` индексируется, поддерживает быстрый поиск и специальные операторы. Это одно из ключевых преимуществ PostgreSQL перед MySQL.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Создание и вставка|Создание и вставка]]
- [[#🔵 Извлечение данных|Извлечение данных]]
- [[#🔴 Операторы|Операторы]]
- [[#🟡 Изменение JSONB|Изменение]]
- [[#🟣 Индексы на JSONB|Индексы]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает | Пример |
|---|---|---|
| `->` | получить элемент как JSON | `data->'name'` |
| `->>` | получить элемент как TEXT | `data->>'name'` |
| `#>` | путь к элементу как JSON | `data#>'{address,city}'` |
| `#>>` | путь к элементу как TEXT | `data#>>'{address,city}'` |
| `@>` | содержит ли JSON | `data @> '{"active": true}'` |
| `<@` | содержится ли в JSON | `'{"a":1}' <@ data` |
| `?` | существует ли ключ | `data ? 'email'` |
| `?|` | существует ли один из ключей | `data ?| array['a','b']` |
| `?&` | существуют ли все ключи | `data ?& array['a','b']` |

---

## 🟢 Создание и вставка

```sql
-- Таблица с JSONB
CREATE TABLE Users (
    id      SERIAL PRIMARY KEY,
    name    VARCHAR(100),
    meta    JSONB
);

-- Вставка JSON
INSERT INTO Users (name, meta)
VALUES (
    'Александр',
    '{"age": 30, "city": "Moscow", "tags": ["python", "sql"], "active": true}'
);

INSERT INTO Users (name, meta)
VALUES (
    'Anna',
    jsonb_build_object('age', 25, 'city', 'SPb', 'active', false)
);
```

---

## 🔵 Извлечение данных

```sql
-- -> вернёт JSON (с кавычками для строк)
SELECT meta->'city' FROM Users;           -- "Moscow"

-- ->> вернёт TEXT (без кавычек)
SELECT meta->>'city' FROM Users;          -- Moscow

-- Вложенный объект
SELECT meta->'address'->>'city' FROM Users;

-- Путь через #>>
SELECT meta#>>'{address,city}' FROM Users;

-- Элемент массива (индекс с 0)
SELECT meta->'tags'->0 FROM Users;        -- "python"
SELECT meta->'tags'->>0 FROM Users;       -- python
```

---

## 🔴 Операторы

```sql
-- @> — содержит ли JSONB
SELECT * FROM Users WHERE meta @> '{"active": true}';
SELECT * FROM Users WHERE meta @> '{"city": "Moscow"}';

-- ? — существует ли ключ
SELECT * FROM Users WHERE meta ? 'email';
SELECT * FROM Users WHERE meta ? 'age';

-- Поиск в массиве
SELECT * FROM Users WHERE meta->'tags' @> '["python"]';

-- Сравнение значений
SELECT * FROM Users WHERE (meta->>'age')::INTEGER > 25;
-- Обрати внимание: нужно явное приведение типа!
```

---

## 🟡 Изменение JSONB

```sql
-- jsonb_set — изменить поле
UPDATE Users
SET meta = jsonb_set(meta, '{city}', '"SPb"')
WHERE id = 1;

-- Добавить поле
UPDATE Users
SET meta = meta || '{"email": "a@mail.com"}'
WHERE id = 1;

-- Удалить поле
UPDATE Users
SET meta = meta - 'email'
WHERE id = 1;
```

---

## 🟣 Индексы на JSONB

```sql
-- GIN-индекс — для операторов @>, ?, ?|, ?&
CREATE INDEX idx_users_meta ON Users USING GIN (meta);

-- Индекс на конкретное поле JSON
CREATE INDEX idx_users_city ON Users ((meta->>'city'));
```

---

## ✅ Главные правила

✅ `JSONB` — бинарный (быстрый), `JSON` — текстовый (медленный) → используй `JSONB`  
✅ `->>` возвращает TEXT — нужно явное приведение типа для сравнений  
✅ `@>` — самый частый оператор для поиска по содержимому  
✅ GIN-индекс — для ускорения поиска по JSONB  
✅ `jsonb_set` — для изменения конкретного поля  

---

## 🔗 Связанные темы

- [[01 — 🧱 Базовые типы данных]]
- [[03 — 📦 Массивы]]
- [[../05 — 🛠 Администрирование/05 — 🚀 Индексы]]

---

#postgresql/типы-данных #jsonb
