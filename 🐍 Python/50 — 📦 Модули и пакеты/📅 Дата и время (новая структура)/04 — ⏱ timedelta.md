---
tags:
  - python
  - тема/модули
  - тема/datetime
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# ⏱ Тип timedelta — памятка

> `timedelta` представляет интервал времени (разницу между двумя моментами) — например, "5 дней" или "3 часа 30 минут". Это единственный из четырёх типов, с которым можно делать полноценную математику: складывать, умножать, делить, сравнивать.

## Содержание

- [[#🟢 Создание|Создание]]
- [[#🔵 Атрибуты|Атрибуты]]
- [[#🔴 Арифметика с датами datetime и date|С датами]]
- [[#🟡 Арифметика между timedelta|Между собой]]
- [[#🟣 Сравнения всех типов|Сравнения]]
- [[#💻 Практические примеры|Практические примеры]]

---

## 🟢 Создание

```python
from datetime import timedelta

timedelta(days=5)                       # 5 дней
timedelta(hours=3, minutes=30)          # 3 часа 30 минут
timedelta(weeks=2, days=3, hours=5)     # можно комбинировать сразу несколько единиц
timedelta(days=-5)                      # отрицательный интервал — "5 дней назад"
```

---

## 🔵 Атрибуты

`timedelta` внутри хранит только 3 числа — дни, секунды и микросекунды. Всё остальное (часы, минуты, недели) при создании автоматически пересчитывается в эти три:

```python
td = timedelta(weeks=1, hours=2)
td.days          # 7 — количество полных дней
td.seconds       # 7200 — ОСТАТОК секунд (не считая дней!), 2 часа = 7200 сек
td.microseconds  # 0

# total_seconds() — самый надёжный способ получить общую длительность
td.total_seconds()  # 615600.0 — вся длительность в секундах, включая дни
```

**Частая ошибка:** `td.seconds` — это НЕ общее число секунд, а только "хвост" после вычета целых дней. Если нужна общая длительность в секундах — всегда используй `total_seconds()`.

---

## 🔴 Арифметика с датами (datetime и date)

| Операция | Результат | Пример |
|---|---|---|
| `datetime + timedelta` | ✅ `datetime` | `dt + timedelta(days=1)` |
| `datetime - timedelta` | ✅ `datetime` | `dt - timedelta(hours=2)` |
| `datetime - datetime` | ⚠️ `timedelta` | `dt1 - dt2` |
| `datetime + datetime` | ❌ Ошибка | нельзя складывать две точки во времени |
| `date + timedelta` | ✅ `date` | `d + timedelta(days=7)` |
| `date - timedelta` | ✅ `date` | `d - timedelta(weeks=1)` |
| `date - date` | ⚠️ `timedelta` | `d1 - d2` |
| `time ± timedelta` | ❌ Ошибка | у `time` нет арифметики, см. [[02 — ⏰ time (тип)]] |

**Логика этой таблицы проще, чем кажется:** `date`/`datetime` — это *точка* на временной шкале, а `timedelta` — *отрезок*. Точка + отрезок = снова точка (логично — сдвинулись во времени). Точка − точка = отрезок (расстояние между ними). А вот точка + точка бессмысленна геометрически — поэтому Python это запрещает.

```python
from datetime import datetime, date, timedelta

now = datetime(2025, 10, 16, 14, 30)

now + timedelta(days=1)                              # 2025-10-17 14:30:00
now + timedelta(days=5, hours=3, minutes=30)          # можно комбинировать сразу
now - timedelta(days=1)                               # 2025-10-15 14:30:00

start = datetime(2025, 10, 16, 10, 0)
end = datetime(2025, 10, 16, 15, 30)
duration = end - start          # timedelta(seconds=19800) = 5ч 30мин
duration.total_seconds()        # 19800.0

# ❌ Недопустимо
# now + datetime(2025, 10, 17)   # TypeError
# now * 2                        # TypeError — на datetime нельзя умножать
```

---

## 🟡 Арифметика между timedelta

Два интервала можно складывать, вычитать, а сам интервал — умножать/делить на число:

```python
from datetime import timedelta

td1 = timedelta(days=5)
td2 = timedelta(hours=12)

td1 + td2        # timedelta(days=5, seconds=43200) — 5 дней 12 часов
td1 - td2        # timedelta(days=4, seconds=43200) — 4 дня 12 часов

one_day = timedelta(days=1)
one_day * 7      # timedelta(days=7) — неделя
one_day * 0.5    # timedelta(hours=12) — можно и на дробное число

week = timedelta(days=7)
week / 7         # timedelta(days=1) — обычное деление, вернёт timedelta
week // 2        # timedelta(days=3) — целочисленное деление (остаток отбрасывается)
```

**Особый случай — деление timedelta НА timedelta:**

```python
week = timedelta(days=7)
day = timedelta(days=1)

week / day       # 7.0 — это float, а не timedelta! "Сколько раз день помещается в неделю"

hour = timedelta(hours=1)
minute = timedelta(minutes=1)
hour / minute    # 60.0
```

Логика та же, что у обычных чисел: `timedelta / timedelta` — это как "10 метров / 2 метра = 5" (число, а не длина). А `timedelta / число` — это "10 метров / 2 = 5 метров" (снова длина).

```python
# Остаток от деления, отрицание, модуль
total = timedelta(days=10)
divisor = timedelta(days=3)
total % divisor         # timedelta(days=1) — как обычный остаток: 10 % 3 = 1

negative = timedelta(days=-5)
abs(negative)            # timedelta(days=5)
future = timedelta(days=5)
-future                  # timedelta(days=-5)
```

---

## 🟣 Сравнения всех типов

Внутри одного типа сравнение работает как ожидается:

```python
from datetime import datetime, date, time, timedelta

datetime(2025, 10, 16) < datetime(2025, 10, 17)   # True
date(2025, 10, 16) == date(2025, 10, 16)           # True
timedelta(days=1) == timedelta(hours=24)           # True — приводится к общей мере
```

**Но нельзя сравнивать РАЗНЫЕ типы между собой** — `datetime` с `date`, `date` с `time` и т.д. Технически это не вызывает ошибку, а просто всегда возвращает `False` для `==`, что легко пропустить как баг:

```python
dt = datetime(2025, 10, 16, 14, 30)
d = date(2025, 10, 16)

dt == d   # False — хотя дата "одна и та же", типы разные, поэтому False
```

---

## 💻 Практические примеры

```python
from datetime import date, datetime, timedelta

# Добавить N рабочих дней (пропуская выходные)
def add_business_days(start_date, days):
    current = start_date
    while days > 0:
        current += timedelta(days=1)
        if current.weekday() < 5:   # 0-4 = Пн-Пт
            days -= 1
    return current

# Найти все понедельники в месяце
def get_mondays(year, month):
    d = date(year, month, 1)
    d += timedelta(days=(7 - d.weekday()) % 7)   # сдвиг к первому понедельнику
    mondays = []
    while d.month == month:
        mondays.append(d)
        d += timedelta(weeks=1)
    return mondays

# Итерация по диапазону дат
start = date(2024, 1, 1)
end = date(2024, 1, 10)
current = start
while current <= end:
    print(current)
    current += timedelta(days=1)
```

---

## 🔗 Связанные темы

- [[00 — 🏠 Обзор]]
- [[01 — 📆 date]]
- [[03 — 🕐 datetime]]
- [[08 — 📊 Справочник методов]]
