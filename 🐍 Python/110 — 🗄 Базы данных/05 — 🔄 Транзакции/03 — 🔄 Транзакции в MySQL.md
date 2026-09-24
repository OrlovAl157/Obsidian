---
tags: [sql, mysql, транзакции, commit, rollback, savepoint]
difficulty: intermediate
---

# 🔄 Транзакции в MySQL

> В MySQL транзакция начинается с `START TRANSACTION` и завершается `COMMIT` (зафиксировать) или `ROLLBACK` (отменить). Поведение настраивается уровнем изоляции, режимом доступа и режимом автофиксации.

## Содержание

- [[#Справка|Справка]]
- [[#🚦 Основные операторы|Основные операторы]]
- [[#⚡ Режим автофиксации|Режим автофиксации]]
- [[#📍 Точки сохранения|Точки сохранения]]
- [[#🧱 DDL и неявный COMMIT|DDL и неявный COMMIT]]
- [[#🔧 Настройка транзакций|Настройка транзакций]]
- [[#🔀 BEGIN и START TRANSACTION|BEGIN и START TRANSACTION]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

---

## Справка

| Оператор | Что делает |
|---|---|
| `START TRANSACTION` | начинает транзакцию (отключает автофиксацию до её конца) |
| `COMMIT` | фиксирует все изменения |
| `ROLLBACK` | отменяет все изменения транзакции |
| `SAVEPOINT имя` | создаёт точку сохранения |
| `ROLLBACK TO имя` | откатывает до точки, транзакция остаётся **открытой** |
| `SET TRANSACTION ...` | задаёт уровень изоляции и/или режим доступа |

| Настройка | Значения | По умолчанию |
|---|---|---|
| Уровень изоляции | `READ UNCOMMITTED`, `READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE` | `REPEATABLE READ` |
| Режим доступа | `READ WRITE`, `READ ONLY` | `READ WRITE` |
| `autocommit` | `1` / `0` | `1` |

| Область действия `SET TRANSACTION` | Что затрагивает |
|---|---|
| без ключевого слова | только **следующую** транзакцию |
| `SESSION` | все транзакции текущего подключения |
| `GLOBAL` | все **новые** подключения (нужны права администратора) |

| Переменная | Что показывает |
|---|---|
| `@@autocommit` | режим автофиксации (1/0) |
| `@@transaction_isolation` | текущий уровень изоляции, например `REPEATABLE-READ` |
| `@@transaction_read_only` | режим доступа: `0` — READ WRITE, `1` — READ ONLY |

---

## 🚦 Основные операторы

Примеры — на БД электронной библиотеки: `Books` (id, author_id, title, price, quantity), `BooksRental` (id, book_id, user_id, quantity), `Users`.

**✅ Фиксация.** Выдаём книгу пользователю и уменьшаем остаток:

```sql
START TRANSACTION;

INSERT INTO BooksRental (book_id, user_id, quantity)
VALUES (5, 1, 1);           -- → запись об аренде

UPDATE Books
SET quantity = quantity - 1
WHERE id = 5;               -- → остаток книги 5: 1 → 0

COMMIT;                     -- → обе операции зафиксированы
```

**↩️ Откат.** Те же действия, но отменяем:

```sql
START TRANSACTION;

INSERT INTO BooksRental (book_id, user_id, quantity)
VALUES (3, 2, 2);

UPDATE Books
SET quantity = quantity - 2
WHERE id = 3;

ROLLBACK;                   -- → обе таблицы как до START TRANSACTION
```

**Что будет, если не завершить транзакцию:**

- изменения видны только внутри текущего соединения, другие пользователи их не видят
- при разрыве соединения или конце сессии транзакция автоматически **отменяется**
- повторный `START TRANSACTION` при активной транзакции **неявно выполняет `COMMIT`**

---

## ⚡ Режим автофиксации

По умолчанию каждый оператор в MySQL — **отдельная транзакция**:

```sql
-- Один оператор в режиме autocommit = 1 равносилен:
START TRANSACTION;
UPDATE Books SET quantity = quantity - 1 WHERE id = 3;
COMMIT;
```

```sql
SELECT @@autocommit;
-- → 1   (автофиксация включена)

SET @@autocommit = 0;       -- отключить: операции копятся в открытой транзакции
UPDATE Books SET quantity = quantity - 1 WHERE id = 3;
-- изменение не зафиксировано, другие транзакции его не видят
COMMIT;                     -- → зафиксировать; MySQL сразу откроет новую транзакцию

SET @@autocommit = 1;       -- включить обратно
```

- при `autocommit = 0` после `COMMIT` или `ROLLBACK` MySQL **автоматически начинает новую** транзакцию
- `START TRANSACTION` отключает автофиксацию **до завершения** этой транзакции

---

## 📍 Точки сохранения

Нужны, когда надо отменить **часть** транзакции, а не всё.

```sql
SAVEPOINT имя_точки;        -- запомнить текущее состояние
ROLLBACK TO имя_точки;      -- откатиться до неё (транзакция остаётся открытой!)
```

```sql
START TRANSACTION;

UPDATE Books SET quantity = quantity - 1 WHERE id = 1;   -- → 3 → 2
SAVEPOINT point1;

UPDATE Books SET quantity = quantity - 1 WHERE id = 2;   -- → 2 → 1
SAVEPOINT point2;

UPDATE Books SET quantity = quantity - 1 WHERE id = 3;   -- → 5 → 4

ROLLBACK TO point2;         -- → отменено только последнее обновление (id = 3)
COMMIT;                     -- → id 1 и id 2 зафиксированы, id 3 без изменений
```

- после `ROLLBACK TO` транзакцию нужно завершить `COMMIT` (или `ROLLBACK`)
- точка с уже существующим именем **заменяет** старую
- точек можно создавать сколько угодно
- типичный случай: сначала добавляем пользователя, если его нет, потом оформляем аренду. При ошибке аренды откатываемся к точке после добавления пользователя, и он остаётся в базе

---

## 🧱 DDL и неявный COMMIT

Операторы, меняющие **структуру** БД (`CREATE TABLE`, `ALTER TABLE`, `DROP TABLE` и другие DDL), нельзя откатить: они **завершают текущую транзакцию** и фиксируют всё сделанное.

```sql
START TRANSACTION;

INSERT INTO BooksRental (book_id, user_id, quantity)
VALUES (5, 1, 1);           -- (1)

DROP TABLE IF EXISTS Users; -- ⚡ неявный COMMIT: (1) зафиксирован, Users удалена

UPDATE Books
SET quantity = quantity - 1
WHERE title = 'Haunted';    -- (2) уже в новой транзакции (режим autocommit)

ROLLBACK;                   -- ❌ откатывать нечего: активной транзакции нет
```

Результат: запись об аренде осталась, таблица `Users` удалена, остаток книги уменьшен. `ROLLBACK` не отменил ничего.

> ⚠️ Не включай DDL-операторы в транзакции.

---

## 🔧 Настройка транзакций

### 📶 Уровень изоляции

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;
START TRANSACTION;

INSERT INTO BooksRental (book_id, user_id, quantity)
VALUES (1, 2, 1);

COMMIT;                     -- → транзакция выполнена на уровне READ COMMITTED
```

Об уровнях и аномалиях — [[02 — 🔒 Изоляция транзакций]].

### 🔏 Режим доступа

- `READ WRITE` — чтение и запись (по умолчанию)
- `READ ONLY` — только чтение, может ускорить работу, когда заведомо известно, что данные не меняются

```sql
SET TRANSACTION READ ONLY;
START TRANSACTION;

UPDATE Books SET price = price * 0.9;
-- ❌ ERROR 1792: Cannot execute statement in a READ ONLY transaction
```

### 🌐 Область действия

```sql
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;  -- все НОВЫЕ подключения
SET SESSION TRANSACTION READ ONLY;                      -- только текущее подключение

-- Без ключевого слова: только следующая транзакция, потом снова настройки сессии
-- Обе характеристики можно задать одним оператором через запятую
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ, READ ONLY;
```

- `SET GLOBAL` не меняет уже открытые подключения
- для `GLOBAL` нужны права администратора

### 🔎 Просмотр текущих настроек

```sql
SELECT @@transaction_isolation, @@transaction_read_only;
-- → REPEATABLE-READ | 0
```

### 🚫 Нельзя менять в уже начатой транзакции

```sql
START TRANSACTION;
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- ❌ ERROR 1568: Transaction characteristics can't be changed while a transaction is in progress
```

Настраивай **до** `START TRANSACTION`.

---

## 🔀 BEGIN и START TRANSACTION

`BEGIN` — альтернатива `START TRANSACTION`, но **в функциях, процедурах и триггерах** он означает начало блока `BEGIN ... END`. Поэтому внутри этих объектов начинать транзакцию нужно только через `START TRANSACTION`.

```sql
DELIMITER //
CREATE PROCEDURE demo()
BEGIN                       -- начало блока процедуры
    START TRANSACTION;      -- ✅ начало транзакции
    -- операции
    COMMIT;
END //
DELIMITER ;
```

---

## 💡 Практические замечания

- Транзакции должны быть как можно **короче**: длинные дают блокировки и конфликты
- Всегда завершай транзакцию явно: `COMMIT` или `ROLLBACK`
- В приложении транзакциями управляет драйвер или библиотека языка, а в MySQL то же самое можно делать внутри хранимых процедур
- Транзакции работают на движке InnoDB
- `@@autocommit` и `@@transaction_isolation` — системные переменные, о них см. [[07 — 🧮 Переменные]]

---

## ⚠️ Частые ошибки

**❌ Забыть завершить транзакцию.** Изменения останутся невидимыми для других и пропадут при разрыве соединения.

**❌ Начать новую транзакцию, не завершив старую.** `START TRANSACTION` при активной транзакции неявно делает `COMMIT`.

**❌ Использовать `ROLLBACK TO` как завершение.**
```sql
ROLLBACK TO point1;
-- ❌ транзакция всё ещё открыта!
COMMIT;  -- ✅ нужно завершить
```

**❌ DDL внутри транзакции.** `DROP TABLE` и другие фиксируют всё неявно, откат не сработает.

**❌ `BEGIN` в процедуре вместо `START TRANSACTION`.** MySQL воспримет его как начало блока, а не транзакции.

**❌ `SET TRANSACTION` после `START TRANSACTION`.** Ошибка 1568.

**❌ Забыть, что `autocommit = 0` действует на всю сессию.** Без `COMMIT` ничего не сохранится, даже если операторы выполнились без ошибок.

---

## ✅ Главные правила

✅ `START TRANSACTION` → операции → `COMMIT` или `ROLLBACK`  
✅ По умолчанию `autocommit = 1`: каждый оператор — своя транзакция  
✅ `START TRANSACTION` отключает автофиксацию до конца транзакции  
✅ Незавершённая транзакция при разрыве соединения отменяется  
✅ `ROLLBACK TO точка` не завершает транзакцию  
✅ DDL неявно фиксирует транзакцию — не смешивай  
✅ `SET TRANSACTION` — до начала транзакции; без `GLOBAL`/`SESSION` действует на одну следующую  
✅ В процедурах — `START TRANSACTION`, а не `BEGIN`  
✅ Уровень по умолчанию — `REPEATABLE READ`, режим — `READ WRITE`  

---

## 🔗 Связанные темы

- [[01 — 🔄 Транзакции и ACID]]
- [[02 — 🔒 Изоляция транзакций]]
- [[06 — 📜 Процедуры]]
- [[07 — 🧮 Переменные]]
- [[04 — 🗑️ DELETE — удаление данных]]

---

#sql/транзакции #mysql #commit #rollback #savepoint
