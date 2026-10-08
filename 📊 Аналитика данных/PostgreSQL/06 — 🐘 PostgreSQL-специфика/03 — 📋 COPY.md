---
tags: [postgresql, специфика, copy]
difficulty: intermediate
---

# 📋 COPY — памятка

> `COPY` — самый быстрый способ импорта/экспорта данных в PostgreSQL. В разы быстрее `INSERT`. Поддерживает CSV, TSV и бинарный формат.

## Содержание

- [[#Справка|Справка]]
- [[#🟢 Экспорт данных|Экспорт]]
- [[#🔵 Импорт данных|Импорт]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Синтаксис | Что делает |
|---|---|
| `COPY t TO file` | экспорт в файл (на сервере) |
| `COPY t FROM file` | импорт из файла (на сервере) |
| `\copy t TO file` | экспорт (в psql, на клиенте) |
| `\copy t FROM file` | импорт (в psql, на клиенте) |

---

## 🟢 Экспорт данных

```sql
-- Экспорт всей таблицы в CSV
COPY Books TO '/tmp/books.csv' CSV HEADER;

-- Экспорт с разделителем
COPY Books TO '/tmp/books.tsv' DELIMITER E'\t' HEADER;

-- Экспорт результата запроса
COPY (SELECT title, price FROM Books WHERE price > 10)
TO '/tmp/expensive.csv' CSV HEADER;

-- В psql (файл на стороне клиента)
\copy Books TO 'books.csv' CSV HEADER
```

---

## 🔵 Импорт данных

```sql
-- Импорт CSV
COPY Books (title, author, price)
FROM '/tmp/books.csv' CSV HEADER;

-- В psql (файл на стороне клиента)
\copy Books (title, author, price) FROM 'books.csv' CSV HEADER

-- NULL-значения
COPY Books FROM '/tmp/books.csv' CSV HEADER NULL AS 'NULL';
```

---

## ✅ Главные правила

✅ `COPY` — серверный файл (требует права superuser)  
✅ `\copy` в psql — клиентский файл (без особых прав)  
✅ `CSV HEADER` — первая строка с именами полей  
✅ Быстрее INSERT при массовой загрузке данных  

---

## 🔗 Связанные темы

- [[../05 — 🛠 Администрирование/02 — ➕ INSERT]]

---

#postgresql/специфика #copy
