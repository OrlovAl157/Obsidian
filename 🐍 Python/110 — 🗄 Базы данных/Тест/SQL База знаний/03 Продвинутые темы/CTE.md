---
tags: [sql, cte, recursive]
---

# CTE (Common Table Expressions)

## Простое CTE
```sql
WITH FirstBooks (bookname, year) AS (
    SELECT title, release_year
    FROM Books
    LIMIT 3
)
SELECT * FROM FirstBooks;
```

## Несколько CTE
```sql
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' AND Authors.surname = 'King'
),
JeromeSalingerBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Jerome' AND Authors.surname = 'Salinger'
)
SELECT * FROM StephenKingBooks
UNION
SELECT * FROM JeromeSalingerBooks;
```

## CTE в CTE
```sql
WITH StephenKingBooks AS (
    SELECT title, release_year
    FROM Books INNER JOIN Authors ON Books.author_id = Authors.id
    WHERE Authors.name = 'Stephen' AND Authors.surname = 'King'
),
StephenKingBooksReleasedIn1996 AS (
    SELECT title, release_year
    FROM StephenKingBooks
    WHERE release_year = 1996
)
SELECT * FROM StephenKingBooksReleasedIn1996;
```

## Рекурсивное CTE

### Натуральные числа
```sql
WITH RECURSIVE NaturalNumbers AS (
    SELECT 1 AS number
    UNION ALL
    SELECT number + 1
    FROM NaturalNumbers
    WHERE number < 10
)
SELECT * FROM NaturalNumbers;
```

### Числа Фибоначчи
```sql
WITH RECURSIVE Fibonacci AS (
    SELECT 1 AS fib_prev, 1 AS fib_cur
    UNION ALL
    SELECT fib_cur, fib_cur + fib_prev
    FROM Fibonacci
    LIMIT 10
)
SELECT fib_prev AS number FROM Fibonacci;
```

### Иерархия сотрудников
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
```

## Увеличение глубины рекурсии
```sql
SET @@cte_max_recursion_depth := 10000;  -- по умолчанию 1000
```
