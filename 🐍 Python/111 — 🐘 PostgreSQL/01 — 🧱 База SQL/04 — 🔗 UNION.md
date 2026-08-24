---
tags: [postgresql, sql, union]
difficulty: beginner
---

# 🔗 UNION и операции над множествами — памятка

> `UNION` объединяет результаты нескольких SELECT. В PostgreSQL все стандартные операции над множествами: `UNION`, `INTERSECT`, `EXCEPT`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 UNION|UNION]]
- [[#🔵 INTERSECT и EXCEPT|INTERSECT и EXCEPT]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Описание |
|---|---|
| `UNION` | объединить, убрать дубли |
| `UNION ALL` | объединить, сохранить дубли |
| `INTERSECT` | пересечение (общие строки) |
| `INTERSECT ALL` | пересечение с дублями |
| `EXCEPT` | разность (строки из первого, которых нет во втором) |
| `EXCEPT ALL` | разность с дублями |

---

## 🟢 UNION

```sql
-- Объединить без дублей
SELECT name FROM Authors
UNION
SELECT name FROM Publishers;

-- Объединить с дублями (быстрее)
SELECT name FROM Authors
UNION ALL
SELECT name FROM Publishers;

-- Требования:
-- 1. Одинаковое количество столбцов
-- 2. Совместимые типы данных
-- 3. Имена столбцов берутся из первого SELECT
```

---

## 🔵 INTERSECT и EXCEPT

```sql
-- INTERSECT — строки, которые есть в обоих запросах
SELECT author FROM Books WHERE genre = 'Fiction'
INTERSECT
SELECT author FROM Books WHERE price < 10;

-- EXCEPT — строки из первого, которых нет во втором
SELECT author FROM Books
EXCEPT
SELECT name FROM BlacklistedAuthors;
```

---

## ✅ Главные правила

✅ `UNION ALL` быстрее `UNION` — не удаляет дубли  
✅ Все операторы требуют одинаковое число столбцов с совместимыми типами  
✅ `ORDER BY` применяется ко всему результату, а не к отдельным частям  
✅ `INTERSECT` и `EXCEPT` — работают как в PostgreSQL, нет в MySQL до 8.0  

---

## 🔗 Связанные темы

- [[02 — 📊 SELECT]]
- [[03 — 🔗 JOIN]]

---

#postgresql/sql #union
