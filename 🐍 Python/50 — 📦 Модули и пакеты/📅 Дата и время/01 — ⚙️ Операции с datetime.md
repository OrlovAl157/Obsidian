---
tags:
  - python
  - тема/модули
  - тема/datetime
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# Операции с datetime - Полная памятка

## Основные правила

### `datetime` операции

|Операция|Результат|Пример|
|:--|:-:|:--|
|`datetime + timedelta`|✅ `datetime`|`dt + timedelta(days=1)`|
|`datetime - timedelta`|✅ `datetime`|`dt - timedelta(hours=2)`|
|`datetime - datetime`|⚠️ `timedelta`|`dt1 - dt2`|
|`datetime + datetime`|❌ Ошибка|Нельзя складывать|
|`datetime * число`|❌ Ошибка|Нельзя умножать|
|`datetime / число`|❌ Ошибка|Нельзя делить|

### `date` операции

|Операция|Результат|Пример|
|:--|:-:|:--|
|`date + timedelta`|✅ `date`|`d + timedelta(days=7)`|
|`date - timedelta`|✅ `date`|`d - timedelta(weeks=1)`|
|`date - date`|⚠️ `timedelta`|`d1 - d2`|
|`date + date`|❌ Ошибка|Нельзя складывать|

### `time` операции

|Операция|Результат|Пример|
|:--|:-:|:--|
|`time + timedelta`|❌ Ошибка|Арифметика недоступна|
|`time - timedelta`|❌ Ошибка|Арифметика недоступна|
|`time - time`|❌ Ошибка|Арифметика недоступна|

### `timedelta` операции

|Операция|Результат|Пример|
|:--|:-:|:--|
|`timedelta + timedelta`|✅ `timedelta`|`td1 + td2`|
|`timedelta - timedelta`|✅ `timedelta`|`td1 - td2`|
|`timedelta * число`|✅ `timedelta`|`td * 2`|
|`timedelta / число`|✅ `timedelta`|`td / 3`|
|`timedelta // число`|✅ `timedelta`|`td // 2`|
|`timedelta % timedelta`|✅ `timedelta`|`td1 % td2`|
|`timedelta / timedelta`|✅ `float`|`td1 / td2`|
|`abs(timedelta)`|✅ `timedelta`|`abs(td)`|
|`-timedelta`|✅ `timedelta`|`-td`|

---

## Примеры с `datetime`

### ✅ `datetime + timedelta` → `datetime`

```python
from datetime import datetime, timedelta

now = datetime(2025, 10, 16, 14, 30)

# Добавляем время
tomorrow = now + timedelta(days=1)
# 2025-10-17 14:30:00

in_3_hours = now + timedelta(hours=3)
# 2025-10-16 17:30:00

next_week = now + timedelta(weeks=1)
# 2025-10-23 14:30:00

complex_add = now + timedelta(days=5, hours=3, minutes=30)
# 2025-10-21 18:00:00
```

### ✅ `datetime - timedelta` → `datetime`

```python
from datetime import datetime, timedelta

now = datetime(2025, 10, 16, 14, 30)

# Вычитаем время
yesterday = now - timedelta(days=1)
# 2025-10-15 14:30:00

two_hours_ago = now - timedelta(hours=2)
# 2025-10-16 12:30:00

last_month = now - timedelta(days=30)
# 2025-09-16 14:30:00
```

### ⚠️ `datetime - datetime` → `timedelta`

```python
from datetime import datetime

start = datetime(2025, 10, 16, 10, 0)
end = datetime(2025, 10, 16, 15, 30)

duration = end - start
# timedelta(seconds=19800)  # 5 часов 30 минут

print(duration.total_seconds())  # 19800.0
print(duration.seconds)          # 19800
```

### ❌ Недопустимые операции

```python
from datetime import datetime, timedelta

dt1 = datetime(2025, 10, 16, 14, 30)
dt2 = datetime(2025, 10, 17, 14, 30)

# dt1 + dt2          # ❌ TypeError
# dt1 * 2            # ❌ TypeError
# dt1 / 2            # ❌ TypeError
```

---

## Примеры с `date`

### ✅ `date + timedelta` → `date`

```python
from datetime import date, timedelta

today = date(2025, 10, 16)

# Добавляем дни
tomorrow = today + timedelta(days=1)
# 2025-10-17

next_week = today + timedelta(weeks=1)
# 2025-10-23

in_100_days = today + timedelta(days=100)
# 2026-01-24
```

### ✅ `date - timedelta` → `date`

```python
from datetime import date, timedelta

today = date(2025, 10, 16)

# Вычитаем дни
yesterday = today - timedelta(days=1)
# 2025-10-15

last_year = today - timedelta(days=365)
# 2024-10-16
```

### ⚠️ `date - date` → `timedelta`

```python
from datetime import date

start = date(2025, 10, 1)
end = date(2025, 10, 16)

diff = end - start
# timedelta(days=15)

print(diff.days)  # 15
```

---

## Примеры с `time`

### ❌ Арифметика недоступна

```python
from datetime import time, timedelta

t = time(14, 30, 45)

# t + timedelta(hours=1)   # ❌ TypeError
# t - timedelta(hours=1)   # ❌ TypeError

t1 = time(14, 30)
t2 = time(15, 45)
# t2 - t1                  # ❌ TypeError
```

**Решение:** Используй `datetime.combine()` для работы с `time`:

```python
from datetime import datetime, time, date, timedelta

t = time(14, 30)
today = date.today()

# Объединяем date и time в datetime
dt = datetime.combine(today, t)

# Теперь можно делать арифметику
new_dt = dt + timedelta(hours=2)
new_time = new_dt.time()  # 16:30:00
```

---

## Примеры с `timedelta`

### ✅ `timedelta + timedelta` → `timedelta`

```python
from datetime import timedelta

td1 = timedelta(days=5)
td2 = timedelta(hours=12)

total = td1 + td2
# timedelta(days=5, seconds=43200)  # 5 дней 12 часов

print(total.days)           # 5
print(total.seconds)        # 43200 (12 часов в секундах)
print(total.total_seconds()) # 475200.0
```

### ✅ `timedelta - timedelta` → `timedelta`

```python
from datetime import timedelta

td1 = timedelta(days=10)
td2 = timedelta(days=3)

diff = td1 - td2
# timedelta(days=7)
```

### ✅ `timedelta * число` → `timedelta`

```python
from datetime import timedelta

one_day = timedelta(days=1)

week = one_day * 7
# timedelta(days=7)

half_day = one_day * 0.5
# timedelta(seconds=43200)  # 12 часов
```

### ✅ `timedelta / число` → `timedelta`

```python
from datetime import timedelta

week = timedelta(days=7)

one_day = week / 7
# timedelta(days=1)

half_week = week / 2
# timedelta(days=3, seconds=43200)  # 3.5 дня
```

### ✅ `timedelta // число` → `timedelta`

```python
from datetime import timedelta

week = timedelta(days=7)

three_days = week // 2
# timedelta(days=3)  # Целочисленное деление
```

### ✅ `timedelta / timedelta` → `float`

```python
from datetime import timedelta

week = timedelta(days=7)
day = timedelta(days=1)

ratio = week / day
# 7.0  ← Получили число, не timedelta!

hour = timedelta(hours=1)
minute = timedelta(minutes=1)

ratio2 = hour / minute
# 60.0
```

### ✅ `timedelta % timedelta` → `timedelta`

```python
from datetime import timedelta

total = timedelta(days=10)
divisor = timedelta(days=3)

remainder = total % divisor
# timedelta(days=1)  # 10 % 3 = 1
```

### ✅ `abs(timedelta)` → `timedelta`

```python
from datetime import timedelta

negative = timedelta(days=-5)
positive = abs(negative)
# timedelta(days=5)
```

### ✅ `-timedelta` → `timedelta`

```python
from datetime import timedelta

future = timedelta(days=5)
past = -future
# timedelta(days=-5)
```

---

## Сравнения

### `datetime` с `datetime`

```python
from datetime import datetime

dt1 = datetime(2025, 10, 16, 14, 30)
dt2 = datetime(2025, 10, 17, 14, 30)

dt1 < dt2   # True
dt1 > dt2   # False
dt1 == dt2  # False
dt1 != dt2  # True
```

### `date` с `date`

```python
from datetime import date

d1 = date(2025, 10, 16)
d2 = date(2025, 10, 17)

d1 < d2   # True
d1 == d2  # False
```

### `time` с `time`

```python
from datetime import time

t1 = time(14, 30)
t2 = time(15, 30)

t1 < t2   # True
t1 == t2  # False
```

### `timedelta` с `timedelta`

```python
from datetime import timedelta

td1 = timedelta(days=5)
td2 = timedelta(days=7)

td1 < td2   # True
td1 == td2  # False
```

### ❌ Нельзя сравнивать разные типы

```python
from datetime import datetime, date, time

dt = datetime(2025, 10, 16, 14, 30)
d = date(2025, 10, 16)
t = time(14, 30)

# dt == d   # ❌ False (разные типы)
# dt == t   # ❌ False (разные типы)
# d == t    # ❌ False (разные типы)
```

---

## Практические примеры

### Вычисление возраста

```python
from datetime import date

birth_date = date(1990, 5, 15)
today = date.today()

age_timedelta = today - birth_date
age_years = age_timedelta.days // 365
# Приблизительный возраст в годах
```

### Дедлайн через N дней

```python
from datetime import datetime, timedelta

now = datetime.now()
deadline = now + timedelta(days=7)

print(f"Дедлайн: {deadline.strftime('%d.%m.%Y %H:%M')}")
```

### Проверка прошло ли событие

```python
from datetime import datetime

event_time = datetime(2025, 10, 20, 15, 0)
now = datetime.now()

if now > event_time:
    print("Событие уже прошло")
else:
    time_left = event_time - now
    print(f"До события осталось: {time_left.days} дней")
```

### Добавление рабочих дней

```python
from datetime import date, timedelta

def add_business_days(start_date, days):
    current = start_date
    while days > 0:
        current += timedelta(days=1)
        # 0 = понедельник, 6 = воскресенье
        if current.weekday() < 5:  # Не суббота и не воскресенье
            days -= 1
    return current

start = date(2025, 10, 16)  # Четверг
result = add_business_days(start, 5)
# Пропустит выходные
```

---

## Итоговая таблица

|Тип слева|Операция|Тип справа|Результат|
|:-:|:-:|:-:|:-:|
|`datetime`|`+`|`timedelta`|`datetime`|
|`datetime`|`-`|`timedelta`|`datetime`|
|`datetime`|`-`|`datetime`|`timedelta`|
|`date`|`+`|`timedelta`|`date`|
|`date`|`-`|`timedelta`|`date`|
|`date`|`-`|`date`|`timedelta`|
|`time`|`+/-`|`timedelta`|❌ Ошибка|
|`timedelta`|`+`|`timedelta`|`timedelta`|
|`timedelta`|`-`|`timedelta`|`timedelta`|
|`timedelta`|`*`|`число`|`timedelta`|
|`timedelta`|`/`|`число`|`timedelta`|
|`timedelta`|`//`|`число`|`timedelta`|
|`timedelta`|`/`|`timedelta`|`float`|
|`timedelta`|`%`|`timedelta`|`timedelta`|

## 🔗 Связанные темы

- [[00 — 📖 Работа с датой и временем]]
- [[02 — 📝 Форматирование дат]]
- [[03 — 📚 Справочник методов]]
