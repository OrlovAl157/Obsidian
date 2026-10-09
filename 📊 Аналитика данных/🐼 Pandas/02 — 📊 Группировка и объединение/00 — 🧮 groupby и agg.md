---
tags: [python, pandas, groupby, agg, группировка]
difficulty: intermediate
---

# 🧮 groupby и agg

> `groupby` разбивает `DataFrame` на группы по значению колонки, `agg` считает статистику по каждой группе. Это прямой аналог `GROUP BY` + агрегатных функций в SQL.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🔢 Базовая группировка|Базовая группировка]]
- [[#🧮 agg — несколько статистик|agg — несколько статистик]]
- [[#🔗 Группировка по нескольким колонкам|Группировка по нескольким колонкам]]
- [[#🏷 Именованная агрегация|Именованная агрегация]]
- [[#🔄 transform — агрегат обратно в исходные строки|transform]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

---

## Справка

| SQL | pandas |
|---|---|
| `GROUP BY col` | `df.groupby('col')` |
| `SELECT col, COUNT(*) ... GROUP BY col` | `df.groupby('col').size()` |
| `SELECT col, SUM(x) ... GROUP BY col` | `df.groupby('col')['x'].sum()` |
| `HAVING COUNT(*) > 5` | `.filter(lambda g: len(g) > 5)` |

| Метод агрегации | Что считает |
|---|---|
| `.sum()` | сумма |
| `.mean()` | среднее |
| `.count()` | число непустых значений |
| `.size()` | число строк в группе (включая NaN) |
| `.min()` / `.max()` | минимум / максимум |
| `.agg([...])` | несколько статистик сразу |

---

## 📊 Общая схема

```
df.groupby('department')['salary'].sum()

   ┌─────────────┐        ┌──────────────┐
   │ department  │        │  department  │
   │ IT   50000  │        │  IT   95000  │  ← сумма по группе IT
   │ IT   45000  │   →    │  HR   40000  │  ← сумма по группе HR
   │ HR   40000  │        └──────────────┘
   └─────────────┘
```

`groupby` сам по себе ничего не считает — он только группирует. Результат нужно получить, применив агрегирующий метод (`.sum()`, `.mean()` и т.д.).

```python
df.groupby('department')            # GroupBy-объект — промежуточное состояние, не таблица
df.groupby('department')['salary']  # то же, но выбрали одну колонку
df.groupby('department')['salary'].sum()   # ✅ вот теперь результат — Series
```

---

## 🔢 Базовая группировка

```python
df = pd.DataFrame({
    'department': ['IT', 'IT', 'HR', 'HR', 'IT'],
    'salary': [50000, 45000, 40000, 42000, 55000]
})

df.groupby('department')['salary'].sum()
# department
# HR     82000
# IT    150000

df.groupby('department')['salary'].mean()
# department
# HR    41000.0
# IT    50000.0

df.groupby('department').size()      # число строк в каждой группе
# department
# HR    2
# IT    3
```

> 💡 Результат группировки — индекс становится значением колонки, по которой группировали (`department`). Чтобы вернуть его обратно в обычную колонку — `.reset_index()`, как и после `set_index`.

```python
df.groupby('department')['salary'].sum().reset_index()
#   department   salary
# 0         HR    82000
# 1         IT   150000
```

---

## 🧮 agg — несколько статистик

```python
df.groupby('department')['salary'].agg(['sum', 'mean', 'count'])
#              sum     mean  count
# department
# HR         82000  41000.0      2
# IT        150000  50000.0      3
```

Можно применить разные агрегаты к разным колонкам:

```python
df.groupby('department').agg({
    'salary': 'sum',
    'name': 'count'
})
```

Своя функция тоже допустима:

```python
df.groupby('department')['salary'].agg(lambda x: x.max() - x.min())   # размах зарплат
```

---

## 🔗 Группировка по нескольким колонкам

```python
df = pd.DataFrame({
    'department': ['IT', 'IT', 'HR', 'HR'],
    'level': ['junior', 'senior', 'junior', 'senior'],
    'salary': [40000, 70000, 35000, 60000]
})

df.groupby(['department', 'level'])['salary'].mean()
# department  level
# HR          junior    35000.0
#             senior    60000.0
# IT          junior    40000.0
#             senior    70000.0
```

Результат — `Series` с **многоуровневым индексом** (`MultiIndex`). Если нужна плоская таблица — `reset_index()`:

```python
df.groupby(['department', 'level'])['salary'].mean().reset_index()
#   department   level   salary
# 0         HR  junior  35000.0
# 1         HR  senior  60000.0
# 2         IT  junior  40000.0
# 3         IT  senior  70000.0
```

---

## 🏷 Именованная агрегация

Способ сразу задать и агрегат, и имя итоговой колонки — удобнее, чем переименовывать после:

```python
df.groupby('department').agg(
    total_salary=('salary', 'sum'),
    avg_salary=('salary', 'mean'),
    employees=('salary', 'count')
)
#              total_salary  avg_salary  employees
# department
# HR                  82000     41000.0          2
# IT                 150000     50000.0          3
```

Без этого пришлось бы делать `.agg(['sum', 'mean', 'count'])`, а потом вручную переименовывать колонки `salary_sum`, `salary_mean` и т.д.

---

## 🔄 transform — агрегат обратно в исходные строки

`agg` сжимает данные (одна строка на группу). `transform` считает то же самое, но **возвращает результат обратно на каждую исходную строку** — без схлопывания таблицы.

```python
df['dept_avg_salary'] = df.groupby('department')['salary'].transform('mean')
#   department   salary  dept_avg_salary
# 0         IT    40000          55000.0
# 1         IT    70000          55000.0
# 2         HR    35000          47500.0
# 3         HR    60000          47500.0
```

Полезно, чтобы сравнить значение строки со средним по её группе:

```python
df['above_dept_avg'] = df['salary'] > df['dept_avg_salary']
```

---

## ⚡ Быстрые примеры

```python
df.groupby('col')['x'].sum()                      # сумма x по группам col
df.groupby('col').size()                           # число строк в группе
df.groupby('col')['x'].agg(['sum', 'mean'])         # несколько статистик
df.groupby(['a', 'b'])['x'].mean()                  # группировка по двум колонкам
df.groupby('col').agg(total=('x', 'sum'))           # именованная агрегация
df.groupby('col')['x'].transform('mean')            # агрегат обратно на каждую строку
df.groupby('col')['x'].sum().reset_index()          # результат как плоская таблица
df.groupby('col').filter(lambda g: len(g) > 5)      # аналог HAVING
```

---

## 💡 Практические замечания

- `groupby` сам по себе — промежуточный объект, результат появляется только после агрегирующего метода
- Группировка по нескольким колонкам даёт `MultiIndex` — `reset_index()` превращает его обратно в обычные колонки
- Именованная агрегация (`agg(name=('col', 'func'))`) — более читаемый способ, чем агрегировать и потом переименовывать
- `transform` — когда нужно сравнить строку со статистикой её группы, не теряя исходные строки; `agg` — когда нужна именно сводная таблица
- Аналог SQL `HAVING` — `.filter(lambda g: условие)`, применяется к целым группам, а не к строкам

---

## ⚠️ Частые ошибки

**❌ Забыть, что `groupby()` без агрегата — не результат:**
```python
result = df.groupby('department')
print(result)   # <pandas.core.groupby.DataFrameGroupBy object> — не таблица
result = df.groupby('department')['salary'].sum()   # ✅ вот результат
```

**❌ Путать `agg` и `transform`:**
```python
df.groupby('dept')['salary'].agg('mean')        # меньше строк — одна на группу
df.groupby('dept')['salary'].transform('mean')  # столько же строк, сколько в df
```

**❌ Забыть `reset_index()` перед дальнейшей обработкой как с обычной таблицей:**
```python
grouped = df.groupby('dept')['salary'].sum()
grouped['IT']        # работает как у Series с индексом
grouped[0]            # ❌ KeyError — индекс не числовой
grouped.reset_index()['salary'][0]   # ✅ после reset_index — обычный DataFrame
```

---

## ✅ Главные правила

✅ `groupby(...)` — только группирует, агрегат (`sum`/`mean`/...) даёт результат  
✅ `agg` — сводная таблица (меньше строк), `transform` — тот же результат на каждой исходной строке  
✅ Именованная агрегация `agg(name=('col', 'func'))` — сразу с понятными названиями колонок  
✅ Группировка по нескольким колонкам → `MultiIndex` → `reset_index()` для плоской таблицы  
✅ `.filter(lambda g: условие)` — аналог SQL `HAVING`, работает на уровне целых групп  

---

## 🔗 Связанные темы

- [[01 — 🔄 pivot_table и melt]]
- [[02 — 🔗 merge, join, concat]]
- [[06 — 🧮 GROUP BY и HAVING]]

---

#python/pandas #groupby #agg #группировка
