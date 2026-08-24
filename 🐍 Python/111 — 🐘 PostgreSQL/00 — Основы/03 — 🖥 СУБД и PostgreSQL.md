---
tags: [postgresql, основы]
difficulty: beginner
---

# 🖥 СУБД и PostgreSQL — памятка

> PostgreSQL — свободная объектно-реляционная СУБД. Для работы с ней используется клиент `psql` или графические инструменты (pgAdmin, DBeaver). Язык запросов — SQL с расширениями PostgreSQL.

## Содержание

- [[#🟢 Установка и запуск|Установка и запуск]]
- [[#🔵 psql — основные команды|psql]]
- [[#🔴 Структура PostgreSQL|Структура]]

---

## 🟢 Установка и запуск

```bash
# macOS (Homebrew)
brew install postgresql
brew services start postgresql

# Ubuntu/Debian
sudo apt install postgresql
sudo systemctl start postgresql

# Подключение к базе
psql -U postgres -d mydb
psql -h localhost -p 5432 -U postgres
```

---

## 🔵 psql — основные команды

| Команда | Что делает |
|---|---|
| `\l` | Список баз данных |
| `\c dbname` | Подключиться к базе |
| `\dt` | Список таблиц в схеме |
| `\d tablename` | Структура таблицы |
| `\dn` | Список схем |
| `\df` | Список функций |
| `\du` | Список пользователей |
| `\i file.sql` | Выполнить SQL из файла |
| `\q` | Выйти из psql |
| `\?` | Справка по командам psql |
| `\h SELECT` | Справка по SQL-команде |

---

## 🔴 Структура PostgreSQL

```
PostgreSQL-сервер
└── База данных (database)
    ├── Схема public (по умолчанию)
    │   ├── Таблицы
    │   ├── Представления
    │   ├── Функции
    │   └── Индексы
    └── Схема myschema
        └── ...
```

**Схема** — это namespace (пространство имён) внутри базы данных. По умолчанию все объекты создаются в схеме `public`. Разные схемы позволяют изолировать объекты друг от друга.

```sql
-- Создать схему
CREATE SCHEMA analytics;

-- Создать таблицу в конкретной схеме
CREATE TABLE analytics.reports (...);

-- Обратиться к таблице через схему
SELECT * FROM analytics.reports;
```

---

## 🔗 Связанные темы

- [[01 — 🗄 Базы данных]]
- [[../05 — 🛠 Администрирование/07 — 📐 Схемы]]
- [[../06 — 🐘 PostgreSQL-специфика/05 — 🖥 psql — клиент командной строки]]

---

#postgresql/основы
