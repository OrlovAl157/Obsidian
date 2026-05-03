---
tags: sql, database, basics
уровень: базовый
статус: готово
раздел: основы sql
---
# **🗄️ Основы SQL**

Конспект оформлен по материалам PDF с сохранением последовательности тем.

## **📑 Содержание**

- [1.1 Извлечение данных](#11-%D0%B8%D0%B7%D0%B2%D0%BB%D0%B5%D1%87%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85)![Attachment.tiff](file:///Attachment.tiff)
- [1.2 ORDER BY — сортировка](#12-order-by--%D1%81%D0%BE%D1%80%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%BA%D0%B0)![Attachment.tiff](file:///Attachment.tiff)
- [1.3 WHERE — фильтрация данных](#13-where--%D1%84%D0%B8%D0%BB%D1%8C%D1%82%D1%80%D0%B0%D1%86%D0%B8%D1%8F-%D0%B4%D0%B0%D0%BD%D0%BD%D1%8B%D1%85)![Attachment.tiff](file:///Attachment.tiff)
- [1.4 AND / OR / IN / NOT](#14-and--or--in--not--%D0%BB%D0%BE%D0%B3%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B0%D1%8F-%D1%84%D0%B8%D0%BB%D1%8C%D1%82%D1%80%D0%B0%D1%86%D0%B8%D1%8F)![Attachment.tiff](file:///Attachment.tiff)
- [1.5 LIKE — шаблонный поиск](#15-like--%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD%D0%BD%D1%8B%D0%B9-%D0%BF%D0%BE%D0%B8%D1%81%D0%BA)![Attachment.tiff](file:///Attachment.tiff)
- [1.5.1 Экранирование (ESCAPE)](#151-%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5-escape)![Attachment.tiff](file:///Attachment.tiff)
- [1.6 Регистрозависимость](#16-%D1%80%D0%B5%D0%B3%D0%B8%D1%81%D1%82%D1%80%D0%BE%D0%B7%D0%B0%D0%B2%D0%B8%D1%81%D0%B8%D0%BC%D0%BE%D1%81%D1%82%D1%8C)![Attachment.tiff](file:///Attachment.tiff)
- [1.6.1 CAST / CONVERT / BINARY](#161-%D0%B2-%D1%87%D0%B5%D0%BC-%D1%80%D0%B0%D0%B7%D0%BD%D0%B8%D1%86%D0%B0-%D0%BC%D0%B5%D0%B6%D0%B4%D1%83-cast-convert-%D0%B8-binary)![Attachment.tiff](file:///Attachment.tiff)
- [1.7 Вычисляемые поля](#17-%D0%B2%D1%8B%D1%87%D0%B8%D1%81%D0%BB%D1%8F%D0%B5%D0%BC%D1%8B%D0%B5-%D0%BF%D0%BE%D0%BB%D1%8F)![Attachment.tiff](file:///Attachment.tiff)
- [1.8 Объединение значений полей](#18-%D0%BE%D0%B1%D1%8A%D0%B5%D0%B4%D0%B8%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5-%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%B8%D0%B9-%D0%BF%D0%BE%D0%BB%D0%B5%D0%B9)![Attachment.tiff](file:///Attachment.tiff)

---

## **1.1  Извлечение данных**

|**Ключевое слово**|**Назначение**|**Пример**|
|---|---|---|
|SELECT|Выбирает столбцы для вывода|SELECT name, age|
|FROM|Указывает таблицу-источник|FROM Users|
|LIMIT|Ограничивает число строк результата|LIMIT 5|
|LIMIT offset, n|Пропускает offset строк и выводит n строк|LIMIT 10, 5|

**Пример:** вывести названия и режиссёров первых 5 фильмов.

```sql
SELECT title, director
FROM Films
LIMIT 5;
```

## **1.2 ORDER BY — сортировка**

|**Ключевое слово**|**Назначение**|**Пример**|
|---|---|---|
|ORDER BY|Сортирует строки результата|ORDER BY release_date|
|ORDER BY … ASC|По возрастанию (по умолчанию)|ORDER BY name ASC|
|ORDER BY … DESC|По убыванию|ORDER BY place DESC|

- можно сортировать по нескольким полям;
- можно использовать псевдонимы (AS) или исходные поля;
- если поле переименовано в SELECT, можно сортировать по исходному через `table.column`.

```sql
SELECT title AS name
FROM Films
ORDER BY name DESC;
```

## **1.3 WHERE — фильтрация данных**

WHERE отбирает строки, удовлетворяющие условию.

- используется после FROM и до ORDER BY / LIMIT;
- используются операторы сравнения;
- для отсутствия значения: IS NULL / IS NOT NULL;
- псевдонимы из SELECT в WHERE использовать нельзя.

|**Конструкция**|**Назначение**|
|---|---|
|WHERE|Фильтрация строк|
|WHERE … =, !=, <>, <, <=, >, >=|Сравнение|
|WHERE … BETWEEN a AND b|Диапазон|
|WHERE … IS NULL|Проверка NULL|
|WHERE … IS NOT NULL|Проверка не-NULL|

```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

## **1.4 AND / OR / IN / NOT — логическая фильтрация**

|**Оператор**|**Назначение**|
|---|---|
|AND|Все условия истинны|
|OR|Хотя бы одно истинно|
|IN (…)|Значение входит в список|
|NOT|Отрицание|
|NOT IN (…)|Значение не входит в список|

```sql
WHERE streams > 50000 AND place <= 3
WHERE artist = 'Heart' OR artist = 'Kate Bush'
WHERE artist IN ('Heart', 'Kate Bush')
WHERE NOT artist = 'The Sounds'
```

Приоритет: `IN / NOT IN` → `NOT` → `AND` → `OR`

## **1.5 LIKE — шаблонный поиск**

LIKE используется для поиска строк по шаблону.

- `%` — любая последовательность символов;
- `_` — ровно один символ;
- шаблон можно ставить в любом месте;
- работает со строками;
- по умолчанию без учёта регистра.

|**Пример**|**Что означает**|
|---|---|
|LIKE ‘text%’|начинается с text|
|LIKE ‘%text%’|содержит text|
|LIKE ‘___%’|первые 3 любых символа|

## **1.5.1 Экранирование (ESCAPE)**

Используется для поиска самих `%` и `_` как обычных символов.

```sql
WHERE value LIKE '__/%' ESCAPE '/'
```

## **1.6 Регистрозависимость**

По умолчанию SQL сравнивает строки без учёта регистра.  
Если регистр важен — приводят к бинарному виду.

|**Способ**|**Пример**|
|---|---|
|CAST(… AS BINARY)|LIKE CAST(’%You%’ AS BINARY)|
|CONVERT(…, BINARY)|LIKE CONVERT(’%You%’, BINARY)|
|BINARY|LIKE BINARY ‘%You%’|

## **1.6.1 В чем разница между CAST, CONVERT и BINARY**

- `CAST(... AS BINARY)` — стандартный SQL;
- `CONVERT(..., BINARY)` — часто MySQL;
- `BINARY` — краткая форма;
- по смыслу эквивалентны для этой задачи.

## **1.7 Вычисляемые поля**

Поля, которые считаются прямо в запросе.

- вычисляются в SELECT;
- не сохраняются в таблице;
- можно использовать `+ - * /`;
- если один операнд NULL → результат NULL;
- псевдоним нельзя в WHERE;
- псевдоним можно в ORDER BY.

```sql
SELECT item,
       price * quantity AS revenue
FROM VibeStore;
```

## **1.8 Объединение значений полей**

|**Функция**|**Назначение**|
|---|---|
|CONCAT(a, b)|Склеивает без разделителя|
|CONCAT(a, ’ ’, b)|Склеивает с ручным разделителем|
|CONCAT_WS(’, ’, a, b)|Склеивает с автоматическим разделителем|

- аргументы приводятся к строке;
- если NULL в CONCAT → NULL;
- CONCAT_WS игнорирует NULL (кроме разделителя).

```sql
SELECT CONCAT_WS(', ', item, color,
       CONCAT('size ', size)) AS product
FROM Clothes;
```

---

## **🔗 Смежные темы**

- JOIN
- GROUP BY
- HAVING
- Подзапросы
- Индексы