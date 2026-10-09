---
tags: [python, pandas, pivot_table, melt, широкий, длинный]
difficulty: intermediate
---

# 🔄 pivot_table и melt

> `pivot_table` превращает «длинную» таблицу в «широкую» (значения одной колонки становятся новыми колонками). `melt` делает обратное — разворачивает «широкую» таблицу в «длинную». Это два противоположных преобразования формы данных.

## Содержание

- [[#Справка|Справка]]
- [[#📐 Длинный и широкий формат|Длинный и широкий формат]]
- [[#🔀 pivot_table|pivot_table]]
- [[#📏 pivot_table с несколькими агрегатами|pivot_table с несколькими агрегатами]]
- [[#🔃 melt — обратное превращение|melt — обратное превращение]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

---

## Справка

| Функция | Делает | Направление |
|---|---|---|
| `pd.pivot_table(df, values, index, columns, aggfunc)` | длинная → широкая | значения колонки становятся заголовками |
| `pd.melt(df, id_vars, value_vars)` | широкая → длинная | заголовки колонок становятся значениями |

| Параметр pivot_table | Значение |
|---|---|
| `values` | что агрегировать (числовая колонка) |
| `index` | что станет строками результата |
| `columns` | что станет колонками результата |
| `aggfunc` | как агрегировать (по умолчанию `'mean'`) |

---

## 📐 Длинный и широкий формат

**Длинный формат** (long) — каждая комбинация признаков в своей строке, похоже на то, как данные обычно лежат в SQL-таблице:

```
   date        city     metric
0  2024-01     London   sales      100
1  2024-01     Paris    sales       80
2  2024-02     London   sales      120
3  2024-02     Paris    sales       90
```

**Широкий формат** (wide) — одна строка на дату, города стали отдельными колонками:

```
   date        London   Paris
0  2024-01        100      80
1  2024-02        120      90
```

Длинный формат удобен для хранения и группировки, широкий — для чтения глазами и для графиков (один ряд на колонку).

---

## 🔀 pivot_table

```python
df = pd.DataFrame({
    'date': ['2024-01', '2024-01', '2024-02', '2024-02'],
    'city': ['London', 'Paris', 'London', 'Paris'],
    'sales': [100, 80, 120, 90]
})

pivot = pd.pivot_table(df, values='sales', index='date', columns='city')
#           city    London  Paris
# date
# 2024-01            100     80
# 2024-02            120     90
```

Если на пересечении `index`/`columns` оказалось **несколько** строк исходных данных — `pivot_table` их **агрегирует** (по умолчанию средним):

```python
df2 = pd.DataFrame({
    'city': ['London', 'London', 'Paris'],
    'month': ['Jan', 'Jan', 'Jan'],
    'sales': [100, 120, 80]
})

pd.pivot_table(df2, values='sales', index='month', columns='city', aggfunc='sum')
#        city  London  Paris
# month
# Jan            220     80    ← 100+120 просуммированы, т.к. обе строки — London/Jan
```

> 💡 В этом и отличие от `pivot()` (без `_table`) — простой `pivot` не умеет агрегировать и упадёт с ошибкой, если на одну ячейку попадёт больше одного значения. `pivot_table` почти всегда безопаснее.

---

## 📏 pivot_table с несколькими агрегатами

```python
pd.pivot_table(df, values='sales', index='date', columns='city',
                aggfunc=['sum', 'mean'])
#              sum              mean
# city      London  Paris   London  Paris
# date
# 2024-01      100     80    100.0   80.0
# 2024-02      120     90    120.0   90.0
```

Несколько значений одновременно:

```python
pd.pivot_table(df, values=['sales', 'profit'], index='date', columns='city')
```

С итоговой строкой/колонкой «Всего»:

```python
pd.pivot_table(df, values='sales', index='date', columns='city',
                aggfunc='sum', margins=True, margins_name='Итого')
```

---

## 🔃 melt — обратное превращение

`melt` разворачивает широкую таблицу обратно в длинную — полезно, например, когда данные выгружены из Excel в виде «месяц как колонка», а для анализа или графика нужен длинный формат.

```python
wide = pd.DataFrame({
    'city': ['London', 'Paris'],
    'Jan': [100, 80],
    'Feb': [120, 90]
})
#     city  Jan  Feb
# 0  London  100  120
# 1   Paris   80   90

long = pd.melt(wide, id_vars='city', value_vars=['Jan', 'Feb'],
               var_name='month', value_name='sales')
#     city month  sales
# 0  London  Jan    100
# 1   Paris  Jan     80
# 2  London  Feb    120
# 3   Paris  Feb     90
```

- `id_vars` — колонки, которые остаются как есть (не разворачиваются)
- `value_vars` — колонки, которые нужно «сложить» в одну колонку значений
- `var_name`/`value_name` — как назвать новые колонки (по умолчанию `variable`/`value`)

Если `value_vars` не указать — развернутся все колонки, кроме перечисленных в `id_vars`.

---

## ⚡ Быстрые примеры

```python
pd.pivot_table(df, values='sales', index='date', columns='city')              # среднее по умолчанию
pd.pivot_table(df, values='sales', index='date', columns='city', aggfunc='sum')
pd.pivot_table(df, values='sales', index='date', columns='city', aggfunc='sum', margins=True)
pd.melt(df, id_vars='id', value_vars=['Jan', 'Feb', 'Mar'])                     # широкая → длинная
df.pivot(index='date', columns='city', values='sales')                          # без агрегации (упадёт при дублях)
```

---

## 💡 Практические замечания

- `pivot_table` = `groupby` + разворот результата в колонки; по сути это удобная обёртка над `groupby`, когда нужна именно сводная таблица, а не `Series`
- По умолчанию `aggfunc='mean'` — если нужна сумма или количество, это нужно указать явно, иначе результат будет неожиданно средним
- `melt` — обратная операция к `pivot_table`/`pivot`; связка «`melt`, почистить, `pivot_table`» — частый паттерн при работе с «неряшливыми» Excel-выгрузками
- `margins=True` — быстрый способ получить итоговую строку/колонку без отдельного подсчёта

---

## ⚠️ Частые ошибки

**❌ Забыть указать `aggfunc`, когда нужна не средняя, а сумма:**
```python
pd.pivot_table(df, values='sales', index='date', columns='city')
# по умолчанию aggfunc='mean' — числа могут выглядеть правдоподобно, но это не сумма!
pd.pivot_table(df, values='sales', index='date', columns='city', aggfunc='sum')   # ✅
```

**❌ Использовать `pivot()` вместо `pivot_table()`, когда в данных возможны дубли:**
```python
df.pivot(index='date', columns='city', values='sales')
# ❌ ValueError: Index contains duplicate entries, cannot reshape
pd.pivot_table(df, index='date', columns='city', values='sales', aggfunc='sum')   # ✅ агрегирует дубли
```

**❌ Не указать `value_vars` в `melt` и развернуть лишние колонки:**
```python
pd.melt(df, id_vars='city')
# развернёт ВСЕ колонки, кроме city — может быть не то, что ожидалось
pd.melt(df, id_vars='city', value_vars=['Jan', 'Feb'])   # ✅ явно что разворачивать
```

---

## ✅ Главные правила

✅ `pivot_table` — длинная таблица → широкая, с агрегацией дублей  
✅ `melt` — широкая таблица → длинная, обратная операция  
✅ `aggfunc` по умолчанию `'mean'` — для суммы или count указывай явно  
✅ `pivot()` (без `_table`) падает на дублях, `pivot_table()` — агрегирует их  
✅ `id_vars` — что остаётся, `value_vars` — что разворачивается в `melt`  

---

## 🔗 Связанные темы

- [[00 — 🧮 groupby и agg]]
- [[02 — 🔗 merge, join, concat]]
- [[00 — 🐼 Series и DataFrame]]

---

#python/pandas #pivot_table #melt #широкий_длинный
