---
tags:
  - sql
  - database
  - basics
  - тема/sql_основы
  - статус/завершён
уровень: базовый
раздел: основы sql
---

# 🗄️ Основы SQL

Конспект по основным командам SQL, синтаксису и типовым приёмам.

---

## 📑 Содержание

- [[#11-извлечение-данных]]
- [[#12-order-by--сортировка]]
- [[#13-where--фильтрация-данных]]
- [[#14-and--or--in--not--логическая-фильтрация]]
- [[#15-like--шаблонный-поиск]]
- [[#151-экранирование-escape]]
- [[#16-регистрозависимость]]
- [[#161-cast--convert--binary]]
- [[#17-вычисляемые-поля]]
- [[#18-объединение-значений-полей]]

---

## 💡 Полезные советы

- Порядок выполнения: `SELECT → FROM → WHERE → ORDER BY → LIMIT`
- `DISTINCT` убирает дубликаты строк
- `WHERE` выполняется до сортировки
- `ORDER BY` после выборки
- Псевдонимы (`AS`) нельзя использовать в `WHERE`

---

## 🚀 Быстрая справка

| Конструкция | Синтаксис | Назначение |
|---|---|---|
| SELECT | `SELECT col` | выбор столбцов |
| DISTINCT | `SELECT DISTINCT col` | уникальные строки |
| FROM | `FROM table` | источник |
| WHERE | `WHERE cond` | фильтрация |
| ORDER BY | `ORDER BY col ASC/DESC` | сортировка |
| LIMIT | `LIMIT n` | ограничение |

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

### DISTINCT — уникальные значения

Удаляет дубликаты строк результата.

```sql
SELECT DISTINCT name
FROM Users;
```

```sql
SELECT DISTINCT name, age
FROM Users;
```

**Важно:** уникальность определяется по комбинации всех выбранных столбцов.

**Пример:**

```sql
SELECT title, director
FROM Films
LIMIT 5;
```

---

## 1.2 ORDER BY — сортировка

| Ключевое слово | Назначение | Пример |
|---|---|---|
| ORDER BY | Сортирует строки результата | ORDER BY release_date |
| ORDER BY … ASC | По возрастанию (по умолчанию) | ORDER BY name ASC |
| ORDER BY … DESC | По убыванию | ORDER BY place DESC |

- можно сортировать по нескольким полям;
- можно использовать псевдонимы (AS) или исходные поля;
- если поле переименовано, можно использовать `table.column`.

```sql
SELECT title AS name
FROM Films
ORDER BY name DESC;
```

---

## 1.3 WHERE — фильтрация данных

WHERE отбирает строки, удовлетворяющие условию.

- используется после FROM и до ORDER BY / LIMIT;
- используются операторы сравнения;
- для отсутствия значения: IS NULL / IS NOT NULL;
- псевдонимы из SELECT в WHERE использовать нельзя.

| Конструкция | Назначение |
|---|---|
| WHERE | Фильтрация строк |
| =, !=, <>, <, <=, >, >= | Сравнение |
| BETWEEN a AND b | Диапазон |
| IS NULL | Проверка NULL |
| IS NOT NULL | Проверка не-NULL |

```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

---

## 1.4 AND / OR / IN / NOT — логическая фильтрация

| Оператор | Назначение |
|---|---|
| AND | Все условия истинны |
| OR | Хотя бы одно истинно |
| IN (…)| Значение входит в список |
| NOT | Отрицание |
| NOT IN (…) | Значение не входит |

```sql
WHERE streams > 50000 AND place <= 3
WHERE artist = 'Heart' OR artist = 'Kate Bush'
WHERE artist IN ('Heart', 'Kate Bush')
WHERE NOT artist = 'The Sounds'
```

Приоритет:

`IN / NOT IN → NOT → AND → OR`

---

## 1.5 LIKE — шаблонный поиск

LIKE используется для поиска строк по шаблону.

- `%` — любая последовательность символов;
- `_` — ровно один символ;
- шаблон можно ставить в любом месте;
- работает со строками;
- по умолчанию без учёта регистра.

| Пример | Что означает |
|---|---|
| LIKE 'text%' | начинается с text |
| LIKE '%text%' | содержит text |
| LIKE '___%' | первые 3 любых символа |

---

## 1.5.1 Экранирование (ESCAPE)

Используется для поиска самих `%` и `_`.

```sql
WHERE value LIKE '__/%' ESCAPE '/'
```

---

## 1.6 Регистрозависимость

По умолчанию SQL не учитывает регистр.

| Способ | Пример |
|---|---|
| CAST(... AS BINARY) | LIKE CAST('%You%' AS BINARY) |
| CONVERT(..., BINARY) | LIKE CONVERT('%You%', BINARY) |
| BINARY | LIKE BINARY '%You%' |

---

## 1.6.1 CAST / CONVERT / BINARY

- CAST — стандарт SQL;
- CONVERT — часто MySQL;
- BINARY — краткая форма;
- по смыслу эквивалентны.

---

## 1.7 Вычисляемые поля

- вычисляются в SELECT;
- не сохраняются в таблице;
- можно использовать `+ - * /`;
- NULL → результат NULL;
- псевдоним нельзя в WHERE;
- псевдоним можно в ORDER BY.

```sql
SELECT item,
       price * quantity AS revenue
FROM VibeStore;
```

---

## 1.8 Объединение значений полей

| Функция | Назначение |
|---|---|
| CONCAT(a, b) | Склеивает без разделителя |
| CONCAT(a, ' ', b) | С разделителем |
| CONCAT_WS(', ', a, b) | С авто-разделителем |

- аргументы приводятся к строке;
- если NULL → CONCAT возвращает NULL;
- CONCAT_WS игнорирует NULL (кроме разделителя).

```sql
SELECT CONCAT_WS(', ', item, color,
       CONCAT('size ', size)) AS product
FROM Clothes;
```

---

## 🔗 Смежные темы

- JOIN
- GROUP BY
- HAVING
- Подзапросы
- Индексы
