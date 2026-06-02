---
tags: [sql, ddl]
---

# CREATE, ALTER, DROP

## Создание таблицы
```sql
CREATE TABLE IF NOT EXISTS Books (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(40) NOT NULL UNIQUE,
    author VARCHAR(40) DEFAULT 'Unknown',
    price DECIMAL(10,2) CHECK (price > 0),
    release_year INT CHECK (release_year > 0),
    PRIMARY KEY (id, title),
    FOREIGN KEY (author) REFERENCES Authors(name) 
        ON UPDATE CASCADE 
        ON DELETE SET NULL,
    CONSTRAINT price_check CHECK (price > 0)
);
```

## Удаление таблиц
```sql
DROP TABLE IF EXISTS Books, Authors;
```

## Переименование таблицы
```sql
RENAME TABLE old_name TO new_name;
```

## ALTER TABLE

### Удаление поля
```sql
ALTER TABLE Books DROP COLUMN author;
```

### Переименование поля
```sql
ALTER TABLE Books RENAME COLUMN author TO writer;
```

### Добавление поля
```sql
ALTER TABLE Books ADD COLUMN release_year INT CHECK (release_year > 0);
ALTER TABLE Books ADD COLUMN release_year INT FIRST;
ALTER TABLE Books ADD COLUMN release_year INT AFTER title;
```

### Изменение поля
```sql
ALTER TABLE Books MODIFY COLUMN title VARCHAR(60) CHECK (title != '');
ALTER TABLE Books ALTER COLUMN title SET DEFAULT 'Untitled';
ALTER TABLE Books ALTER COLUMN title DROP DEFAULT;
```

### Первичный ключ
```sql
ALTER TABLE Books ADD PRIMARY KEY (id);
ALTER TABLE Books DROP PRIMARY KEY;
```

### Внешний ключ
```sql
ALTER TABLE Books ADD FOREIGN KEY (publisher) REFERENCES Publishers(name);
ALTER TABLE Books DROP FOREIGN KEY publisher;
```

### CHECK-ограничения
```sql
ALTER TABLE Books ADD CHECK (title != '');
ALTER TABLE Books ADD CONSTRAINT title_not_empty CHECK (title != '');
ALTER TABLE Books DROP CONSTRAINT title_not_empty;
-- Узнать имя CHECK: SHOW CREATE TABLE Books;
```

## Индексы

### Создание
```sql
CREATE INDEX idx_title ON Books(title);
```

### Просмотр
```sql
SHOW INDEX FROM Books;
```

### Удаление
```sql
DROP INDEX idx_title ON Books;
```

### Переименование
```sql
ALTER TABLE Books RENAME INDEX old_name TO new_name;
```

## Представления (VIEW)

### Создание
```sql
CREATE VIEW expensive_books AS
SELECT title, price FROM Books WHERE price > 100;
```

### Изменение
```sql
ALTER VIEW expensive_books AS
SELECT title, price FROM Books WHERE price > 50;
```

### Удаление
```sql
DROP VIEW expensive_books;
```
