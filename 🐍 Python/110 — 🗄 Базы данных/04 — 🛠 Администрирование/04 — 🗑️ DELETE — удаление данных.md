---
tags: [sql, dml, delete, truncate, администрирование]
difficulty: intermediate
---

# 🗑️ DELETE — удаление данных — памятка

> `DELETE` удаляет записи из таблицы. Можно удалить все записи или отдельные через `WHERE`, использовать данные из других таблиц через подзапрос или `JOIN`. `TRUNCATE` — быстрая очистка всей таблицы.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Удаление всех записей|Удаление всех записей]]
- [[#🔵 Удаление отдельных записей|Удаление отдельных записей]]
- [[#🔴 DELETE JOIN — данные из другой таблицы|DELETE JOIN]]
- [[#🟡 ORDER BY и LIMIT|ORDER BY и LIMIT]]
- [[#🟣 Удаление зависимых данных|Зависимые данные]]
- [[#⚙️ IGNORE и TRUNCATE|IGNORE и TRUNCATE]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `DELETE FROM t` | удалить все записи |
| `DELETE FROM t WHERE cond` | удалить отдельные записи |
| `DELETE FROM t WHERE col = (SELECT ...)` | удалить через подзапрос |
| `DELETE FROM t USING t JOIN t2 ON ... WHERE ...` | удалить через JOIN (синтаксис 1) |
| `DELETE t FROM t JOIN t2 ON ... WHERE ...` | удалить через JOIN (синтаксис 2) |
| `DELETE FROM t ORDER BY col LIMIT n` | удалить N записей в порядке сортировки |
| `DELETE IGNORE FROM t WHERE ...` | не прерываться при ошибках |
| `TRUNCATE t` | очистить всю таблицу (быстро, необратимо) |

---

## 📊 Общая схема

```
DELETE FROM таблица
WHERE условие          ← без WHERE — удалятся ВСЕ записи!
ORDER BY поле          ← порядок удаления
LIMIT n;               ← ограничение количества

DELETE не удаляет саму таблицу — только записи!

Два синтаксиса для DELETE JOIN:
├── DELETE FROM Books USING Books JOIN Authors ON ... WHERE ...
└── DELETE Books FROM Books JOIN Authors ON ... WHERE ...
    ↑ явно указываем из какой таблицы удалять
```

---

## 🟢 Удаление всех записей

```sql
-- Удалить все записи таблицы
DELETE FROM Books;

-- Таблица остаётся пустой, но не удаляется
-- AUTO_INCREMENT не сбрасывается (следующий id продолжит с последнего)
```

**Важное:** `DELETE FROM Books` без WHERE — очень опасная операция. Всегда проверяй WHERE перед выполнением.

---

## 🔵 Удаление отдельных записей

```sql
-- Удалить по id
DELETE FROM Books
WHERE id = 2;

-- Удалить по значению поля
DELETE FROM Books
WHERE title = 'Fight Club';

-- Удалить по нескольким условиям
DELETE FROM Books
WHERE amount = 0
  AND price < 1;

-- Удалить через подзапрос — данные из другой таблицы
DELETE FROM Books
WHERE author_id = (SELECT id
                   FROM Authors
                   WHERE author = 'Chuck Palahniuk');

-- Было:
-- | 1 | The Shining            | 1 |
-- | 2 | Fight Club             | 2 |  ← удалена
-- | 3 | The Catcher in the Rye | 3 |
-- | 4 | The Green Mile         | 1 |
-- | 5 | Haunted                | 2 |  ← удалена
```

---

## 🔴 DELETE JOIN — данные из другой таблицы

Два равнозначных синтаксиса:

**Синтаксис 1 — через USING:**

```sql
DELETE FROM Books
USING Books INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'Chuck Palahniuk';

-- Читается как: "удали из Books, используя JOIN Books и Authors,
--                где автор — Chuck Palahniuk"
```

**Синтаксис 2 — имя таблицы после DELETE:**

```sql
DELETE Books
FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'Chuck Palahniuk';

-- Читается как: "удали из Books, из JOIN Books и Authors,
--                где автор — Chuck Palahniuk"
```

Оба дают одинаковый результат. Второй синтаксис нагляднее показывает из какой таблицы удаляем.

**Как работает JOIN при удалении:**

```sql
-- JOIN создаёт расширенную таблицу:
-- | id | title        | author_id | id | author          |
-- |  1 | The Shining  | 1         |  1 | Stephen King    |
-- |  2 | Fight Club   | 2         |  2 | Chuck Palahniuk | ← удалить
-- |  3 | Catcher...   | 3         |  3 | Jerome Salinger |
-- |  4 | Green Mile   | 1         |  1 | Stephen King    |
-- |  5 | Haunted      | 2         |  2 | Chuck Palahniuk | ← удалить
--
-- Удаляются только записи из Books где Authors.author = 'Chuck Palahniuk'
```

**Ограничение при JOIN:**

```sql
-- ❌ Нельзя ORDER BY при JOIN
DELETE FROM Books
USING Books INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'Chuck Palahniuk'
ORDER BY Books.id;   -- ERROR 1064: синтаксическая ошибка
```

---

## 🟡 ORDER BY и LIMIT

```sql
-- Удалить последнюю добавленную книгу
DELETE FROM Books
ORDER BY id DESC
LIMIT 1;

-- Удалить самую дешёвую книгу
DELETE FROM Books
ORDER BY price ASC
LIMIT 1;

-- Удалить 3 самые старые записи
DELETE FROM Books
ORDER BY id ASC
LIMIT 3;

-- Удалить книги с нулевым количеством, начиная с самых дорогих
DELETE FROM Books
WHERE amount = 0
ORDER BY price DESC
LIMIT 5;
```

---

## 🟣 Удаление зависимых данных

Если между таблицами есть внешний ключ — СУБД не даст удалить родительскую запись пока есть зависимые дочерние:

```sql
-- ❌ Нельзя удалить автора если у него есть книги
DELETE FROM Authors
WHERE author = 'Jerome Salinger';
-- ERROR 1451: Cannot delete or update a parent row:
--             a foreign key constraint fails

-- ✅ Сначала удаляем зависимые данные (книги), потом автора
DELETE FROM Books
WHERE author_id = (SELECT id FROM Authors WHERE author = 'Jerome Salinger');

DELETE FROM Authors
WHERE author = 'Jerome Salinger';
```

**Порядок важен:**

```
Правило: сначала удаляй из дочерних таблиц (Books),
         потом из родительских (Authors)

Authors (родитель)          Books (дочерняя)
    id ◄────────── author_id
    │
    └── нельзя удалить Authors.id пока есть Books.author_id ссылающийся на него
```

**СУБД следит за целостностью только если внешний ключ определён явно.** Если связь между таблицами не прописана через `FOREIGN KEY` — СУБД не будет мешать удалению.

---

## ⚙️ IGNORE и TRUNCATE

**IGNORE — игнорировать ошибки:**

```sql
-- Без IGNORE — ошибка остановит удаление
DELETE FROM Authors
WHERE author = 'Jerome Salinger';
-- ERROR 1451: нарушение внешнего ключа

-- С IGNORE — конфликтное удаление пропускается, запрос выполнится
DELETE IGNORE FROM Authors
WHERE author = 'Jerome Salinger';
-- Строка осталась в таблице, ошибка проигнорирована
```

**TRUNCATE — быстрая очистка всей таблицы:**

```sql
-- Удалить все записи быстро
TRUNCATE Books;
-- или
TRUNCATE TABLE Books;
```

**Сравнение DELETE vs TRUNCATE:**

| Характеристика | `DELETE FROM t` | `TRUNCATE t` |
|---|---|---|
| `WHERE` | ✅ можно | ❌ нет |
| Скорость | медленнее | быстрее |
| Журнал транзакций | записывается | не записывается |
| Можно откатить | ✅ да | ❌ нет |
| Сбрасывает AUTO_INCREMENT | ❌ нет | ✅ да |
| Внешние ключи | проверяет | проверяет |
| Когда использовать | обычное удаление | полная очистка таблицы |

---

## ⚡ Быстрые примеры

```sql
-- Удалить все
DELETE FROM Books;

-- По условию
DELETE FROM Books WHERE id = 2;
DELETE FROM Books WHERE title = 'Fight Club';
DELETE FROM Books WHERE amount = 0 AND price < 1;

-- Через подзапрос
DELETE FROM Books
WHERE author_id = (SELECT id FROM Authors WHERE author = 'Chuck Palahniuk');

-- Через JOIN — синтаксис USING
DELETE FROM Books
USING Books INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'Chuck Palahniuk';

-- Через JOIN — синтаксис DELETE table FROM
DELETE Books
FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'Chuck Palahniuk';

-- ORDER BY + LIMIT
DELETE FROM Books ORDER BY id DESC LIMIT 1;
DELETE FROM Books ORDER BY price ASC LIMIT 3;

-- Зависимые данные — сначала дочерние
DELETE FROM Books WHERE author_id = (SELECT id FROM Authors WHERE author = 'J.D.S.');
DELETE FROM Authors WHERE author = 'J.D. Salinger';

-- IGNORE
DELETE IGNORE FROM Authors WHERE author = 'Jerome Salinger';

-- TRUNCATE
TRUNCATE Books;
```

---

## ⚠️ Частые ошибки

**❌ DELETE без WHERE — удалит все записи:**
```sql
DELETE FROM Books;         -- ❌ все книги удалены!
DELETE FROM Books WHERE id = 1;  -- ✅ только одна
```

**❌ Удаление родителя при наличии дочерних записей:**
```sql
DELETE FROM Authors WHERE id = 1;
-- ❌ ERROR 1451 если в Books есть записи с author_id = 1
-- Сначала удали книги этого автора, потом автора
```

**❌ ORDER BY при DELETE JOIN:**
```sql
DELETE FROM Books
USING Books JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.author = 'King'
ORDER BY Books.id;   -- ❌ синтаксическая ошибка при JOIN
```

**❌ Думаешь что TRUNCATE можно откатить:**
```sql
BEGIN;
TRUNCATE Books;   -- ❌ не откатится! Данные потеряны
ROLLBACK;         -- не поможет

-- Используй DELETE если нужна возможность отката:
BEGIN;
DELETE FROM Books;
ROLLBACK;         -- ✅ данные восстановлены
```

**❌ Путаешь DELETE и DROP:**
```sql
DELETE FROM Books;   -- удаляет записи, таблица остаётся
DROP TABLE Books;    -- удаляет саму таблицу!
```

---

## ✅ Главные правила

✅ Без `WHERE` — удалятся все записи, будь осторожен  
✅ `DELETE` не удаляет таблицу — только записи  
✅ При наличии внешнего ключа — сначала удаляй дочерние записи, потом родительские  
✅ `ORDER BY` + `LIMIT` — удалить N записей в определённом порядке  
✅ При `DELETE JOIN` — нельзя `ORDER BY`  
✅ `TRUNCATE` — быстро, но необратимо и без журнала  
✅ `TRUNCATE` сбрасывает `AUTO_INCREMENT`, `DELETE` — нет  
✅ `DELETE IGNORE` — пропускать ошибки внешних ключей  

---

## 🔗 Связанные темы

- [[02 — ➕ INSERT — добавление данных]]
- [[03 — ✏️ UPDATE — обновление данных]]
- [[01 — 🏗 CREATE ALTER DROP]]

---

#sql/dml #delete #truncate #администрирование
