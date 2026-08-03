---
tags: [sql, union, intersect, except, множества]
difficulty: intermediate
---

# 🔗 UNION и операции над множествами — памятка

> `UNION` объединяет результаты нескольких запросов в одну таблицу. В MySQL 8.0.31+ добавлены `INTERSECT` (пересечение) и `EXCEPT` (разность). Все три — операции над множествами: работают со строками как с элементами множества.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 UNION — объединение результатов|UNION]]
- [[#🔵 UNION ALL vs UNION DISTINCT|UNION ALL vs UNION DISTINCT]]
- [[#🔴 Правила объединения|Правила объединения]]
- [[#🟡 ORDER BY и LIMIT в UNION|ORDER BY и LIMIT]]
- [[#🟣 Вспомогательные поля|Вспомогательные поля]]
- [[#⚙️ UNION как подзапрос|UNION как подзапрос]]
- [[#🔶 INTERSECT и EXCEPT|INTERSECT и EXCEPT]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает | Дубликаты |
|---|---|---|
| `UNION` | Объединяет результаты запросов | Удаляет |
| `UNION ALL` | Объединяет результаты запросов | Сохраняет |
| `UNION DISTINCT` | То же что `UNION` | Удаляет |
| `INTERSECT` | Только общие строки | Удаляет |
| `EXCEPT` | Строки первого минус второй | Удаляет |

| Правило | Описание |
|---|---|
| Количество полей | Обязательно одинаковое во всех запросах |
| Имена полей | Берутся из первого запроса |
| Типы полей | Могут отличаться (но не рекомендуется) |
| `ORDER BY` глобальный | Указывается после последнего запроса |
| `ORDER BY` / `LIMIT` локальный | Запрос заключается в скобки |

---

## 📊 Общая схема

```
Запрос 1        Запрос 2        Результат
─────────       ─────────       ──────────────────────────
SELECT ...  +   SELECT ...  →   Объединённые строки

UNION:       все строки, дубликаты удаляются
UNION ALL:   все строки, дубликаты сохраняются
INTERSECT:   только строки присутствующие в ОБОИХ результатах
EXCEPT:      строки из первого которых НЕТ во втором

Визуально (множества):
A = {1, 2, 3, 4}    B = {3, 4, 5, 6}

A UNION B      = {1, 2, 3, 4, 5, 6}   ← все уникальные
A UNION ALL B  = {1, 2, 3, 4, 3, 4, 5, 6}  ← все подряд
A INTERSECT B  = {3, 4}               ← только общие
A EXCEPT B     = {1, 2}               ← есть в A, нет в B
```

---

## 🟢 UNION — объединение результатов

**Назначение:** объединить строки из нескольких запросов в одну таблицу.

Используемые таблицы:

```
Students                          Teachers
+----+-------+---------+-----+    +----+---------+---------+-------------+
| id | name  | surname | age |    | id | name    | surname | subject     |
+----+-------+---------+-----+    +----+---------+---------+-------------+
|  1 | Peter | Parker  |  18 |    |  1 | Norman  | Osborn  | algebra     |
|  2 | Gwen  | Stacy   |  21 |    |  2 | Curt    | Connors | informatics |
|  3 | Flash | Tompson |  19 |    |  3 | Harry   | Osborn  | english     |
|  4 | Mary  | Jane    |  18 |    |  4 | Flint   | Marko   | chemistry   |
|  5 | Eddie | Brock   |  21 |    |  5 | Richard | Parker  | physics     |
+----+-------+---------+-----+    +----+---------+---------+-------------+
```

**Базовый пример:**

```sql
SELECT id, name, surname
FROM Students

UNION

SELECT id, name, surname
FROM Teachers;
```

```
+----+---------+---------+
| id | name    | surname |
+----+---------+---------+
|  1 | Peter   | Parker  |
|  2 | Gwen    | Stacy   |
|  3 | Flash   | Tompson |
|  4 | Mary    | Jane    |
|  5 | Eddie   | Brock   |
|  1 | Norman  | Osborn  |
|  2 | Curt    | Connors |
|  3 | Harry   | Osborn  |
|  4 | Flint   | Marko   |
|  5 | Richard | Parker  |
+----+---------+---------+
```

**Три и более запросов — UNION между каждым:**

```sql
SELECT id, name, surname FROM Students WHERE id = 1
UNION
SELECT id, name, surname FROM Students WHERE id = 2
UNION
SELECT id, name, surname FROM Students WHERE id = 3;
```

```
+----+-------+---------+
| id | name  | surname |
+----+-------+---------+
|  1 | Peter | Parker  |
|  2 | Gwen  | Stacy   |
|  3 | Flash | Tompson |
+----+-------+---------+
```

**Важное:**
- Имена полей в результате берутся из **первого** запроса
- Поле называется `age` даже если во втором запросе там `subject`

```sql
SELECT name AS firstname, surname AS lastname FROM Students
UNION
SELECT name, surname FROM Teachers;
-- Названия колонок: firstname, lastname (из первого запроса)
```

---

## 🔵 UNION ALL vs UNION DISTINCT

По умолчанию `UNION` удаляет дубликаты. Управлять этим можно явно:

```sql
-- UNION DISTINCT — удалить дубликаты (по умолчанию)
SELECT surname FROM Students
UNION DISTINCT
SELECT surname FROM Teachers;
```

```
+---------+
| surname |     ← Parker и Osborn встречаются в обоих таблицах
+---------+       но выводятся один раз
| Parker  |
| Stacy   |
| Tompson |
| Jane    |
| Brock   |
| Osborn  |
| Connors |
| Marko   |
+---------+
```

```sql
-- UNION ALL — сохранить все строки включая дубликаты
SELECT surname FROM Students
UNION ALL
SELECT surname FROM Teachers;
```

```
+---------+
| surname |     ← Parker появляется дважды (студент и преподаватель)
+---------+       Osborn тоже дважды (два преподавателя)
| Parker  |
| Stacy   |
| Tompson |
| Jane    |
| Brock   |
| Osborn  |
| Connors |
| Osborn  |  ← дубликат!
| Marko   |
| Parker  |  ← дубликат!
+---------+
```

**Когда использовать:**

| | `UNION` | `UNION ALL` |
|---|---|---|
| Нужны уникальные строки | ✅ | ❌ |
| Важна скорость | ❌ (доп. сортировка) | ✅ (быстрее) |
| Дубликаты допустимы/нужны | ❌ | ✅ |

---

## 🔴 Правила объединения

**Правило 1 — количество полей должно совпадать:**

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
SELECT name AS firstname, surname AS lastname FROM Students
UNION
SELECT name, surname FROM Teachers;
-- Колонки называются firstname и lastname
```

**Правило 3 — типы могут отличаться (но осторожно):**

```sql
SELECT name, surname, age       -- age: число
FROM Students
UNION
SELECT name, surname, subject   -- subject: строка
FROM Teachers;
-- Работает, но поле age будет содержать и числа и строки
```

```
+---------+---------+-------------+
| name    | surname | age         |
+---------+---------+-------------+
| Peter   | Parker  | 18          |  ← число
| Norman  | Osborn  | Algebra     |  ← строка
+---------+---------+-------------+
```

> ⚠️ Объединять разнотипные данные не рекомендуется — только если это осознанное решение.

---

## 🟡 ORDER BY и LIMIT в UNION

**Глобальная сортировка всего результата** — `ORDER BY` после последнего запроса:

```sql
SELECT id, name, surname FROM Students
UNION
SELECT id, name, surname FROM Teachers
ORDER BY id;    -- ← сортирует весь объединённый результат
```

```
+----+---------+---------+
| id | name    | surname |
+----+---------+---------+
|  1 | Peter   | Parker  |
|  1 | Norman  | Osborn  |
|  2 | Gwen    | Stacy   |
|  2 | Curt    | Connors |
|  3 | Flash   | Tompson |
|  3 | Harry   | Osborn  |
|  4 | Mary    | Jane    |
|  4 | Flint   | Marko   |
|  5 | Eddie   | Brock   |
|  5 | Richard | Parker  |
+----+---------+---------+
```

**Локальный LIMIT/ORDER BY** — запрос в скобках:

```sql
(SELECT id, name, surname FROM Students LIMIT 2)
UNION
(SELECT id, name, surname FROM Teachers LIMIT 2);
```

```
+----+--------+---------+
| id | name   | surname |
+----+--------+---------+
|  1 | Peter  | Parker  |
|  2 | Gwen   | Stacy   |
|  1 | Norman | Osborn  |
|  2 | Curt   | Connors |
+----+--------+---------+
```

**ORDER BY + LIMIT — выбрать последнюю запись из каждой таблицы:**

```sql
(SELECT id, name, surname FROM Students ORDER BY id DESC LIMIT 1)
UNION
(SELECT id, name, surname FROM Teachers ORDER BY id DESC LIMIT 1);
```

```
+----+---------+---------+
| id | name    | surname |
+----+---------+---------+
|  5 | Eddie   | Brock   |
|  5 | Richard | Parker  |
+----+---------+---------+
```

**Важное:** `ORDER BY` внутри подзапроса без `LIMIT` **игнорируется** — MySQL оптимизирует и не выполняет сортировку. Полезен только в связке с `LIMIT`.

```sql
-- ❌ ORDER BY без LIMIT внутри — игнорируется
(SELECT id, name FROM Students ORDER BY id DESC)
UNION
(SELECT id, name FROM Teachers ORDER BY id DESC);
-- Порядок в результате не гарантирован!
```

---

## 🟣 Вспомогательные поля

**Добавить метку источника** — из какой таблицы пришла строка:

```sql
SELECT name, surname, 'Students' AS from_table
FROM Students
UNION
SELECT name, surname, 'Teachers'
FROM Teachers;
```

```
+---------+---------+------------+
| name    | surname | from_table |
+---------+---------+------------+
| Peter   | Parker  | Students   |
| Gwen    | Stacy   | Students   |
| Norman  | Osborn  | Teachers   |
| Curt    | Connors | Teachers   |
+---------+---------+------------+
```

**Гарантировать порядок групп** — сначала студенты, потом преподаватели:

```sql
(SELECT name, surname, 1 AS sortvalue FROM Students)
UNION
(SELECT name, surname, 2 FROM Teachers)
ORDER BY sortvalue;
```

```
+---------+---------+-----------+
| name    | surname | sortvalue |
+---------+---------+-----------+
| Peter   | Parker  | 1         |  ← все студенты первыми
| Gwen    | Stacy   | 1         |
| Eddie   | Brock   | 1         |
| Norman  | Osborn  | 2         |  ← все преподаватели после
| Curt    | Connors | 2         |
+---------+---------+-----------+
```

> Без `sortvalue` `UNION` не гарантирует что строки из первого запроса идут раньше строк второго.

---

## ⚙️ UNION как подзапрос

Результат `UNION` можно использовать как таблицу в `FROM`:

```sql
SELECT name, surname
FROM (
    SELECT * FROM Students
    UNION
    SELECT * FROM Teachers
) AS StudentsTeachers;
```

```
+---------+---------+
| name    | surname |
+---------+---------+
| Peter   | Parker  |
| Gwen    | Stacy   |
| Norman  | Osborn  |
| Curt    | Connors |
+---------+---------+
```

**Важное:** при использовании в `FROM` — обязательно давать псевдоним (`AS StudentsTeachers`), иначе ошибка:
```
ERROR 1248: Every derived table must have its own alias
```

---

## 🔶 INTERSECT и EXCEPT

Доступны в **MySQL 8.0.31+**. Применяются по тем же правилам что и `UNION`.

**INTERSECT — только общие строки (пересечение):**

```sql
SELECT surname FROM Students
INTERSECT
SELECT surname FROM Teachers;
```

```
+---------+
| surname |
+---------+
| Parker  |   ← есть и среди студентов и среди преподавателей
+---------+
```

Аналог через JOIN:
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
```

```
+---------+
| surname |
+---------+
| Stacy   |   ← только среди студентов
| Tompson |
| Jane    |
| Brock   |
+---------+
```

Аналог через LEFT JOIN:
```sql
SELECT DISTINCT s.surname
FROM Students s
LEFT JOIN Teachers t ON s.surname = t.surname
WHERE t.id IS NULL;
```

**Сравнение операторов:**

| Оператор | Аналог через JOIN | MySQL версия |
|---|---|---|
| `UNION` | — | Все версии |
| `INTERSECT` | `INNER JOIN` + `DISTINCT` | 8.0.31+ |
| `EXCEPT` | `LEFT JOIN` + `WHERE IS NULL` | 8.0.31+ |

> ⚠️ На Stepik используется MySQL 8.0.27 — `INTERSECT` и `EXCEPT` там недоступны. Используй аналоги через JOIN.

---

## ⚡ Быстрые примеры

```sql
-- Объединить две таблицы с меткой источника
SELECT name, surname, 'student' AS role FROM Students
UNION ALL
SELECT name, surname, 'teacher' FROM Teachers;

-- Последняя запись из каждой таблицы
(SELECT id, name FROM Students ORDER BY id DESC LIMIT 1)
UNION
(SELECT id, name FROM Teachers ORDER BY id DESC LIMIT 1);

-- Уникальные фамилии из обеих таблиц
SELECT surname FROM Students
UNION
SELECT surname FROM Teachers;

-- Все фамилии включая повторы
SELECT surname FROM Students
UNION ALL
SELECT surname FROM Teachers;

-- Фамилии только среди студентов (аналог EXCEPT)
SELECT DISTINCT s.surname
FROM Students s
LEFT JOIN Teachers t ON s.surname = t.surname
WHERE t.id IS NULL;

-- UNION как подзапрос — посчитать всех
SELECT COUNT(*) AS total
FROM (
    SELECT id FROM Students
    UNION ALL
    SELECT id FROM Teachers
) AS all_people;

-- Гарантированный порядок: сначала студенты, потом преподаватели
(SELECT name, surname, 1 AS ord FROM Students)
UNION
(SELECT name, surname, 2 FROM Teachers)
ORDER BY ord, surname;
```

---

## 💡 Практические замечания

- `UNION` медленнее `UNION ALL` — выполняет дополнительную сортировку для поиска дубликатов
- Если дубликаты заведомо невозможны — используй `UNION ALL` для производительности
- `ORDER BY` глобальный пишется один раз после последнего запроса — без скобок
- `ORDER BY` внутри подзапроса без `LIMIT` MySQL может просто проигнорировать
- При использовании в `FROM` — псевдоним обязателен
- `INTERSECT` и `EXCEPT` — используй аналоги через JOIN если MySQL < 8.0.31
- Вспомогательное поле-число — лучший способ гарантировать порядок групп в результате

---

## ⚠️ Частые ошибки

**❌ Разное количество полей:**
```sql
SELECT name, surname FROM Students
UNION
SELECT id, name, surname FROM Teachers;  -- ❌ ERROR 1222
```

**❌ ORDER BY внутри без LIMIT — игнорируется:**
```sql
(SELECT id, name FROM Students ORDER BY id DESC)  -- ❌ сортировка не сработает
UNION
(SELECT id, name FROM Teachers);
-- Используй ORDER BY DESC только с LIMIT
```

**❌ Забыл псевдоним при UNION в FROM:**
```sql
SELECT name FROM (
    SELECT * FROM Students
    UNION
    SELECT * FROM Teachers
);  -- ❌ ERROR 1248: Every derived table must have its own alias

SELECT name FROM (
    SELECT * FROM Students
    UNION
    SELECT * FROM Teachers
) AS everyone;  -- ✅
```

**❌ Ожидал что UNION сохранит порядок строк:**
```sql
-- UNION не гарантирует порядок!
-- Для гарантии используй вспомогательное поле + ORDER BY
(SELECT name, 1 AS ord FROM Students)
UNION
(SELECT name, 2 FROM Teachers)
ORDER BY ord;  -- ✅
```

---

## ✅ Главные правила

✅ `UNION` удаляет дубликаты, `UNION ALL` сохраняет  
✅ Количество полей во всех запросах обязательно одинаковое  
✅ Имена полей берутся из первого запроса  
✅ `ORDER BY` глобальный — после последнего запроса, без скобок  
✅ `ORDER BY` / `LIMIT` локальный — запрос в скобках  
✅ `ORDER BY` без `LIMIT` внутри подзапроса UNION игнорируется  
✅ `UNION` в `FROM` — обязательно давать псевдоним  
✅ Для гарантированного порядка групп — вспомогательное числовое поле + `ORDER BY`  
✅ `UNION ALL` быстрее `UNION` — используй когда дубликаты не важны  

---

## 🔗 Связанные темы

- [[04 — 🔗 JOIN - объединение таблиц]]
- [[06 — 🧮 GROUP BY и HAVING]]
- [[07 — 🔎 Подзапросы]]

---

#sql #union #intersect #except #множества
