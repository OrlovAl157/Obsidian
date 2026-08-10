---
tags: [sql, dml, update, администрирование]
difficulty: intermediate
---

# ✏️ UPDATE — обновление данных — памятка

> `UPDATE` изменяет значения полей в существующих записях. Можно обновить все записи или отдельные через `WHERE`, использовать вычисляемые значения, `CASE`, `JOIN` и подзапросы.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Обновление всех записей|Обновление всех записей]]
- [[#🔵 Обновление отдельных записей|Обновление отдельных записей]]
- [[#🔴 Вычисляемые значения|Вычисляемые значения]]
- [[#🟡 UPDATE CASE — условное обновление|UPDATE CASE]]
- [[#🟣 UPDATE JOIN — данные из другой таблицы|UPDATE JOIN]]
- [[#⚙️ ORDER BY и LIMIT|ORDER BY и LIMIT]]
- [[#🔧 IGNORE и DEFAULT|IGNORE и DEFAULT]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `UPDATE t SET col=val` | обновить все записи |
| `UPDATE t SET col=val WHERE cond` | обновить отдельные записи |
| `UPDATE t SET col=expr` | обновить вычисляемым значением |
| `UPDATE t SET col=CASE WHEN ... END` | условное обновление |
| `UPDATE t JOIN t2 ON ... SET ...` | обновление через JOIN |
| `UPDATE t SET col=val ORDER BY col LIMIT n` | обновить N записей в порядке сортировки |
| `UPDATE IGNORE t SET ...` | не прерываться при ошибках |
| `UPDATE t SET col=DEFAULT` | сбросить на значение по умолчанию |

---

## 📊 Общая схема

```
UPDATE таблица
SET поле1 = значение1,
    поле2 = значение2
WHERE условие          ← без WHERE — обновятся ВСЕ записи!
ORDER BY поле          ← порядок обновления
LIMIT n;               ← ограничение количества

Значение может быть:
├── Константа:          price = 9.99
├── Вычисление:         price = price * 0.9
├── Функция:            price = ROUND(price * 0.9, 2)
├── DEFAULT:            amount = DEFAULT
└── CASE:               price = CASE WHEN ... THEN ... END
```

---

## 🟢 Обновление всех записей

**Без WHERE — обновляются все записи:**

```sql
-- Установить одинаковую цену всем книгам
UPDATE Books
SET price = 9.99;

-- Было:
-- | 1 | The Shining   | 7.99 |
-- | 2 | Fight Club    | 9.99 |
-- | 3 | Catcher...    | 3.49 |

-- Стало:
-- | 1 | The Shining   | 9.99 |
-- | 2 | Fight Club    | 9.99 |
-- | 3 | Catcher...    | 9.99 |
```

**Обновление нескольких полей:**

```sql
-- Несколько полей через запятую
UPDATE Books
SET price = 9.99,
    amount = 50;

-- Оба поля изменятся у всех записей
```

**Важное:** если значение поля уже совпадает с обновляемым — оно не перезаписывается. MySQL сообщит что обновлено меньше строк чем ожидалось.

---

## 🔵 Обновление отдельных записей

```sql
-- Обновить одну запись по id
UPDATE Books
SET price = 1.99
WHERE id = 1;

-- Обновить по условию
UPDATE Books
SET price = 1.99
WHERE amount = 1;       -- все книги у которых осталось 1 штука

-- Несколько условий
UPDATE Books
SET price = 1.99
WHERE amount = 1
  AND price > 5;

-- Обновить через подзапрос — данные из другой таблицы
UPDATE Books
SET price = price * 0.5
WHERE author_id = (SELECT id
                   FROM Authors
                   WHERE author = 'Stephen King');

-- Было:
-- | 1 | The Shining   | 1 | 7.99 |  → 4.00
-- | 4 | The Green Mile| 1 | 15.99|  → 8.00
-- | 2 | Fight Club    | 2 | 9.99 |  без изменений
```

---

## 🔴 Вычисляемые значения

В SET можно использовать текущее значение поля в выражении:

```sql
-- Скидка 10% всем книгам
UPDATE Books
SET price = price * 0.9;

-- Скидка 10% с округлением до целых
UPDATE Books
SET price = ROUND(price * 0.9);

-- Скидка 10% с округлением до 2 знаков
UPDATE Books
SET price = ROUND(price * 0.9, 2);

-- Увеличить цену на фиксированную сумму
UPDATE Books
SET price = price + 2
LIMIT 1;

-- Несколько вычислений одновременно
UPDATE Books
SET price = ROUND(price * 0.9, 2),
    amount = amount - 1
WHERE amount > 0;
```

---

## 🟡 UPDATE CASE — условное обновление

Разные значения для разных условий в одном запросе:

```sql
-- Скидка зависит от количества товара
UPDATE Books
SET price = CASE
                WHEN amount < 5  THEN price * 0.9    -- меньше 5 шт → скидка 10%
                WHEN amount BETWEEN 5 AND 10 THEN price * 0.7  -- 5-10 шт → скидка 30%
                ELSE price * 0.5                      -- больше 10 шт → скидка 50%
            END;

-- Было:
-- | 1 | The Shining  | amount=1  | 7.99  | → 7.99 * 0.9 = 7.19
-- | 2 | Fight Club   | amount=19 | 9.99  | → 9.99 * 0.5 = 5.00
-- | 3 | Catcher...   | amount=11 | 3.49  | → 3.49 * 0.5 = 1.75
-- | 4 | Green Mile   | amount=6  | 15.99 | → 15.99 * 0.7 = 11.19
-- | 5 | Haunted      | amount=1  | 13.99 | → 13.99 * 0.9 = 12.59

-- CASE можно комбинировать с WHERE
UPDATE Books
SET price = CASE
                WHEN amount < 5 THEN price * 0.9
                ELSE price * 0.7
            END
WHERE price > 5;      -- только книги дороже 5 долларов
```

---

## 🟣 UPDATE JOIN — данные из другой таблицы

Два способа обновить на основе данных из другой таблицы:

**Способ 1 — подзапрос в WHERE:**

```sql
UPDATE Books
SET price = price * 0.5
WHERE author_id = (SELECT id
                   FROM Authors
                   WHERE author = 'Stephen King');
```

**Способ 2 — JOIN (более гибкий):**

```sql
-- INNER JOIN — обновить только совпадающие записи
UPDATE Books
INNER JOIN Authors ON Books.author_id = Authors.id
SET Books.price = Books.price * 0.5
WHERE Authors.author = 'Stephen King';

-- LEFT JOIN — обновить даже если нет совпадения
UPDATE Books
LEFT JOIN Authors ON Books.author_id = Authors.id
SET Books.price = CASE
                      WHEN Authors.author = 'Stephen King' THEN Books.price * 0.5
                      ELSE Books.price * 0.9
                  END;
```

**Ограничения при JOIN:**

```sql
-- ❌ Нельзя ORDER BY при JOIN
UPDATE Books INNER JOIN Authors ON Books.author_id = Authors.id
SET Books.price = Books.price * 0.5
WHERE Authors.author = 'Stephen King'
ORDER BY Books.id;   -- ERROR 1221: Incorrect usage of UPDATE and ORDER BY

-- ❌ Нельзя LIMIT при JOIN
UPDATE Books INNER JOIN Authors ON Books.author_id = Authors.id
SET Books.price = Books.price * 0.5
WHERE Authors.author = 'Stephen King'
LIMIT 1;             -- ERROR 1221: Incorrect usage of UPDATE and LIMIT
```

**Ограничение подзапроса:**

```sql
-- ❌ Нельзя обращаться к обновляемой таблице в подзапросе
UPDATE Books
SET price = price * 0.5
WHERE price = (SELECT MIN(price) FROM Books);
-- ERROR 1093: You can't specify target table 'Books' for update in FROM clause

-- ✅ Решение — вложить подзапрос ещё раз
UPDATE Books
SET price = price * 0.5
WHERE price = (SELECT min_price FROM
                   (SELECT MIN(price) AS min_price FROM Books) AS temp);
```

---

## ⚙️ ORDER BY и LIMIT

```sql
-- Обновить только первую запись (по умолчанию порядок не гарантирован)
UPDATE Books
SET price = price + 2
LIMIT 1;

-- Обновить 3 самые дорогие книги — дать скидку
UPDATE Books
SET price = price * 0.9
ORDER BY price DESC
LIMIT 3;

-- Обновить самую дешёвую книгу
UPDATE Books
SET price = 0.99
ORDER BY price ASC
LIMIT 1;
```

**ORDER BY при обновлении первичного ключа:**

```sql
-- ❌ Без ORDER BY — ошибка дублирования
UPDATE Books
SET id = id + 1;
-- ERROR 1062: Duplicate entry '2' for key 'Books.PRIMARY'
-- Причина: обновляем id=1 → 2, но id=2 уже есть!

-- ✅ С ORDER BY DESC — сначала обновляем последний
UPDATE Books
SET id = id + 1
ORDER BY id DESC;
-- 5 → 6, 4 → 5, 3 → 4, 2 → 3, 1 → 2 — нет конфликтов!
```

**Важное:** `OFFSET` при UPDATE использовать нельзя — только `LIMIT`.

---

## 🔧 IGNORE и DEFAULT

**IGNORE — не прерываться при ошибках:**

```sql
-- Без IGNORE — ошибка остановит весь запрос
UPDATE Books
SET id = id + 1;   -- может вызвать конфликт ключей

-- С IGNORE — конфликтные обновления пропускаются, остальные выполняются
UPDATE IGNORE Books
SET id = id + 1;

-- С IGNORE при попытке установить NULL в NOT NULL поле
UPDATE IGNORE Authors
SET id = NULL
LIMIT 1;
-- id не станет NULL — вместо этого примет значение по умолчанию (0 для INT)
```

**DEFAULT — сбросить на значение по умолчанию:**

```sql
-- Если amount имеет DEFAULT = 0
UPDATE Books
SET amount = DEFAULT;

-- Эквивалентно:
UPDATE Books
SET amount = 0;

-- Сброс нескольких полей
UPDATE Books
SET amount = DEFAULT,
    price = DEFAULT
WHERE id = 1;
```

**После UPDATE получить данные:**

```sql
-- UPDATE не возвращает таблицу — нужен отдельный SELECT
UPDATE Books
SET price = 9.99;

SELECT * FROM Books;   -- отдельный запрос через точку с запятой
```

---

## ⚡ Быстрые примеры

```sql
-- Установить цену всем
UPDATE Books SET price = 9.99;

-- Изменить одно поле по условию
UPDATE Books SET price = 1.99 WHERE amount = 1;

-- Скидка 10%
UPDATE Books SET price = ROUND(price * 0.9, 2);

-- Несколько полей
UPDATE Books SET price = 9.99, amount = 50 WHERE id = 1;

-- CASE
UPDATE Books
SET price = CASE
    WHEN amount < 5 THEN price * 0.9
    ELSE price * 0.5
END;

-- JOIN
UPDATE Books
INNER JOIN Authors ON Books.author_id = Authors.id
SET Books.price = Books.price * 0.5
WHERE Authors.author = 'Stephen King';

-- ORDER BY + LIMIT
UPDATE Books SET price = price * 0.9 ORDER BY price DESC LIMIT 3;

-- Сдвиг первичного ключа без конфликта
UPDATE Books SET id = id + 1 ORDER BY id DESC;

-- DEFAULT
UPDATE Books SET amount = DEFAULT;
```

---

## ⚠️ Частые ошибки

**❌ UPDATE без WHERE — обновит ВСЕ записи:**
```sql
UPDATE Books SET price = 0;   -- ❌ все книги стали бесплатными!
UPDATE Books SET price = 0 WHERE id = 1;  -- ✅ только одна книга
```

**❌ ORDER BY + LIMIT при JOIN:**
```sql
UPDATE Books INNER JOIN Authors ON Books.author_id = Authors.id
SET Books.price = 0
ORDER BY Books.id LIMIT 1;  -- ❌ ERROR 1221
-- При JOIN нельзя ORDER BY и LIMIT
```

**❌ Подзапрос к обновляемой таблице:**
```sql
UPDATE Books
SET price = 0
WHERE price = (SELECT MIN(price) FROM Books);  -- ❌ ERROR 1093
-- Оберни подзапрос ещё раз в подзапрос
```

**❌ Сдвиг первичного ключа без ORDER BY:**
```sql
UPDATE Books SET id = id + 1;          -- ❌ ERROR 1062 — конфликт ключей
UPDATE Books SET id = id + 1 ORDER BY id DESC;  -- ✅
```

---

## ✅ Главные правила

✅ Без `WHERE` — обновятся все записи, будь осторожен  
✅ Несколько полей — через запятую в `SET`  
✅ Вычисления в SET — `price = price * 0.9`, `ROUND(price * 0.9, 2)`  
✅ `CASE` в SET — разные значения для разных условий  
✅ При JOIN — нельзя `ORDER BY` и `LIMIT`  
✅ В подзапросе — нельзя обращаться к обновляемой таблице  
✅ При сдвиге первичного ключа — обязательно `ORDER BY DESC`  
✅ `UPDATE IGNORE` — пропускать конфликтные обновления  
✅ `DEFAULT` — сбросить поле на значение по умолчанию  
✅ `UPDATE` не возвращает таблицу — нужен отдельный `SELECT`  

---

## 🔗 Связанные темы

- [[02 — ➕ INSERT — добавление данных]]
- [[04 — 🗑️ DELETE — удаление данных]]
- [[01 — 🏗 CREATE ALTER DROP]]

---

#sql/dml #update #администрирование
