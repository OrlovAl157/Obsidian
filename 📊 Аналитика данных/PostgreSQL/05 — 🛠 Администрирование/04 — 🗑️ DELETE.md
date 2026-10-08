---
tags: [postgresql, dml, delete]
difficulty: beginner
---

# 🗑️ DELETE — памятка

> `DELETE` удаляет строки из таблицы. В PostgreSQL есть `DELETE ... USING` для удаления с условием из другой таблицы и `RETURNING` для получения удалённых данных. `TRUNCATE` сбрасывает счётчик `SEQUENCE`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Базовый синтаксис|Базовый синтаксис]]
- [[#🔵 DELETE USING — удаление с JOIN|DELETE USING]]
- [[#🟣 RETURNING|RETURNING]]
- [[#🔴 TRUNCATE|TRUNCATE]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `DELETE FROM t WHERE ...` | удалить строки по условию |
| `DELETE FROM t` | удалить все строки (медленно) |
| `DELETE FROM t USING t2` | удалить с условием из другой таблицы |
| `DELETE ... RETURNING *` | вернуть удалённые строки |
| `TRUNCATE t` | очистить таблицу быстро + сброс SEQUENCE |
| `TRUNCATE t RESTART IDENTITY` | сброс автоинкремента |
| `TRUNCATE t CASCADE` | каскадная очистка связанных таблиц |

---

## 🟢 Базовый синтаксис

```sql
-- Удалить по условию
DELETE FROM Books WHERE id = 1;
DELETE FROM Books WHERE price < 0;

-- Удалить все строки (медленнее TRUNCATE, но с RETURNING)
DELETE FROM Books;
```

---

## 🔵 DELETE USING — удаление с JOIN

В PostgreSQL нет `DELETE ... JOIN` как в MySQL — вместо него `DELETE ... USING`:

```sql
-- Удалить книги британских авторов
DELETE FROM Books
USING Authors
WHERE Books.author_id = Authors.id
  AND Authors.country = 'UK';
```

---

## 🟣 RETURNING

```sql
-- Получить удалённые строки
DELETE FROM Books
WHERE price < 5
RETURNING id, title, price;

-- Вернуть всё
DELETE FROM Books WHERE id = 1
RETURNING *;
```

---

## 🔴 TRUNCATE

```sql
-- Быстрая очистка таблицы
TRUNCATE Books;

-- Сбросить счётчик SEQUENCE (аналог AUTO_INCREMENT)
TRUNCATE Books RESTART IDENTITY;

-- Каскадно очистить связанные таблицы
TRUNCATE Authors CASCADE;
```

**Отличие от DELETE:**
- `TRUNCATE` — быстрее, не логируется построчно, сбрасывает SEQUENCE
- `DELETE` — медленнее, логируется, поддерживает `RETURNING` и `WHERE`

---

## ⚠️ Частые ошибки

```sql
-- ❌ DELETE с JOIN — MySQL-синтаксис не работает
DELETE b FROM Books b JOIN Authors a ...   -- ❌

-- ✅ PostgreSQL:
DELETE FROM Books USING Authors
WHERE Books.author_id = Authors.id;
```

---

## ✅ Главные правила

✅ `DELETE ... USING` вместо `DELETE ... JOIN`  
✅ `RETURNING` — получить удалённые строки  
✅ `TRUNCATE RESTART IDENTITY` — сброс счётчика SEQUENCE  
✅ `TRUNCATE CASCADE` — каскадная очистка связанных таблиц  
✅ Без `WHERE` — удалятся ВСЕ строки  

---

## 🔗 Связанные темы

- [[02 — ➕ INSERT]]
- [[03 — ✏️ UPDATE]]
- [[01 — 🏗 CREATE TABLE]]

---

#postgresql/dml #delete
