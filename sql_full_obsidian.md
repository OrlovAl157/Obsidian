---
tags:
  - sql
  - тема/базы_данных
уровень: начальный
---

# 🧾 Основы SQL

## 📌 Коротко

> Конспект по основным командам SQL, синтаксису и типовым приёмам.

---

## 📖 Теория

---

## 1.1 Извлечение данных

| Ключевое слово | Назначение | Пример |
|---|---|---|
| SELECT | Выбирает столбцы для вывода | SELECT name, age |
| FROM | Указывает таблицу-источник | FROM Users |
| LIMIT | Ограничивает число строк результата | LIMIT 5 |
| LIMIT offset, n | Пропускает offset строк и выводит n строк | LIMIT 10, 5 |

### DISTINCT (дополнение ❗)

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

## 1.2 ORDER BY — сортировка

| Ключевое слово | Назначение | Пример |
|---|---|---|
| ORDER BY | Сортирует строки результирующей таблицы | ORDER BY release_date |
| ORDER BY ... ASC | Сортировка по возрастанию (по умолчанию) | ORDER BY name ASC |
| ORDER BY ... DESC | Сортировка по убыванию | ORDER BY place DESC |

- можно сортировать по нескольким полям  
- можно использовать псевдонимы (AS) или исходные поля  
- В ORDER BY можно сортировать по псевдониму (AS) или по исходному полю  
- Если поле переименовано в SELECT, можно сортировать по исходному, указав его как table.column  

Пример:

```sql
SELECT title AS name
FROM Films
ORDER BY name DESC;
```

---

## 1.3 WHERE — фильтрация данных

Назначение: оператор WHERE отбирает строки, удовлетворяющие условию.

- используется после FROM и до ORDER BY / LIMIT  
- условия задаются операторами сравнения  
- для NULL: IS NULL / IS NOT NULL  
- псевдонимы (AS) использовать нельзя  

| Ключевое слово | Назначение |
|---|---|
| WHERE | Фильтрация строк |
| =, !=, <>, <, <=, >, >= | Сравнение |
| BETWEEN ... AND | Диапазон |
| IS NULL | Проверка на NULL |
| IS NOT NULL | Проверка на не-NULL |

Пример:

```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

---

## 1.4 AND / OR / IN / NOT — логическая фильтрация

| Оператор | Назначение | Пример |
|---|---|---|
| AND | Все условия истинны | streams > 50000 AND place <= 3 |
| OR | Хотя бы одно | artist = 'Heart' OR artist = 'Kate Bush' |
| IN | Значение входит в список | artist IN ('Heart','Kate Bush') |
| NOT | Отрицание | NOT artist = 'The Sounds' |
| NOT IN | Не входит | artist NOT IN ('Heart','Kate Bush') |

Приоритет операторов:

IN → NOT → AND → OR  

---

## 1.5 LIKE — шаблонный поиск

Назначение: поиск строк по шаблону.

- % — любая последовательность символов  
- _ — ровно один символ  
- применяется к строкам  
- по умолчанию без учета регистра  

Примеры:

```sql
WHERE artist LIKE 'The%'
```

```sql
WHERE trackname LIKE '%You%'
```

```sql
WHERE trackname LIKE '___ %'
```

### Экранирование (ESCAPE)

Назначение: поиск самих символов % и _

```sql
WHERE value LIKE '__/%' ESCAPE '/'
```

---

## 1.6 Регистрозависимость

Назначение: по умолчанию SQL не учитывает регистр.

| Ключевое слово | Назначение |
|---|---|
| CAST(... AS BINARY) | стандарт |
| CONVERT(..., BINARY) | MySQL |
| BINARY | сокращенная форма |

Пример:

```sql
WHERE name LIKE BINARY '%You%'
```

### Разница:

- CAST — стандарт SQL  
- CONVERT — MySQL  
- BINARY — сокращение  

---

## 1.7 Вычисляемые поля

Назначение: поля, которые вычисляются в запросе.

- вычисляются в SELECT  
- не сохраняются в таблице  
- поддерживают + - * /  
- NULL → результат NULL  
- нельзя использовать в WHERE  
- можно использовать в ORDER BY  

Пример:

```sql
SELECT item,
       price * quantity AS revenue
FROM VibeStore;
```

---

## 1.8 Объединение значений полей

| Функция | Назначение |
|---|---|
| CONCAT | объединяет значения |
| CONCAT с разделителем | вручную |
| CONCAT_WS | с разделителем |

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

- аргументы приводятся к строке  
- NULL → CONCAT возвращает NULL  
- CONCAT_WS игнорирует NULL  

---

## 🧠 Копилка

- SELECT — что взять  
- FROM — откуда  
- WHERE — фильтр  
- ORDER BY — сортировка  
- DISTINCT — уникальные значения  
- LIMIT — ограничение  
