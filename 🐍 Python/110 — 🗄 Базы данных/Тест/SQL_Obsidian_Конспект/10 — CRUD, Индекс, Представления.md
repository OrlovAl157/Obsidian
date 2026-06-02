---
type: sql-note
source: Конспект SQL.xlsx
created: 2026-06-02
tags: [sql, database, конспект]
---

# CRUD, Индекс, Представления

> Источник: лист Excel-конспекта. Перенесено в Markdown для Obsidian.

## Создание таблиц:

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
| CREATE TABLE | IF NOT EXISTS | <1 Таблица> |  |  |  |  |

## (

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | <1 поле> | INT | PRIMARY KEY | AUTO_INCREMENT |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | <2 поле> | INT | NOT NULL | UNIQUE | CHECK (<2 поле> != ' ') | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | <3 поле> | VARCHAR(40) | DEFAULT  | '______' |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | <4 поле> | VARCHAR(40) | DEFAULT  | (CONCAT(<1 поле>, ' by ', <2 поле>)) |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | PRIMARY KEY | (<1 поле>, <2 поле>) |  |  |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | FOREIGN KEY | (<1 поле>, <2 поле>) | REFERENCES | <2 Таблица>(<1 поле>, <2 поле>) |  |  |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  |  | ON UPDATE | CASCADE | RESTRICT (ограничивать) cтоит по умолчанию. |  |  |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  |  | ON DELETE | SET NULL |  |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | CONSTRAINT | some_check | CHECK (id > 0) |  |  | , |

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
|  | CONSTRAINT | another_check | CHECK (id != 10) |  |  |  |

```sql
);
```

## Удаления таблиц:

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
| DROP TABLE | IF EXISTS | <1 Таблица>, <2 Таблица>, ... |  | ; |  |  |

## Переименование таблицы:

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
| RENAME TABLE | <текущее имя таблицы> | TO | <новое имя таблицы> | ; |  |  |

## Обновление таблицы:

## Удаление поля

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
| ALTER TABLE Books<br>DROP COLUMN author; |  |  | Узнать имя, присвоенное ограничению, можно с помощью оператора SHOW CREATE TABLE Books |  |  |  |

## Переименование поля

```sql
ALTER TABLE Books
RENAME COLUMN author TO writer;
```

## Добавление поля

```sql
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0);
```

```sql
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0) FIRST;
```

```sql
ALTER TABLE Books
ADD COLUMN release_year INT CHECK (release_year > 0) AFTER title NOT NULL;
```

## Обновление поля

```sql
ALTER TABLE Books
MODIFY COLUMN title VARCHAR(60) CHECK (title != '');
```

```sql
ALTER TABLE Books
ALTER COLUMN title SET DEFAULT 'Untitled';
```

```sql
ALTER TABLE Books
ALTER COLUMN title DROP DEFAULT;
```

## Добавление и удаление первичного ключа

```sql
ALTER TABLE Books
ADD PRIMARY KEY (id);
```

```sql
ALTER TABLE Books
DROP PRIMARY KEY;
```

## Добавление и удаление внешнего ключа

```sql
ALTER TABLE Books
ADD COLUMN publisher VARCHAR(40);

ALTER TABLE Books
ADD FOREIGN KEY (publisher) REFERENCES Publishers (name);
```

```sql
ALTER TABLE Books
DROP FOREIGN KEY publisher;
```

## Добавление и удаление дополнительной проверки

| Колонка 1 | Колонка 2 | Колонка 3 | Колонка 4 | Колонка 5 | Колонка 6 | Колонка 7 |
| --- | --- | --- | --- | --- | --- | --- |
| ALTER TABLE Books<br>ADD CHECK (title != ''); |  |  | Чтобы удалить ADD CHECK:<br>SHOW CREATE TABLE Books;   -- смотрим имя добавленного CHECK<br>ALTER TABLE Books<br>DROP CHECK books_chk_1,   -- удаляем нужный CHECK<br>DROP CHECK books_chk_2 |  |  |  |

```sql
ALTER TABLE Books
ADD CONSTRAINT title_is_not_empty_string CHECK (title != '');
```

```sql
ALTER TABLE Books
DROP CONSTRAINT title_is_not_empty_string;
```

## Индексация таблиц

## Создание индекса

```sql
CREATE INDEX <имя некластеризованного индекса>
ON <имя таблицы>(<имя индексируемого поля>);
```

## Просмотр индексов

```sql
SHOW INDEX FROM <имя таблицы>;
```

## Удаление индексов

```sql
DROP INDEX <имя некластеризованного индекса>
ON <имя таблицы>(<имя индексируемого поля>);
```

## Переименование индекса

```sql
ALTER TABLE <имя таблицы>ALTER TABLE <имя таблицы>
RENAME INDEX <текущее имя индекса> TO <новое имя индекса>;
```

## Представления

## Создание представлений

```sql
CREATE VIEW <имя представления> AS
<извлекающий запрос>
```

## Изменение представлений

```sql
ALTER VIEW <имя представления> AS
<новый извлекающий запрос>
```

## Удаление представлений

## DROP VIEW <имя представления>
