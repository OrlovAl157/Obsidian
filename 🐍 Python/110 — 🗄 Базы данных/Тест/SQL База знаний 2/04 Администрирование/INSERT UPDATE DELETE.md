---
tags: [sql, dml]
---

# INSERT, UPDATE, DELETE, REPLACE

## INSERT

### Стандартная вставка
```sql
INSERT INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),
       (2, 'Lord of the Flies', 'William Golding', 5.99);
```

### INSERT IGNORE
```sql
INSERT IGNORE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99);
```

### INSERT с SET
```sql
INSERT INTO Books
SET id = 1, title = 'Animal Farm', author = 'George Orwell', price = 9.99;
```

### INSERT с подзапросом
```sql
INSERT INTO Books (id, title, author, price)
SELECT id, title, author, price FROM NewBooks;
```

## REPLACE
Удаляет старую запись и вставляет новую.
```sql
REPLACE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99);
```

## UPDATE

### Простое обновление
```sql
UPDATE Books
SET price = 9.99, amount = 50
WHERE id = 1;
```

### UPDATE с CASE
```sql
UPDATE Books
SET price = CASE
    WHEN amount < 5 THEN price * 0.9
    WHEN amount BETWEEN 5 AND 10 THEN price * 0.7
    ELSE price * 0.5
END;
```

### UPDATE IGNORE
```sql
UPDATE IGNORE Books
SET id = NULL
WHERE id = 1;
```

## DELETE

### Удаление записей
```sql
DELETE FROM Books
WHERE price < 10
ORDER BY id
LIMIT 5;
```

### DELETE IGNORE
```sql
DELETE IGNORE FROM Books
WHERE id = 1;
```

### DELETE с JOIN
```sql
DELETE FROM Books
USING Books INNER JOIN OldBooks ON Books.id = OldBooks.id;
```

## TRUNCATE
Быстрое удаление всех записей.
```sql
TRUNCATE Books;
```

⚠️ TRUNCATE не попадает в журнал и не подлежит восстановлению!
