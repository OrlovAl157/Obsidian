# SQL
Сборник для быстрого повторения  
Раздел Основы SQL  

---

## 1. Основы SQL

### 1.1 Извлечение данных

| Ключевое слово | Назначение | Пример |
|----------------|-----------|--------|
| SELECT | Выбирает столбцы для вывода | SELECT name, age |
| FROM | Указывает таблицу-источник | FROM Users |
| LIMIT | Ограничивает число строк результата | LIMIT 5 |
| LIMIT offset, n | Пропускает offset строк и выводит n строк | LIMIT 10, 5 |

**DISTINCT (дополнение ❗)**  
Удаляет дубликаты строк:

```sql
SELECT DISTINCT column
FROM table;
```

```sql
SELECT DISTINCT column1, column2
FROM table;
```

Пример:
```sql
SELECT title, director
FROM Films
LIMIT 5;
```

---

### 1.2 ORDER BY — сортировка

| Ключевое слово | Назначение | Пример |
|----------------|-----------|--------|
| ORDER BY | Сортирует строки результирующей таблицы | ORDER BY release_date |
| ORDER BY ... ASC | Сортировка по возрастанию (по умолчанию) | ORDER BY name ASC |
| ORDER BY ... DESC | Сортировка по убыванию | ORDER BY place DESC |

- можно сортировать по нескольким полям  
- можно использовать псевдонимы (AS) или исходные поля  
- если поле переименовано, можно явно указать table.column  

Пример:
```sql
SELECT title AS name
FROM Films
ORDER BY name DESC;
```

---

### 1.3 WHERE — фильтрация данных

Назначение: отбирает строки по условию  

- используется после FROM и до ORDER BY / LIMIT  
- псевдонимы (AS) использовать нельзя  

| Ключевое слово | Назначение |
|----------------|-----------|
| WHERE | Фильтрация |
| =, !=, <>, <, <=, >, >= | Сравнение |
| BETWEEN ... AND | Диапазон |
| IS NULL | Проверка NULL |
| IS NOT NULL | Проверка не-NULL |

Пример:
```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

---

### 1.4 AND / OR / IN / NOT — логика

| Оператор | Назначение | Пример |
|----------|-----------|--------|
| AND | Все условия истинны | streams > 50000 AND place <= 3 |
| OR | Хотя бы одно | artist = 'A' OR artist = 'B' |
| IN | Входит в список | artist IN ('A','B') |
| NOT | Отрицание | NOT artist = 'A' |
| NOT IN | Не входит | artist NOT IN ('A','B') |

Приоритет:  
IN → NOT → AND → OR  

---

### 1.5 LIKE — шаблонный поиск

- % — любое количество символов  
- _ — один символ  
- работает со строками  
- по умолчанию без учета регистра  

Примеры:
```sql
WHERE artist LIKE 'The%'
```

```sql
WHERE trackname LIKE '%You%'
```

#### Экранирование (ESCAPE)

```sql
WHERE value LIKE '__/%' ESCAPE '/'
```

---

### 1.6 Регистрозависимость

По умолчанию регистр не учитывается  

| Способ | Описание |
|--------|---------|
| CAST(... AS BINARY) | стандарт |
| CONVERT(..., BINARY) | MySQL |
| BINARY | короткая форма |

Пример:
```sql
WHERE name LIKE BINARY '%You%'
```

---

### 1.7 Вычисляемые поля

- считаются в SELECT  
- не хранятся в таблице  
- поддерживают + - * /  
- NULL → результат NULL  

Пример:
```sql
SELECT item,
       price * quantity AS revenue
FROM VibeStore;
```

---

### 1.8 Объединение значений

| Функция | Назначение |
|--------|-----------|
| CONCAT | объединение |
| CONCAT с разделителем | вручную |
| CONCAT_WS | с разделителем автоматически |

Примеры:
```sql
SELECT CONCAT(artist, trackname)
```

```sql
SELECT CONCAT(artist, ' - ', trackname)
```

```sql
SELECT CONCAT_WS(' ', artist, trackname)
```

Особенности:
- NULL → CONCAT возвращает NULL  
- CONCAT_WS игнорирует NULL  

