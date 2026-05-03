---
tags: sql, database, basics
уровень: базовый
статус: готово
раздел: основы sql
---

# 🗄️ Основы SQL

## 📑 Содержание
1. Извлечение данных
2. ORDER BY — сортировка
3. WHERE — фильтрация данных
4. AND / OR / IN / NOT
5. LIKE — шаблонный поиск
6. Регистрозависимость
7. Вычисляемые поля
8. Объединение значений полей

---

## 1.1 Извлечение данных

```sql
SELECT title, director
FROM Films
LIMIT 5;
```

## 1.2 ORDER BY — сортировка

```sql
SELECT title AS name
FROM Films
ORDER BY name DESC;
```

## 1.3 WHERE — фильтрация данных

```sql
SELECT trackname, artist, streams
FROM Songs
WHERE streams > 100000;
```

## 1.4 AND / OR / IN / NOT

```sql
WHERE streams > 50000 AND place <= 3
WHERE artist = 'Heart' OR artist = 'Kate Bush'
WHERE artist IN ('Heart', 'Kate Bush')
WHERE NOT artist = 'The Sounds'
```

## 1.5 LIKE — шаблонный поиск

- `%` — любая последовательность символов
- `_` — один любой символ

```sql
LIKE 'text%'
LIKE '%text%'
```

## 1.6 Регистрозависимость

```sql
LIKE BINARY '%You%'
CAST('%You%' AS BINARY)
CONVERT('%You%', BINARY)
```

## 1.7 Вычисляемые поля

```sql
SELECT item,
       price * quantity AS revenue
FROM VibeStore;
```

## 1.8 Объединение значений полей

```sql
SELECT CONCAT_WS(', ', item, color,
       CONCAT('size ', size)) AS product
FROM Clothes;
```
