---
tags: [sql, union, intersect, except, множества]
difficulty: intermediate
---

# 🔗 UNION и операции над множествами — памятка

> `UNION` объединяет результаты нескольких `SELECT` запросов в одну таблицу. `UNION ALL` сохраняет дубликаты. В MySQL 8.0.31+ доступны `INTERSECT` и `EXCEPT` для операций пересечения и разности.

## Содержание

- [[#Справка|Справка]]
- [[#🔗 UNION — объединение результатов|UNION]]
- [[#🔵 UNION ALL и UNION DISTINCT|UNION ALL и UNION DISTINCT]]
- [[#📋 Правила объединения|Правила объединения]]
- [[#📑 ORDER BY и LIMIT в UNION|ORDER BY и LIMIT]]
- [[#🏷 Вспомогательные поля|Вспомогательные поля]]
- [[#⚙️ UNION как подзапрос|UNION как подзапрос]]
- [[#🔶 INTERSECT и EXCEPT|INTERSECT и EXCEPT]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

## Справка

| Оператор | Что делает | Дубликаты |
| --- | --- | --- |
| `UNION` | Объединяет результаты | Удаляет |
| `UNION ALL` | Объединяет результаты | Сохраняет |
| `UNION DISTINCT` | То же что `UNION` | Удаляет |
| `INTERSECT` | Только общие строки | Удаляет |
| `EXCEPT` | Строки первого минус второй | Удаляет |

---




## 🔗 UNION — объединение результатов

**Назначение:** объединить строки из нескольких запросов в одну таблицу. Запрос, объединяющий результаты нескольких запросов, называют **комбинированным**.

| Конструкция | Что делает |
| --- | --- |
| `SELECT ... UNION SELECT ...` | Объединяет два запроса, удаляет дубликаты |
| `SELECT ... UNION ... UNION ...` | Объединяет три и более запросов |

**Примеры:**

```sql
-- Объединить студентов и преподавателей
SELECT id, name, surname
FROM Students

UNION

SELECT id, name, surname
FROM Teachers;

-- Результат:
-- id | name    | surname
-- ---+---------+---------
--  1 | Peter   | Parker      ← студенты
--  2 | Gwen    | Stacy
--  3 | Flash   | Tompson
--  4 | Mary    | Jane
--  5 | Eddie   | Brock
--  1 | Norman  | Osborn      ← преподаватели
--  2 | Curt    | Connors
--  3 | Harry   | Osborn
--  4 | Flint   | Marko
--  5 | Richard | Parker
```

```sql
-- Три и более запросов — UNION между каждым
SELECT id, name, surname FROM Students WHERE id = 1
UNION
SELECT id, name, surname FROM Students WHERE id = 2
UNION
SELECT id, name, surname FROM Students WHERE id = 3;

-- Результат:
-- id | name  | surname
-- ---+-------+---------
--  1 | Peter | Parker
--  2 | Gwen  | Stacy
--  3 | Flash | Tompson
```

**Важное:**
- Имена полей в результате берутся из **первого** запроса
- Количество полей во всех запросах должно быть **одинаковым**
- Типы полей могут отличаться, но объединять разнотипные данные не рекомендуется

---




## 🔵 UNION ALL и UNION DISTINCT

**Назначение:** управлять поведением дубликатов при объединении.

| Конструкция | Что делает |
| --- | --- |
| `UNION` / `UNION DISTINCT` | Удаляет одинаковые строки |
| `UNION ALL` | Сохраняет все строки включая дубликаты |

**Примеры:**

```sql
-- UNION DISTINCT — только уникальные фамилии
SELECT surname FROM Students
UNION DISTINCT
SELECT surname FROM Teachers;

-- Результат: Parker и Osborn встречаются в обоих таблицах
-- но выводятся один раз
-- surname
-- ---------
-- Parker
-- Stacy
-- Tompson
-- Jane
-- Brock
-- Osborn
-- Connors
-- Marko
```

```sql
-- UNION ALL — все фамилии включая повторы
SELECT surname FROM Students
UNION ALL
SELECT surname FROM Teachers;

-- Результат: Parker и Osborn встречаются дважды
-- surname
-- ---------
-- Parker
-- Stacy
-- ...
-- Osborn   ← дубликат!
-- ...
-- Parker   ← дубликат!
```

**Важное:** `UNION ALL` работает быстрее `UNION` — не выполняет дополнительную сортировку для поиска дубликатов. Используй `UNION ALL` когда дубликаты заведомо невозможны или не важны.

---




## 📋 Правила объединения

**Правило 1 — одинаковое количество полей:**

```sql
SELECT name, surname        -- 2 поля
FROM Students
UNION
SELECT id, name, surname    -- 3 поля ❌
FROM Teachers;
-- ERROR 1222: The used SELECT statements have a different number of columns
```

**Правило 2 — имена полей из первого запроса:**

```sql
SELECT name AS firstname, surname AS lastname
FROM Students
UNION
SELECT name, surname
FROM Teachers;

-- Результат: колонки называются firstname и lastname
-- firstname | lastname
-- ----------+---------
-- Peter     | Parker
-- Norman    | Osborn
```

**Правило 3 — типы могут отличаться, но осторожно:**

```sql
SELECT name, surname, age       -- age: число
FROM Students
UNION
SELECT name, surname, subject   -- subject: строка
FROM Teachers;

-- Результат: поле age содержит и числа и строки
-- name    | surname | age
-- --------+---------+-------------
-- Peter   | Parker  | 18          ← число
-- Norman  | Osborn  | Algebra     ← строка
```

---




## 📑 ORDER BY и LIMIT в UNION

**Назначение:** сортировать и ограничивать результаты комбинированных запросов.

| Конструкция | Что делает |
| --- | --- |
| `... UNION ... ORDER BY col` | Сортирует весь объединённый результат |
| `(SELECT ... LIMIT n) UNION (SELECT ...)` | Ограничивает отдельный запрос |
| `(SELECT ... ORDER BY col LIMIT n) UNION ...` | Локальная сортировка + LIMIT |

**Глобальная сортировка — после последнего запроса:**

```sql
SELECT id, name, surname FROM Students
UNION
SELECT id, name, surname FROM Teachers
ORDER BY id;    -- сортирует весь результат

-- id | name    | surname
-- ---+---------+---------
--  1 | Peter   | Parker
--  1 | Norman  | Osborn
--  2 | Gwen    | Stacy
--  2 | Curt    | Connors
-- ...
```

**Локальный LIMIT — запрос в скобках:**

```sql
(SELECT id, name, surname FROM Students LIMIT 2)
UNION
(SELECT id, name, surname FROM Teachers LIMIT 2);

-- id | name   | surname
-- ---+--------+---------
--  1 | Peter  | Parker
--  2 | Gwen   | Stacy
--  1 | Norman | Osborn
--  2 | Curt   | Connors
```

**ORDER BY + LIMIT — последняя запись из каждой таблицы:**

```sql
(SELECT id, name FROM Students ORDER BY id DESC LIMIT 1)
UNION
(SELECT id, name FROM Teachers ORDER BY id DESC LIMIT 1);

-- id | name
-- ---+---------
--  5 | Eddie
--  5 | Richard
```

**Важное:** `ORDER BY` внутри подзапроса **без LIMIT** MySQL игнорирует — сортировка не выполняется. Полезен только в связке с `LIMIT`.

---




## 🏷 Вспомогательные поля

**Назначение:** добавить метку источника или гарантировать порядок групп в результате.

**Метка источника — из какой таблицы строка:**

```sql
SELECT name, surname, 'Students' AS from_table
FROM Students
UNION
SELECT name, surname, 'Teachers'
FROM Teachers;

-- name    | surname | from_table
-- --------+---------+------------
-- Peter   | Parker  | Students
-- Gwen    | Stacy   | Students
-- Norman  | Osborn  | Teachers
-- Curt    | Connors | Teachers
```

**Гарантированный порядок групп:**

```sql
(SELECT name, surname, 1 AS sortvalue FROM Students)
UNION
(SELECT name, surname, 2 FROM Teachers)
ORDER BY sortvalue;

-- name    | surname | sortvalue
-- --------+---------+-----------
-- Peter   | Parker  | 1         ← все студенты первыми
-- Gwen    | Stacy   | 1
-- Norman  | Osborn  | 2         ← все преподаватели после
-- Curt    | Connors | 2
```

**Важное:** без `sortvalue` `UNION` не гарантирует что строки из первого запроса идут раньше второго.

---




## ⚙️ UNION как подзапрос

**Назначение:** использовать результат `UNION` как таблицу в `FROM`.

```sql
SELECT name, surname
FROM (
    SELECT * FROM Students
    UNION
    SELECT * FROM Teachers
) AS StudentsTeachers;    -- псевдоним обязателен!

-- name    | surname
-- --------+---------
-- Peter   | Parker
-- Norman  | Osborn
-- ...
```

**Подсчёт всех записей:**

```sql
SELECT COUNT(*) AS total
FROM (
    SELECT id FROM Students
    UNION ALL
    SELECT id FROM Teachers
) AS everyone;

-- total
-- -----
-- 10
```

**Важное:** псевдоним (`AS StudentsTeachers`) обязателен — без него `ERROR 1248: Every derived table must have its own alias`.

---




## 🔶 INTERSECT и EXCEPT

Доступны в **MySQL 8.0.31+**. На Stepik (MySQL 8.0.27) используй аналоги через JOIN.

**INTERSECT — только общие строки (пересечение):**

```sql
SELECT surname FROM Students
INTERSECT
SELECT surname FROM Teachers;

-- surname
-- --------
-- Parker   ← есть в обоих таблицах
```

Аналог для MySQL < 8.0.31:
```sql
SELECT DISTINCT s.surname
FROM Students s
JOIN Teachers t ON s.surname = t.surname;
```

**EXCEPT — строки первого которых нет во втором (разность):**

```sql
SELECT surname FROM Students
EXCEPT
SELECT surname FROM Teachers;

-- surname
-- --------
-- Stacy    ← только среди студентов
-- Tompson
-- Jane
-- Brock
```

Аналог для MySQL < 8.0.31:
```sql
SELECT DISTINCT s.surname
FROM Students s
LEFT JOIN Teachers t ON s.surname = t.surname
WHERE t.id IS NULL;
```

| Оператор | Аналог через JOIN | MySQL |
| --- | --- | --- |
| `INTERSECT` | `INNER JOIN` + `DISTINCT` | 8.0.31+ |
| `EXCEPT` | `LEFT JOIN` + `WHERE IS NULL` | 8.0.31+ |

---




## ⚡ Быстрые примеры

```sql
-- Уникальные фамилии из двух таблиц
SELECT surname FROM Students
UNION
SELECT surname FROM Teachers;

-- Все фамилии включая повторы
SELECT surname FROM Students
UNION ALL
SELECT surname FROM Teachers;

-- С меткой источника
SELECT name, surname, 'student' AS role FROM Students
UNION ALL
SELECT name, surname, 'teacher' FROM Teachers;

-- Последняя запись из каждой таблицы
(SELECT id, name FROM Students ORDER BY id DESC LIMIT 1)
UNION
(SELECT id, name FROM Teachers ORDER BY id DESC LIMIT 1);

-- FULL OUTER JOIN через UNION (MySQL не поддерживает FULL OUTER JOIN напрямую)
SELECT u.id, o.amount FROM Users u LEFT JOIN Orders o ON u.id = o.user_id
UNION
SELECT u.id, o.amount FROM Users u RIGHT JOIN Orders o ON u.id = o.user_id;
```

---

## ⚠️ Частые ошибки

**❌ ОШИБКА — разное количество полей:**
```sql
SELECT name, surname FROM Students
UNION
SELECT id, name, surname FROM Teachers;
```
❌ ERROR 1222: The used SELECT statements have a different number of columns

**✅ ВЕРНО:**
```sql
SELECT name, surname FROM Students
UNION
SELECT name, surname FROM Teachers;
```

---

**❌ ОШИБКА — забыл псевдоним в FROM:**
```sql
SELECT name FROM (
    SELECT * FROM Students UNION SELECT * FROM Teachers
);
```
❌ ERROR 1248: Every derived table must have its own alias

**✅ ВЕРНО:**
```sql
SELECT name FROM (
    SELECT * FROM Students UNION SELECT * FROM Teachers
) AS everyone;
```

---

**❌ ОШИБКА — ORDER BY внутри без LIMIT:**
```sql
(SELECT id, name FROM Students ORDER BY id DESC)
UNION
(SELECT id, name FROM Teachers);
```
❌ ORDER BY без LIMIT внутри подзапроса игнорируется MySQL

**✅ ВЕРНО:**
```sql
(SELECT id, name FROM Students ORDER BY id DESC LIMIT 5)
UNION
(SELECT id, name FROM Teachers);
```

---

## 🔗 Связанные темы

- [[04 — 🔗 JOIN - объединение таблиц]]
- [[06 — 🧮 GROUP BY и HAVING]]
- [[07 — 🔎 Подзапросы]]

---

#sql #union #intersect #except #множества
