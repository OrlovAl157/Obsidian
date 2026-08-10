---
tags: [sql, dml, insert, update, delete, replace]
---

# ✏️ Изменение данных (DML)

## Вставка данных (INSERT)

### INSERT с VALUES

```sql
INSERT IGNORE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),
       (6, 'Lord of the Flies', 'William Golding', 5.99);
```

`IGNORE` игнорирует ошибки и продолжает вставку остальных строк.

### INSERT с SET

```sql
INSERT INTO Books
SET id = 6,
    title = 'Animal Farm',
    author = 'George Orwell',
    price = 9.99;
```

### INSERT с SELECT

```sql
INSERT INTO Books (id, title, author, price)
SELECT id, title, author, price
FROM NewBooks;
```

## REPLACE

Работает как `INSERT`, но при конфликте `PRIMARY KEY` или `UNIQUE` заменяет старую запись на новую.

```sql
REPLACE INTO Books (id, title, author, price)
VALUES (1, 'Animal Farm', 'George Orwell', 9.99),
       (6, 'Lord of the Flies', 'William Golding', 5.99);
```

## Обновление данных (UPDATE)

```sql
UPDATE IGNORE Books
INNER JOIN Authors ON Books.author_id = Authors.id
SET price = 9.99,
    amount = 50
WHERE Authors.country = 'USA'
ORDER BY Books.id
LIMIT 10;
```

## UPDATE с CASE

```sql
UPDATE Books
SET price = CASE
    WHEN amount < 5 THEN price * 0.9
    WHEN amount BETWEEN 5 AND 10 THEN price * 0.7
    ELSE price * 0.5
END;
```

## Удаление данных (DELETE)

```sql
DELETE IGNORE FROM Books
USING Books
INNER JOIN Authors ON Books.author_id = Authors.id
WHERE Authors.country = 'Russia'
ORDER BY Books.price
LIMIT 100;
```

## TRUNCATE

Удаляет все данные из таблицы. Не попадает в журнал и не подлежит восстановлению.

```sql
TRUNCATE TABLE Books;
```

## Сравнение DELETE vs TRUNCATE

| Характеристика | DELETE | TRUNCATE |
|----------------|--------|----------|
| Можно использовать `WHERE` | да | нет |
| Можно восстановить | да, из журнала | нет |
| Скорость | медленнее | быстрее |
| Сбрасывает `AUTO_INCREMENT` | нет | да |
