---
tags: [postgresql, dml, update]
difficulty: beginner
---

# ✏️ UPDATE — памятка

> `UPDATE` изменяет существующие записи. В PostgreSQL есть `UPDATE ... FROM` для обновления с JOIN и `RETURNING` для получения изменённых данных.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Базовый синтаксис|Базовый синтаксис]]
- [[#🔵 UPDATE FROM — обновление с JOIN|UPDATE FROM]]
- [[#🟣 RETURNING|RETURNING]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `UPDATE t SET col = val` | обновить все строки |
| `UPDATE t SET col = val WHERE ...` | обновить по условию |
| `UPDATE t SET col = val FROM t2` | обновить с данными из другой таблицы |
| `UPDATE ... RETURNING *` | вернуть изменённые строки |

---

## 🟢 Базовый синтаксис

```sql
-- Обновить одно поле
UPDATE Books SET price = 9.99 WHERE id = 1;

-- Обновить несколько полей
UPDATE Books
SET price = 9.99,
    title = '1984 (updated)'
WHERE id = 1;

-- Вычисляемое значение
UPDATE Books SET price = price * 1.1;   -- поднять цену на 10%

-- Сбросить на NULL
UPDATE Books SET description = NULL WHERE id = 1;
```

---

## 🔵 UPDATE FROM — обновление с JOIN

В PostgreSQL нет `UPDATE ... JOIN` как в MySQL — вместо него `UPDATE ... FROM`:

```sql
-- MySQL-стиль (не работает в PostgreSQL):
-- UPDATE Books b JOIN Authors a ON b.author_id = a.id
-- SET b.author_name = a.name

-- PostgreSQL-стиль:
UPDATE Books
SET author_name = Authors.name
FROM Authors
WHERE Books.author_id = Authors.id;

-- С условием
UPDATE Books
SET price = price * 0.9
FROM Authors
WHERE Books.author_id = Authors.id
  AND Authors.country = 'UK';
```

---

## 🟣 RETURNING

```sql
-- Получить обновлённые строки
UPDATE Books
SET price = price * 1.1
WHERE author = 'King'
RETURNING id, title, price;

-- Вернуть всё
UPDATE Books SET price = 0 WHERE price < 0
RETURNING *;
```

---

## ⚠️ Частые ошибки

```sql
-- ❌ UPDATE с JOIN — MySQL-синтаксис не работает
UPDATE Books b
JOIN Authors a ON b.author_id = a.id
SET b.price = 0;   -- ❌ PostgreSQL не понимает

-- ✅ PostgreSQL:
UPDATE Books
SET price = 0
FROM Authors
WHERE Books.author_id = Authors.id;
```

---

## ✅ Главные правила

✅ `UPDATE ... FROM` вместо `UPDATE ... JOIN`  
✅ `RETURNING` — получить изменённые строки без отдельного SELECT  
✅ Без `WHERE` — обновятся ВСЕ строки таблицы  

---

## 🔗 Связанные темы

- [[02 — ➕ INSERT]]
- [[04 — 🗑️ DELETE]]

---

#postgresql/dml #update
