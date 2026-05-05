---
tags:
  - sql
  - тема/базы_данных
  - статус/черновик
уровень: начальный
---

# 🧾 Основы SQL

## 📌 Коротко

> SQL — язык для работы с базами данных: выборка, фильтрация, сортировка и преобразование данных.

---

## 💡 Полезные советы

- Порядок: `SELECT → FROM → WHERE → ORDER BY → LIMIT`
- `DISTINCT` убирает дубликаты
- `WHERE` фильтрует строки до сортировки
- `ORDER BY` работает после выборки
- `LIMIT` ограничивает результат

---

## 🚀 Быстрая справка

| Конструкция | Синтаксис | Назначение |
|---|---|---|
| SELECT | `SELECT col` | выбор столбцов |
| FROM | `FROM table` | источник данных |
| DISTINCT | `SELECT DISTINCT col` | уникальные значения |
| WHERE | `WHERE cond` | фильтрация |
| ORDER BY | `ORDER BY col ASC/DESC` | сортировка |
| LIMIT | `LIMIT n` | ограничение |

---

## 📖 Теория

## 1. Извлечение данных

```sql
SELECT name, age
FROM Users
LIMIT 5;
```

### DISTINCT (уникальные значения)

```sql
SELECT DISTINCT name
FROM Users;
```

```sql
SELECT DISTINCT name, age
FROM Users;
```

---

## 2. ORDER BY — сортировка

```sql
SELECT name
FROM Users
ORDER BY name ASC;
```

```sql
ORDER BY name DESC;
```

---

## 3. WHERE — фильтрация

```sql
SELECT *
FROM Users
WHERE age > 18;
```

---

## 4. Логика: AND / OR / IN / NOT

```sql
WHERE age > 18 AND city = 'Moscow'
```

```sql
WHERE city IN ('Moscow', 'SPB')
```

---

## 5. LIKE — поиск

```sql
WHERE name LIKE 'A%'
```

---

## 6. Регистрозависимость

```sql
WHERE name LIKE BINARY 'Alex%'
```

---

## 7. Вычисляемые поля

```sql
SELECT price * quantity AS total
FROM Products;
```

---

## 8. CONCAT

```sql
SELECT CONCAT(name, ' ', surname)
```

```sql
SELECT CONCAT_WS(', ', city, country)
```

---

## 🧠 Копилка

- SELECT — что взять  
- FROM — откуда  
- WHERE — фильтр  
- ORDER BY — сортировка  
- DISTINCT — уникальные значения  
- LIMIT — ограничение  

---

## 🔗 Связанные темы

- ...
