---
tags: [sql, ddl, create, alter, drop, администрирование]
difficulty: intermediate
---

# 🏗 CREATE, ALTER, DROP — управление таблицами — памятка

> DDL-операторы управляют структурой таблиц: `CREATE` создаёт, `ALTER` изменяет, `DROP` удаляет. Выполняются немедленно и необратимо — не требуют и не поддерживают `COMMIT/ROLLBACK`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 CREATE TABLE — создание таблицы|CREATE TABLE]]
- [[#🔵 Ограничения и ключевые слова|Ограничения]]
- [[#🔴 DROP и RENAME|DROP и RENAME]]
- [[#🟡 ALTER TABLE — изменение таблицы|ALTER TABLE]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает |
|---|---|
| `CREATE TABLE t (...)` | создать таблицу |
| `CREATE TABLE IF NOT EXISTS t (...)` | создать если не существует |
| `DROP TABLE t` | удалить таблицу |
| `DROP TABLE IF EXISTS t` | удалить если существует |
| `RENAME TABLE t TO new_t` | переименовать таблицу |
| `ALTER TABLE t ADD COLUMN col ...` | добавить поле |
| `ALTER TABLE t DROP COLUMN col` | удалить поле |
| `ALTER TABLE t RENAME COLUMN col TO new_col` | переименовать поле |
| `ALTER TABLE t MODIFY COLUMN col ...` | изменить тип/ограничения поля |
| `ALTER TABLE t ADD PRIMARY KEY (col)` | добавить первичный ключ |
| `ALTER TABLE t ADD FOREIGN KEY ...` | добавить внешний ключ |
| `ALTER TABLE t ADD CHECK (...)` | добавить CHECK |
| `SHOW CREATE TABLE t` | показать SQL создания таблицы |

---

## 🟢 CREATE TABLE — создание таблицы

```sql
-- Полный пример с ограничениями
CREATE TABLE IF NOT EXISTS Books (
    id          INT PRIMARY KEY AUTO_INCREMENT,
    title       VARCHAR(40) NOT NULL UNIQUE,
    author      VARCHAR(40) DEFAULT 'Unknown',
    price       DECIMAL(10, 2) CHECK (price >= 0),
    created_at  DATE DEFAULT (CURDATE()),
    author_id   INT,
    FOREIGN KEY (author_id) REFERENCES Authors(id)
        ON UPDATE CASCADE
        ON DELETE SET NULL,
    CONSTRAINT price_positive CHECK (price > 0)
);
```

**Минимальная таблица:**

```sql
CREATE TABLE Users (
    id   INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);
```

**IF NOT EXISTS** — не вызывает ошибку если таблица уже существует:

```sql
CREATE TABLE Books (...);              -- ❌ ошибка если Books уже есть
CREATE TABLE IF NOT EXISTS Books (...); -- ✅ просто пропустит если уже есть
```

---

## 🔵 Ограничения и ключевые слова

| Ключевое слово | Что делает |
|---|---|
| `PRIMARY KEY` | первичный ключ — уникальный идентификатор строки |
| `AUTO_INCREMENT` | автоматическое увеличение (только для INT PRIMARY KEY) |
| `NOT NULL` | поле не может быть NULL |
| `UNIQUE` | все значения в поле уникальны |
| `DEFAULT value` | значение по умолчанию |
| `CHECK (expr)` | проверка условия при вставке/обновлении |
| `FOREIGN KEY (col) REFERENCES t(col)` | внешний ключ — связь с другой таблицей |

**ON UPDATE / ON DELETE — поведение внешнего ключа:**

| Опция | Что происходит при изменении/удалении родительской записи |
|---|---|
| `CASCADE` | каскадно обновляет/удаляет дочерние записи |
| `SET NULL` | устанавливает NULL в дочерней таблице |
| `RESTRICT` | запрещает изменение/удаление (по умолчанию) |
| `NO ACTION` | то же что RESTRICT |
| `SET DEFAULT` | устанавливает значение по умолчанию |

```sql
FOREIGN KEY (author_id) REFERENCES Authors(id)
    ON UPDATE CASCADE   -- если id автора изменился — обновится и в Books
    ON DELETE SET NULL  -- если автора удалили — author_id станет NULL
```

**CONSTRAINT — именованное ограничение:**

```sql
-- Без имени — MySQL генерирует имя автоматически
CHECK (price > 0)

-- С именем — удобно для последующего удаления
CONSTRAINT price_positive CHECK (price > 0)
```

---

## 🔴 DROP и RENAME

```sql
-- Удалить таблицу (необратимо!)
DROP TABLE Books;

-- Удалить если существует — без ошибки
DROP TABLE IF EXISTS Books;

-- Удалить несколько таблиц за раз
DROP TABLE IF EXISTS Books, Authors, Publishers;

-- Переименовать таблицу
RENAME TABLE Books TO Library;

-- Переименовать несколько
RENAME TABLE Books TO Library, Authors TO Writers;
```

---

## 🟡 ALTER TABLE — изменение таблицы

### Работа с полями

```sql
-- Добавить поле (в конец по умолчанию)
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0);

-- Добавить первым
ALTER TABLE Books
ADD COLUMN release_year INT FIRST;

-- Добавить после определённого поля
ALTER TABLE Books
ADD COLUMN release_year INT AFTER title;

-- Удалить поле
ALTER TABLE Books
DROP COLUMN author;

-- Переименовать поле
ALTER TABLE Books
RENAME COLUMN author TO writer;

-- Изменить тип или ограничения (MODIFY)
ALTER TABLE Books
MODIFY COLUMN title VARCHAR(60) NOT NULL;

-- Установить значение по умолчанию
ALTER TABLE Books
ALTER COLUMN title SET DEFAULT 'Untitled';

-- Удалить значение по умолчанию
ALTER TABLE Books
ALTER COLUMN title DROP DEFAULT;
```

### Работа с первичным ключом

```sql
-- Добавить первичный ключ
ALTER TABLE Books
ADD PRIMARY KEY (id);

-- Удалить первичный ключ
ALTER TABLE Books
DROP PRIMARY KEY;
```

### Работа с внешним ключом

```sql
-- Добавить поле для внешнего ключа
ALTER TABLE Books
ADD COLUMN publisher_id INT;

-- Добавить внешний ключ
ALTER TABLE Books
ADD FOREIGN KEY (publisher_id) REFERENCES Publishers(id);

-- Добавить именованный внешний ключ
ALTER TABLE Books
ADD CONSTRAINT fk_publisher
FOREIGN KEY (publisher_id) REFERENCES Publishers(id)
ON DELETE CASCADE;

-- Узнать имя внешнего ключа
SHOW CREATE TABLE Books;

-- Удалить внешний ключ
ALTER TABLE Books
DROP FOREIGN KEY fk_publisher;
```

### Работа с CHECK

```sql
-- Добавить CHECK без имени
ALTER TABLE Books
ADD CHECK (title != '');

-- Добавить CHECK с именем
ALTER TABLE Books
ADD CONSTRAINT title_not_empty CHECK (title != '');

-- Узнать имя сгенерированного CHECK
SHOW CREATE TABLE Books;

-- Удалить CHECK по имени
ALTER TABLE Books
DROP CONSTRAINT title_not_empty;

-- Удалить несколько CHECK
ALTER TABLE Books
DROP CHECK books_chk_1,
DROP CHECK books_chk_2;
```

### Несколько изменений за один ALTER

```sql
-- Всё за один запрос
ALTER TABLE Books
ADD COLUMN release_year INT AFTER title,
MODIFY COLUMN price DECIMAL(12, 2),
DROP COLUMN old_field,
ADD INDEX idx_price (price);
```

---

## ⚡ Быстрые примеры

```sql
-- Создать таблицу
CREATE TABLE IF NOT EXISTS Authors (
    id     INT PRIMARY KEY AUTO_INCREMENT,
    name   VARCHAR(100) NOT NULL,
    email  VARCHAR(100) UNIQUE,
    rating DECIMAL(3, 1) DEFAULT 0 CHECK (rating BETWEEN 0 AND 10)
);

-- Удалить
DROP TABLE IF EXISTS Authors;

-- Переименовать
RENAME TABLE Authors TO Writers;

-- Добавить поле
ALTER TABLE Books ADD COLUMN isbn VARCHAR(20) UNIQUE AFTER title;

-- Удалить поле
ALTER TABLE Books DROP COLUMN isbn;

-- Изменить тип
ALTER TABLE Books MODIFY COLUMN price DECIMAL(12, 2) DEFAULT 0;

-- Переименовать поле
ALTER TABLE Books RENAME COLUMN price TO cost;

-- Добавить внешний ключ
ALTER TABLE Books
ADD CONSTRAINT fk_author
FOREIGN KEY (author_id) REFERENCES Authors(id) ON DELETE CASCADE;

-- Удалить внешний ключ
ALTER TABLE Books DROP FOREIGN KEY fk_author;

-- Несколько изменений
ALTER TABLE Books
ADD COLUMN pages INT,
ADD COLUMN genre VARCHAR(50),
MODIFY COLUMN title VARCHAR(200);
```

---

## ⚠️ Частые ошибки

**❌ DROP без IF EXISTS — ошибка если таблицы нет:**
```sql
DROP TABLE Books;              -- ❌ ошибка если Books не существует
DROP TABLE IF EXISTS Books;    -- ✅ без ошибки
```

**❌ Удаление поля с внешним ключом:**
```sql
ALTER TABLE Books DROP COLUMN author_id;
-- ❌ ERROR: Cannot drop column — used in FK constraint
-- Сначала удали внешний ключ:
ALTER TABLE Books DROP FOREIGN KEY fk_author;
ALTER TABLE Books DROP COLUMN author_id;  -- ✅
```

**❌ Удаление внешнего ключа без знания его имени:**
```sql
ALTER TABLE Books DROP FOREIGN KEY author_id;  -- ❌ может не совпадать с именем
SHOW CREATE TABLE Books;                       -- ✅ узнай точное имя
```

**❌ Забыл IF NOT EXISTS при повторном создании:**
```sql
CREATE TABLE Books (...);  -- ❌ ошибка при втором запуске скрипта
CREATE TABLE IF NOT EXISTS Books (...);  -- ✅
```

---

## ✅ Главные правила

✅ `IF NOT EXISTS` / `IF EXISTS` — защита от ошибок при повторном запуске  
✅ `AUTO_INCREMENT` — только для `INT PRIMARY KEY`  
✅ `CONSTRAINT имя` — именуй ограничения для удобного удаления  
✅ `SHOW CREATE TABLE t` — узнать имена всех ограничений и индексов  
✅ `ON DELETE CASCADE` — каскадное удаление дочерних записей  
✅ `ON DELETE SET NULL` — внешний ключ станет NULL при удалении родителя  
✅ Несколько изменений в одном `ALTER TABLE` — через запятую  
✅ DDL необратимо — нет `ROLLBACK` для `DROP TABLE`  

---

## 🔗 Связанные темы

- [[02 — ➕ INSERT — добавление данных]]
- [[03 — ✏️ UPDATE — обновление данных]]
- [[04 — 🗑️ DELETE — удаление данных]]
- [[05 — 🚀 Индексы]]

---

#sql/ddl #create #alter #drop #администрирование
