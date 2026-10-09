---
tags: [python, pandas, merge, join, concat, объединение]
difficulty: intermediate
---

# 🔗 merge, join, concat

> `merge` объединяет две таблицы по ключу — прямой аналог SQL `JOIN`. `concat` склеивает таблицы одну под другой или рядом, без привязки по ключу. `join` — сокращённая форма `merge` для объединения по индексу.

## Содержание

- [[#Справка|Справка]]
- [[#🔗 merge — аналог SQL JOIN|merge — аналог SQL JOIN]]
- [[#🎯 Типы соединений how|Типы соединений how]]
- [[#🏷 Разные имена ключей|Разные имена ключей]]
- [[#📎 join — объединение по индексу|join — объединение по индексу]]
- [[#➕ concat — склейка таблиц|concat — склейка таблиц]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

---

## Справка

| pandas | SQL | Что делает |
|---|---|---|
| `pd.merge(a, b, on='id')` | `INNER JOIN ... ON` | объединяет по ключу |
| `how='inner'` | `INNER JOIN` | только совпавшие строки |
| `how='left'` | `LEFT JOIN` | все строки левой + совпавшие правой |
| `how='right'` | `RIGHT JOIN` | все строки правой + совпавшие левой |
| `how='outer'` | `FULL OUTER JOIN` | все строки обеих таблиц |
| `a.join(b)` | — | merge по индексу, а не по колонке |
| `pd.concat([a, b])` | `UNION ALL` | склейка таблиц одна под другой |

---

## 🔗 merge — аналог SQL JOIN

```python
authors = pd.DataFrame({
    'author_id': [1, 2, 3],
    'name': ['King', 'Palahniuk', 'Tolkien']
})

books = pd.DataFrame({
    'title': ['It', 'Fight Club', 'The Hobbit'],
    'author_id': [1, 2, 3]
})

pd.merge(books, authors, on='author_id')
#         title  author_id       name
# 0          It          1       King
# 1  Fight Club          2  Palahniuk
# 2  The Hobbit          3    Tolkien
```

`on='author_id'` — ключ, по которому сопоставляются строки, ровно как `ON a.author_id = b.author_id` в SQL.

---

## 🎯 Типы соединений (how)

```python
books2 = pd.DataFrame({
    'title': ['It', 'Unknown Book'],
    'author_id': [1, 99]   # 99 — автора с таким id нет в authors
})

pd.merge(books2, authors, on='author_id', how='inner')
#   title  author_id  name
# 0    It          1  King
# (строка с 99 пропала — нет совпадения)

pd.merge(books2, authors, on='author_id', how='left')
#           title  author_id  name
# 0            It          1  King
# 1  Unknown Book         99   NaN    ← строка сохранена, но name = NaN

pd.merge(books2, authors, on='author_id', how='outer')
# включает и несовпавшие строки с обеих сторон, заполняя NaN где нет пары
```

По умолчанию `how='inner'` — как и в SQL, если не указано иное.

---

## 🏷 Разные имена ключей

Если колонка-ключ называется по-разному в двух таблицах:

```python
pd.merge(books, authors, left_on='author_id', right_on='id')
```

Если в обеих таблицах есть одноимённые неключевые колонки, pandas добавит суффиксы `_x`/`_y`:

```python
pd.merge(a, b, on='id')
# price_x  ← из a
# price_y  ← из b

pd.merge(a, b, on='id', suffixes=('_books', '_orders'))   # понятные суффиксы вместо _x/_y
```

---

## 📎 join — объединение по индексу

`join` — удобная форма `merge`, когда объединение идёт по **индексу**, а не по обычной колонке:

```python
authors_idx = authors.set_index('author_id')
books_idx = books.set_index('author_id')

books_idx.join(authors_idx)
# объединяет по индексу обеих таблиц, аналог merge(..., left_index=True, right_index=True)
```

```python
books.join(authors_idx, on='author_id')
# books соединяется по обычной колонке author_id с индексом authors_idx
```

На практике `merge` используется чаще — он явный и гибкий; `join` удобен, когда таблицы уже проиндексированы по общему ключу.

---

## ➕ concat — склейка таблиц

`concat` не ищет совпадения по ключу — он просто ставит таблицы друг под друга (или рядом), аналог SQL `UNION ALL`.

**По строкам** (одна таблица под другой, `axis=0` по умолчанию):

```python
jan_sales = pd.DataFrame({'city': ['London', 'Paris'], 'sales': [100, 80]})
feb_sales = pd.DataFrame({'city': ['London', 'Paris'], 'sales': [120, 90]})

pd.concat([jan_sales, feb_sales])
#      city  sales
# 0  London    100
# 1   Paris     80
# 0  London    120   ← индекс повторился (0, 1 снова)!
# 1   Paris     90

pd.concat([jan_sales, feb_sales], ignore_index=True)   # ✅ пересчитать индекс 0,1,2,3
```

**По колонкам** (таблицы рядом, `axis=1`):

```python
pd.concat([df_a, df_b], axis=1)
# объединяет по позиции строк (или по индексу, если он не числовой по умолчанию)
```

| `concat` vs `merge` | |
|---|---|
| `concat` | складывает таблицы, не глядя на значения — только по позиции или индексу |
| `merge` | сопоставляет строки по значению ключевой колонки, как `JOIN` |

---

## ⚡ Быстрые примеры

```python
pd.merge(a, b, on='id')                              # INNER JOIN по умолчанию
pd.merge(a, b, on='id', how='left')                    # LEFT JOIN
pd.merge(a, b, left_on='a_id', right_on='b_id')        # разные имена ключа
pd.merge(a, b, on='id', suffixes=('_a', '_b'))         # свои суффиксы вместо _x/_y
a.join(b, on='id')                                      # merge по индексу b
pd.concat([a, b], ignore_index=True)                    # склейка строк, аналог UNION ALL
pd.concat([a, b], axis=1)                                # склейка колонок рядом
```

---

## 💡 Практические замечания

- `merge` — когда нужно сопоставить строки по значению (как SQL `JOIN`); `concat` — когда просто нужно сложить таблицы вместе (как `UNION ALL`)
- По умолчанию `merge` берёт `how='inner'` — строки без пары теряются с обеих сторон; для «ничего не потерять» нужен `how='outer'` или `how='left'`
- После `concat` по строкам индекс почти всегда стоит пересчитать через `ignore_index=True`, иначе появятся дубли меток
- Если объединяемые таблицы из разных источников (SQL + CSV, например) — сначала стоит свериться с их `dtypes`: `merge` по ключам с разными типами (`int` и `str`) просто не найдёт совпадений, без явной ошибки
- Одноимённые неключевые колонки после `merge` получают суффиксы `_x`/`_y` — лучше сразу задавать `suffixes` понятными именами

---

## ⚠️ Частые ошибки

**❌ Ждать, что concat найдёт совпадения по ключу — как merge:**
```python
pd.concat([orders_jan, orders_feb])
# просто ставит строки одну под другой, НЕ сопоставляет по id
```

**❌ Забыть `ignore_index=True` после concat по строкам:**
```python
result = pd.concat([a, b])
result.loc[0]   # ❌ вернёт ДВЕ строки — из a и из b, если оба имели индекс 0
result = pd.concat([a, b], ignore_index=True)   # ✅ индекс 0,1,2,3...
```

**❌ merge по ключам с разными типами данных:**
```python
a['id'] = a['id'].astype(str)
b['id'] = b['id'].astype(int)
pd.merge(a, b, on='id')
# результат пустой или подозрительно маленький — типы не совпали, pandas не ругается явно
```

**❌ Не указать `how`, когда нужны все строки, а не только совпавшие:**
```python
pd.merge(orders, customers, on='customer_id')
# по умолчанию inner — заказы без найденного клиента молча исчезнут
pd.merge(orders, customers, on='customer_id', how='left')   # ✅ все заказы сохранены
```

---

## ✅ Главные правила

✅ `merge` — сопоставление по ключу, аналог SQL `JOIN`; `concat` — склейка без сопоставления, аналог `UNION ALL`  
✅ `how`: `inner` (по умолчанию) теряет несовпавшие строки, `left`/`right`/`outer` сохраняют  
✅ После `concat` по строкам — `ignore_index=True`, чтобы не было дублей индекса  
✅ Перед `merge` сверь `dtypes` ключевых колонок — несовпадение типов даёт пустой результат без ошибки  
✅ `join` — сокращённая форма `merge` специально для объединения по индексу  

---

## 🔗 Связанные темы

- [[00 — 🧮 groupby и agg]]
- [[01 — 🔄 pivot_table и melt]]
- [[04 — 🔗 JOIN - объединение таблиц]]
- [[05 — 🔗 UNION и операции над множествами]]

---

#python/pandas #merge #join #concat #объединение
