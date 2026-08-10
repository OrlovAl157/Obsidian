---
tags: [sql, dml, insert, администрирование]
difficulty: intermediate
---

# ➕ INSERT — добавление данных — памятка

> `INSERT` добавляет новые записи в таблицу. Можно добавить одну запись или несколько за один запрос, использовать вычисляемые значения и данные из других таблиц.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Базовый синтаксис|Базовый синтаксис]]
- [[#🔵 Добавление нескольких записей|Несколько записей]]
- [[#🔴 Вычисляемые значения|Вычисляемые значения]]
- [[#🟡 INSERT SET — альтернативный синтаксис|INSERT SET]]
- [[#🟣 INSERT SELECT — данные из другой таблицы|INSERT SELECT]]
- [[#⚙️ IGNORE и REPLACE|IGNORE и REPLACE]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `INSERT INTO t VALUES (...)` | добавить запись без указания полей |
| `INSERT INTO t (col1, col2) VALUES (...)` | добавить запись с явным указанием полей ✅ |
| `INSERT INTO t (col1) VALUES (...),(...)` | добавить несколько записей |
| `INSERT INTO t SET col1=val1` | добавить одну запись через SET |
| `INSERT INTO t SELECT ... FROM t2` | добавить данные из другой таблицы |
| `INSERT IGNORE INTO t VALUES (...)` | игнорировать ошибки при вставке |
| `REPLACE INTO t VALUES (...)` | вставить или заменить при конфликте ключа |

---

## 📊 Общая схема

```
INSERT INTO таблица (поле1, поле2, поле3)
VALUES (значение1, значение2, значение3);

Три способа задать значение:
├── Константа:          'Stephen King', 9.99, 1
├── Вычисление:         9.99 * 0.6, ROUND(15.99 * 0.9)
└── NULL / DEFAULT:     NULL, DEFAULT

Два способа указать поля:
├── Без полей:  VALUES (1, 'Title', 'Author', 9.99)
│               ↑ опасно — зависит от порядка столбцов в таблице
└── С полями:   (id, title, author, price) VALUES (...)
                ↑ рекомендуется всегда
```

---

## 🟢 Базовый синтаксис

**Без указания полей — опасный способ:**

```sql
-- Порядок значений должен совпадать с порядком полей в таблице
INSERT INTO Books
VALUES (6, 'Animal Farm', 'George Orwell', 9.99);

-- Если поля нет значения — NULL
INSERT INTO Books
VALUES (6, 'Animal Farm', 'George Orwell', NULL);
```

**С явным указанием полей — рекомендуется:**

```sql
-- Порядок полей можно менять
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', 9.99);

-- Порядок не важен — главное чтобы поля и значения совпадали
INSERT INTO Books (id, price, author, title)
VALUES (6, 9.99, 'George Orwell', 'Animal Farm');
```

**Поле с DEFAULT можно пропустить:**

```sql
-- Если price имеет DEFAULT = 0 — можно не указывать
INSERT INTO Books (id, title, author)
VALUES (6, 'Animal Farm', 'George Orwell');

-- Результат: price = 0 (значение по умолчанию)
```

**Явный DEFAULT как значение:**

```sql
-- Можно явно указать DEFAULT вместо конкретного значения
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', DEFAULT);

-- Результат: price = 0 (то же самое что пропустить поле)
```

---

## 🔵 Добавление нескольких записей

```sql
-- Несколько записей в одном запросе — через запятую
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', NULL),
       (7, 'Lord of the Flies', 'William Golding', 5.99),
       (8, '1984', 'George Orwell', 8.99);

-- Результат: три записи добавлены за один запрос
-- +----+-------------------+-----------------+-------+
-- | id | title             | author          | price |
-- +----+-------------------+-----------------+-------+
-- |  6 | Animal Farm       | George Orwell   | NULL  |
-- |  7 | Lord of the Flies | William Golding |  5.99 |
-- |  8 | 1984              | George Orwell   |  8.99 |
-- +----+-------------------+-----------------+-------+
```

Это быстрее чем несколько отдельных INSERT — один запрос к БД вместо трёх.

---

## 🔴 Вычисляемые значения

В VALUES можно использовать выражения и функции:

```sql
-- Скидка 40% при вставке
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', 9.99 * 0.6);
-- price = 5.994

-- Скидка 40% с округлением
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', ROUND(9.99 * 0.6));
-- price = 6.00

-- Текущая дата
INSERT INTO Books (id, title, author, price, added_at)
VALUES (6, 'Animal Farm', 'George Orwell', 9.99, CURDATE());

-- Несколько вычислений
INSERT INTO Books (id, title, author, price, discount_price)
VALUES (6, 'Animal Farm', 'George Orwell', 9.99, ROUND(9.99 * 0.7, 2));
```

---

## 🟡 INSERT SET — альтернативный синтаксис

Поля и значения рядом через `=` — как в UPDATE:

```sql
INSERT INTO Books
SET id = 6,
    title = 'Animal Farm',
    author = 'George Orwell',
    price = 9.99;
```

**Ограничение:** только одна запись за раз — несколько через SET добавить нельзя.

---

## 🟣 INSERT SELECT — данные из другой таблицы

```sql
-- Перенести все записи из NewBooks в Books
INSERT INTO Books (id, title, author, price)
SELECT id, title, author, price
FROM NewBooks;

-- С фильтрацией
INSERT INTO Books (id, title, author, price)
SELECT id, title, author, price
FROM NewBooks
WHERE price > 5;

-- С вычислением при вставке
INSERT INTO Books (id, title, author, price)
SELECT id, title, author, ROUND(price * 0.9)
FROM NewBooks;
```

**Важное:** количество полей в SELECT должно совпадать с количеством полей после INSERT. Имена не важны — сопоставление по порядку.

**Конфликт первичного ключа при переносе:**

```sql
-- Если id в NewBooks совпадают с id в Books — ошибка!
-- ERROR 1062: Duplicate entry '1' for key 'Books.PRIMARY'

-- Решение: сдвинуть id на максимальный id из Books
INSERT INTO Books (id, title, author, price)
SELECT (SELECT MAX(id) FROM Books) + NewBooks.id,
       title, author, price
FROM NewBooks;

-- Если MAX(id) = 5, а NewBooks.id = 1 → новый id = 6
-- Если MAX(id) = 5, а NewBooks.id = 2 → новый id = 7
```

---

## ⚙️ IGNORE и REPLACE

**IGNORE — игнорировать ошибки:**

```sql
-- Без IGNORE — ошибка при совпадении PRIMARY KEY
INSERT INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),   -- id=1 уже есть → ошибка!
       (6, 'Lord of the Flies', 'William Golding', 5.99);
-- ERROR 1062: Duplicate entry '1' for key 'Books.PRIMARY'

-- С IGNORE — конфликтная запись пропускается, остальные добавляются
INSERT IGNORE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),   -- пропущена (id=1 есть)
       (6, 'Lord of the Flies', 'William Golding', 5.99);  -- добавлена ✅
```

**REPLACE — вставить или заменить:**

```sql
-- Если id совпадает — старая запись удаляется, новая добавляется
REPLACE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),    -- заменит запись с id=1
       (6, 'Lord of the Flies', 'William Golding', 5.99);  -- добавит новую

-- Было: id=1, 'The Shining', 'Stephen King', 7.99
-- Стало: id=1, 'Animal Farm', 'George Orwell', 9.99
```

**Сравнение IGNORE vs REPLACE:**

| | `INSERT IGNORE` | `REPLACE` |
|---|---|---|
| При конфликте ключа | пропускает запись | удаляет старую, добавляет новую |
| Старые данные | сохраняются | теряются! |
| Когда использовать | «не трогай если есть» | «перезапиши если есть» |

---

## 💡 Практические замечания

- Всегда указывай поля явно — `INSERT INTO t (col1, col2) VALUES (...)` — запрос будет работать даже если структура таблицы изменится
- `INSERT IGNORE` — удобен при массовой загрузке данных когда дубликаты возможны
- `REPLACE` — осторожно, он удаляет и вставляет заново, счётчик `AUTO_INCREMENT` растёт
- `INSERT ... SELECT` — полноценный SELECT: может иметь WHERE, JOIN, ORDER BY, LIMIT
- На практике поле `id` чаще делают `AUTO_INCREMENT` — тогда не нужно думать об уникальности

---

## ⚠️ Частые ошибки

**❌ Вставка без указания полей — риск при изменении таблицы:**
```sql
INSERT INTO Books VALUES (6, 'Animal Farm', 'George Orwell', 9.99);
-- ❌ если добавят новое поле — запрос сломается!

INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell', 9.99);  -- ✅
```

**❌ Несовпадение количества полей и значений:**
```sql
INSERT INTO Books (id, title, author, price)
VALUES (6, 'Animal Farm', 'George Orwell');  -- ❌ 4 поля, 3 значения
-- ERROR: Column count doesn't match value count
```

**❌ Конфликт PRIMARY KEY без IGNORE:**
```sql
INSERT INTO Books (id, title, author, price)
VALUES (1, 'New Book', 'Author', 9.99);  -- ❌ id=1 уже есть
-- ERROR 1062: Duplicate entry '1' for key 'Books.PRIMARY'

INSERT IGNORE INTO Books (id, title, author, price)
VALUES (1, 'New Book', 'Author', 9.99);  -- ✅ пропустит конфликтную запись
```

**❌ INSERT SET для нескольких записей:**
```sql
INSERT INTO Books
SET id=6, title='A', author='B', price=1.0,
    id=7, title='C', author='D', price=2.0;  -- ❌ нельзя!

-- Используй VALUES для нескольких записей:
INSERT INTO Books (id, title, author, price)
VALUES (6, 'A', 'B', 1.0),
       (7, 'C', 'D', 2.0);  -- ✅
```

---

## ✅ Главные правила

✅ Всегда указывай поля явно — `INSERT INTO t (col1, col2) VALUES (...)`  
✅ Несколько записей — перечисляй через запятую после VALUES  
✅ Вычисления прямо в VALUES — `ROUND(price * 0.9)`, `CURDATE()`  
✅ `INSERT IGNORE` — пропустить конфликтные записи  
✅ `REPLACE` — заменить при конфликте (удаляет старую запись!)  
✅ `INSERT SET` — только одна запись  
✅ `INSERT SELECT` — данные из другой таблицы, количество полей должно совпадать  
✅ При конфликте id в INSERT SELECT — используй `MAX(id) + id`  

---

## 🔗 Связанные темы

- [[01 — 🏗 CREATE ALTER DROP]]
- [[03 — ✏️ UPDATE — обновление данных]]
- [[04 — 🗑️ DELETE — удаление данных]]

---

#sql/dml #insert #администрирование
