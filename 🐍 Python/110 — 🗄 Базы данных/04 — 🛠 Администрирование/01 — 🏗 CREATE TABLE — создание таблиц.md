---
tags: [sql, ddl, create, администрирование]
difficulty: intermediate
---

# 🏗 CREATE TABLE — создание таблиц — памятка

> `CREATE TABLE` создаёт новую таблицу и определяет её структуру: поля, типы данных и ограничения целостности. Выполняется немедленно и необратимо — не требует и не поддерживает `COMMIT/ROLLBACK`.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 CREATE TABLE — создание таблицы|CREATE TABLE]]
- [[#🔵 Ограничения и ключевые слова|Ограничения]]
    - [[#🔑 PRIMARY KEY|PRIMARY KEY]]
    - [[#🔢 AUTO_INCREMENT|AUTO_INCREMENT]]
    - [[#📌 DEFAULT|DEFAULT]]
    - [[#🔗 FOREIGN KEY и ON UPDATE / ON DELETE|FOREIGN KEY и ON UPDATE/DELETE]]
    - [[#✔️ CHECK и CONSTRAINT|CHECK и CONSTRAINT]]
    - [[#➕ Комбинирование ограничений|Комбинирование ограничений]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Оператор | Что делает |
|---|---|
| `CREATE TABLE t (...)` | создать таблицу |
| `CREATE TABLE IF NOT EXISTS t (...)` | создать если не существует |
| `SHOW CREATE TABLE t` | показать SQL создания таблицы |
| `DESCRIBE t` | показать структуру таблицы (поля, типы, ключи) |

---

## 🟢 CREATE TABLE — создание таблицы

```sql
-- Полный пример с ограничениями
CREATE TABLE IF NOT EXISTS Books (
    id          INT PRIMARY KEY AUTO_INCREMENT,
    title       VARCHAR(40) NOT NULL UNIQUE,
    author      VARCHAR(40) DEFAULT 'Unknown',
    price       DECIMAL(10, 2) CHECK (price >= 0),
    created_at  DATE DEFAULT (CURDATE()),
    author_id   INT,
    FOREIGN KEY (author_id) REFERENCES Authors(id)
        ON UPDATE CASCADE
        ON DELETE SET NULL,
    CONSTRAINT price_positive CHECK (price > 0)
);
```

**Минимальная таблица:**

```sql
CREATE TABLE Users (
    id   INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);
```

**IF NOT EXISTS** — не вызывает ошибку если таблица уже существует:

```sql
CREATE TABLE Books (...);              -- ❌ ошибка если Books уже есть
CREATE TABLE IF NOT EXISTS Books (...); -- ✅ просто пропустит если уже есть
```

**Составной первичный ключ** — если ключом должны быть сразу несколько полей, `PRIMARY KEY` выносится в отдельное определение (в определении одного поля так писать нельзя):

```sql
CREATE TABLE Books (
    title  VARCHAR(40),
    author VARCHAR(40),
    PRIMARY KEY (title, author)   -- уникальна пара значений, а не каждое поле по отдельности
);
```

---

## 🔵 Ограничения и ключевые слова

| Ключевое слово | Что делает |
|---|---|
| `PRIMARY KEY` | первичный ключ — уникальный идентификатор строки |
| `AUTO_INCREMENT` | автоматическое увеличение (только для INT PRIMARY KEY) |
| `NOT NULL` | поле не может быть NULL |
| `UNIQUE` | все значения в поле уникальны |
| `DEFAULT value` | значение по умолчанию |
| `CHECK (expr)` | проверка условия при вставке/обновлении |
| `FOREIGN KEY (col) REFERENCES t(col)` | внешний ключ — связь с другой таблицей |

### 🔑 PRIMARY KEY

**PRIMARY KEY** гарантирует две вещи одновременно: поле не может быть `NULL` и не может содержать повторяющиеся значения. По сути это комбинация `NOT NULL` + `UNIQUE`, но нагляднее и поддерживает составные ключи.

**PRIMARY KEY и FOREIGN KEY — это роли поля, а не два разных типа сущностей.** Одно и то же значение может быть первичным ключом в одной таблице и одновременно храниться как внешний ключ в другой — именно это и связывает таблицы между собой. У таблицы может быть только один `PRIMARY KEY`, но при этом сколько угодно `FOREIGN KEY`, ссылающихся на разные родительские таблицы:

```
orders
┌─────┬─────────┬────────────┬────────┐
│ id  │ user_id │ product_id │ status │
│(PK) │  (FK)   │    (FK)    │        │
├─────┼─────────┼────────────┼────────┤
│ 101 │    1    │     55     │ new    │
│ 102 │    1    │     77     │ paid   │
└─────┴─────────┴────────────┴────────┘
  │        │            │
  │        │            └──► REFERENCES products(id)
  │        └──────────────► REFERENCES users(id)
  │
  └──► свой собственный PRIMARY KEY таблицы orders
```

Здесь `id` — первичный ключ самой таблицы `orders`, а `user_id` и `product_id` — два независимых внешних ключа, ссылающихся на `users.id` и `products.id` соответственно. Внешний ключ не "становится" им сам по себе — это поле, которое ты изначально проектируешь для хранения id из другой таблицы.

### 🔢 AUTO_INCREMENT

**AUTO_INCREMENT** — только для `INT PRIMARY KEY`, автоматически подставляет следующее натуральное число. Несовместим с `DEFAULT`. Счётчик не переиспользует значения после `DELETE` (следующая запись продолжит с последнего использованного id), но сбрасывается до 1 после `TRUNCATE` (см. [[04 — 🗑️ DELETE — удаление данных]]).

### 📌 DEFAULT

**DEFAULT — выражение может ссылаться на другие поля этой же записи** (только на те, что уже определены выше по списку):

```sql
CREATE TABLE Books (
    title     VARCHAR(40),
    author    VARCHAR(40),
    fulltitle VARCHAR(40) DEFAULT (CONCAT(title, ' by ', author))
);
-- при вставке без fulltitle он соберётся сам: 'Fight Club by Chuck Palahniuk'
```

Если поле не имеет явного `DEFAULT`, но допускает `NULL` — его значением по умолчанию будет `NULL`.

### 🔗 FOREIGN KEY и ON UPDATE / ON DELETE

Поведение внешнего ключа при изменении или удалении родительской записи — это то, что определяет `ON UPDATE`/`ON DELETE`:

| Опция | Что происходит при изменении/удалении родительской записи |
|---|---|
| `CASCADE` | каскадно обновляет/удаляет дочерние записи |
| `SET NULL` | устанавливает NULL в дочерней таблице |
| `RESTRICT` | запрещает изменение/удаление (по умолчанию) |
| `NO ACTION` | то же что RESTRICT |
| `SET DEFAULT` | устанавливает значение по умолчанию |

```sql
FOREIGN KEY (author_id) REFERENCES Authors(id)
    ON UPDATE CASCADE   -- если id автора изменился — обновится и в Books
    ON DELETE SET NULL  -- если автора удалили — author_id станет NULL
```

**Требование к типам:** внешний ключ и поле, на которое он ссылается, должны иметь одинаковый тип данных. Для `INT`/`DECIMAL` и подобных типов с фиксированной точностью должны совпадать ещё размер и знак.

**Составной внешний ключ** — если связь строится сразу по нескольким полям:

```sql
FOREIGN KEY (col1, col2) REFERENCES ParentTable(col1, col2)
```

### ✔️ CHECK и CONSTRAINT

**CHECK на уровне поля ссылается только на само это поле.** Если `CHECK` объявлен прямо в определении поля (`price DECIMAL CHECK (price > 0)`), он не может проверять другие поля таблицы. Если нужно сравнить несколько полей между собой (например, `price > cost`), CHECK нужно вынести в отдельное табличное ограничение через `CONSTRAINT`:

```sql
CREATE TABLE Books (
    price DECIMAL(10,2),
    cost  DECIMAL(10,2),
    CONSTRAINT price_above_cost CHECK (price > cost)  -- ✅ ссылается на 2 поля
);
```

**CONSTRAINT — именованное ограничение:**

```sql
-- Без имени — MySQL генерирует имя автоматически
CHECK (price > 0)

-- С именем — удобно для последующего удаления
CONSTRAINT price_positive CHECK (price > 0)
```

Автосгенерированное имя `CHECK` имеет шаблон `<таблица>_chk_<номер>` — например, первое безымянное ограничение в `Books` получит имя `Books_chk_1`, второе — `Books_chk_2`, и т.д. Именно это имя нужно, чтобы удалить CHECK через `DROP CHECK`.

### ➕ Комбинирование ограничений

Несколько ключевых слов в одном поле пишутся через пробел, а не через запятую (запятая разделяет только сами поля):

```sql
id INT NOT NULL UNIQUE   -- ✅ через пробел
id INT NOT NULL, UNIQUE  -- ❌ синтаксическая ошибка
```

---

## ⚡ Быстрые примеры

```sql
-- Создать таблицу
CREATE TABLE IF NOT EXISTS Authors (
    id     INT PRIMARY KEY AUTO_INCREMENT,
    name   VARCHAR(100) NOT NULL,
    email  VARCHAR(100) UNIQUE,
    rating DECIMAL(3, 1) DEFAULT 0 CHECK (rating BETWEEN 0 AND 10)
);

-- Составной первичный ключ
CREATE TABLE BookAuthors (
    book_id   INT,
    author_id INT,
    PRIMARY KEY (book_id, author_id)
);

-- Таблица со всеми видами ограничений
CREATE TABLE Books (
    id        INT PRIMARY KEY AUTO_INCREMENT,
    title     VARCHAR(100) NOT NULL UNIQUE,
    price     DECIMAL(10,2) CHECK (price >= 0),
    author_id INT,
    FOREIGN KEY (author_id) REFERENCES Authors(id) ON DELETE CASCADE
);
```

---

## ⚠️ Частые ошибки

**❌ Забыл IF NOT EXISTS при повторном создании:**
```sql
CREATE TABLE Books (...);  -- ❌ ошибка при втором запуске скрипта
CREATE TABLE IF NOT EXISTS Books (...);  -- ✅
```

**❌ AUTO_INCREMENT вместе с DEFAULT — несовместимы:**
```sql
id INT PRIMARY KEY AUTO_INCREMENT DEFAULT 1  -- ❌ ERROR
id INT PRIMARY KEY AUTO_INCREMENT             -- ✅
```

**❌ Подзапрос в качестве DEFAULT — недопустим:**
```sql
price DECIMAL(10,2) DEFAULT (SELECT AVG(price) FROM Books)  -- ❌ ERROR
```

**❌ CHECK на уровне поля ссылается на другое поле:**
```sql
price DECIMAL(10,2) CHECK (price > cost)  -- ❌ может не сработать как ожидается
-- Вынеси в табличное ограничение:
CONSTRAINT price_above_cost CHECK (price > cost)  -- ✅
```

---

## ✅ Главные правила

✅ `IF NOT EXISTS` — защита от ошибок при повторном запуске  
✅ `AUTO_INCREMENT` — только для `INT PRIMARY KEY`, несовместим с `DEFAULT`  
✅ `CONSTRAINT имя` — именуй ограничения для удобного удаления  
✅ Составной `PRIMARY KEY` / `FOREIGN KEY` — выносится в отдельное определение  
✅ `DEFAULT` может ссылаться на другие поля записи, но не на подзапрос  
✅ `CHECK` на уровне поля — только своё поле; для нескольких полей — `CONSTRAINT`  
✅ Внешний ключ и поле, на которое он ссылается — должны совпадать по типу  
✅ Несколько ограничений в поле — через пробел, не через запятую  
✅ Таблица обязана иметь хотя бы одно поле — без полей создать нельзя  
✅ DDL необратимо — нет `ROLLBACK` для `CREATE TABLE`

---

## 🔗 Связанные темы

- [[01.1 — 🔧 ALTER, DROP, RENAME — изменение таблиц]]
- [[02 — ➕ INSERT — добавление данных]]
- [[04 — 🗑️ DELETE — удаление данных]]
- [[05 — 🚀 Индексы]]

---

#sql/ddl #create #администрирование
