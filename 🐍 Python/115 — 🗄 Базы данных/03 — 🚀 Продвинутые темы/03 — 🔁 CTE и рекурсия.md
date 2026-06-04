---
tags: [sql, cte, recursive]
---

# 🔄 Обобщённые табличные выражения (CTE)

## Простое CTE

```sql
WITH FirstBooks (bookname, year) AS (
    SELECT title, release_year
    FROM Books
    LIMIT 3
)
SELECT * FROM FirstBooks;

-- Результат:
-- The Shining | 1977
-- Fight Club | 1996
-- The Catcher in the Rye | 1951
```

## Использование двух CTE

```sql
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' and Authors.surname = 'King'
),
JeromeSalingerBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Jerome' and Authors.surname = 'Salinger'
)
SELECT * FROM StephenKingBooks
UNION
SELECT * FROM JeromeSalingerBooks;

-- Результат:
-- The Shining | 1977
-- The Green Mile | 1996
-- The Outsider | 2018
-- Dolores Claiborne | 1993
-- The Catcher in the Rye | 1951
-- Franny and Zooey | 1961
```

## Использование CTE внутри CTE

```sql
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' and Authors.surname = 'King'
),
StephenKingBooksReleasedIn1996 AS (
    SELECT title, release_year
    FROM StephenKingBooks
    WHERE release_year = 1996
)
SELECT * FROM StephenKingBooksReleasedIn1996;

-- Результат:
-- The Green Mile | 1996
```

## Рекурсивное CTE

### Синтаксис

```sql
WITH RECURSIVE <имя CTE> AS (
    -- нерекурсивный запрос, якорь
    SELECT ...

    UNION ALL  -- или UNION DISTINCT

    -- рекурсивный запрос
    SELECT ...
    FROM <имя CTE>
    WHERE <условие выхода>
)
SELECT * FROM <имя CTE>;
```

### Пример: числа от 1 до 10

```sql
WITH RECURSIVE NaturalNumbers AS (
    SELECT 1 AS number
    UNION ALL
    SELECT number + 1
    FROM NaturalNumbers
    WHERE number < 10
)
SELECT * FROM NaturalNumbers;

-- Результат: 1,2,3,4,5,6,7,8,9,10
```

### Пример: числа Фибоначчи

```sql
WITH RECURSIVE Fibonacci AS (
    SELECT 1 AS fib_prev,
           1 AS fib_cur
    UNION ALL
    SELECT fib_cur,
           fib_cur + fib_prev
    FROM Fibonacci
    LIMIT 10
)
SELECT fib_prev AS number FROM Fibonacci;

-- Результат: 1,1,2,3,5,8,13,21,34,55
```

### Пример: иерархия сотрудников

```sql
WITH RECURSIVE EmployeeHierarchy AS (
    SELECT id, name, 1 AS level
    FROM Employees
    WHERE manager_id IS NULL

    UNION ALL

    SELECT Employees.id, Employees.name, EmployeeHierarchy.level + 1
    FROM EmployeeHierarchy
    INNER JOIN Employees ON EmployeeHierarchy.id = Employees.manager_id
)
SELECT * FROM EmployeeHierarchy;

-- Результат:
-- id | name | level
-- 1 | Matthew | 1
-- 2 | Caroline | 2
-- 3 | Tom | 2
-- 4 | Sam | 3
-- 5 | Ann | 3
-- 6 | Anthony | 3
-- 7 | Lousie | 3
-- 8 | Travis | 3
-- 9 | John | 4
-- 10 | Jennifer | 4
-- 11 | Maria | 4
```

## Увеличение глубины рекурсии

```sql
SET @@cte_max_recursion_depth := 10000;
-- по умолчанию 1000
```
