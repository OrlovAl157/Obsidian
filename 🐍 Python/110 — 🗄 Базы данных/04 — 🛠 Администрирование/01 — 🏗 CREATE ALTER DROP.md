---
tags: [sql, ddl, create, alter, drop, таблицы]
---

# 🏗 CREATE, ALTER, DROP — управление таблицами

## Создание таблиц (CREATE TABLE)

```sql
CREATE TABLE IF NOT EXISTS Books (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(40) NOT NULL UNIQUE,
    author VARCHAR(40) DEFAULT 'Unknown',
    price DECIMAL(10,2) CHECK (price >= 0),
    created_at DATE DEFAULT (CURDATE()),
    PRIMARY KEY (id, title),
    FOREIGN KEY (author_id) REFERENCES Authors(id)
        ON UPDATE CASCADE
        ON DELETE SET NULL,
    CONSTRAINT price_check CHECK (price > 0)
);
```

## Ключевые слова при создании

| Ключевое слово | Описание |
|----------------|----------|
| `PRIMARY KEY` | первичный ключ |
| `AUTO_INCREMENT` | автоувеличение, обычно для `INT PRIMARY KEY` |
| `NOT NULL` | поле не может быть `NULL` |
| `UNIQUE` | все значения в поле уникальны |
| `DEFAULT` | значение по умолчанию |
| `CHECK` | проверка условия |
| `FOREIGN KEY` | внешний ключ |
| `REFERENCES` | ссылается на таблицу и поле |
| `ON UPDATE` | что делать при обновлении родительской записи |
| `ON DELETE` | что делать при удалении родительской записи |

## ON UPDATE / ON DELETE опции

| Опция | Значение |
|-------|----------|
| `CASCADE` | каскадное изменение или удаление |
| `SET NULL` | установить `NULL` |
| `RESTRICT` | ограничивать, стоит по умолчанию |

## Удаление таблиц (DROP TABLE)

```sql
DROP TABLE IF EXISTS Books, Authors, Publishers;
```

## Переименование таблицы (RENAME TABLE)

```sql
RENAME TABLE current_name TO new_name;
```

## Обновление таблицы (ALTER TABLE)

### Удаление поля

```sql
-- узнать имя ограничения
SHOW CREATE TABLE Books;

ALTER TABLE Books
DROP COLUMN author;
```

### Переименование поля

```sql
ALTER TABLE Books
RENAME COLUMN author TO writer;
```

### Добавление поля

```sql
-- просто добавить
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0);

-- добавить первым
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0) FIRST;

-- добавить после определённого поля
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0) AFTER title;
```

### Обновление поля

```sql
-- изменить тип или ограничения
ALTER TABLE Books
MODIFY COLUMN title VARCHAR(60) CHECK (title != '');

-- установить значение по умолчанию
ALTER TABLE Books
ALTER COLUMN title SET DEFAULT 'Untitled';

-- удалить значение по умолчанию
ALTER TABLE Books
ALTER COLUMN title DROP DEFAULT;
```

### Добавление и удаление первичного ключа

```sql
-- добавить
ALTER TABLE Books
ADD PRIMARY KEY (id);

-- удалить
ALTER TABLE Books
DROP PRIMARY KEY;
```

### Добавление и удаление внешнего ключа

```sql
-- сначала добавить поле
ALTER TABLE Books
ADD COLUMN publisher_id INT;

-- затем добавить внешний ключ
ALTER TABLE Books
ADD FOREIGN KEY (publisher_id) REFERENCES Publishers(id);

-- удалить внешний ключ
ALTER TABLE Books
DROP FOREIGN KEY publisher_id;
```

### Добавление и удаление дополнительной проверки (CHECK)

```sql
-- добавить CHECK без имени, сгенерируется автоматически
ALTER TABLE Books
ADD CHECK (title != '');

-- добавить CHECK с именем
ALTER TABLE Books
ADD CONSTRAINT title_is_not_empty_string CHECK (title != '');

-- удалить CHECK по имени
SHOW CREATE TABLE Books;
ALTER TABLE Books
DROP CONSTRAINT title_is_not_empty_string;

-- удалить несколько CHECK
ALTER TABLE Books
DROP CHECK books_chk_1,
DROP CHECK books_chk_2;
```
