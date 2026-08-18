---
tags:
  - python
  - тема/модули
  - тема/datetime
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# 🕐 Тип datetime — памятка

> `datetime` объединяет дату и время в одном объекте — самый используемый тип из модуля. По сути, это `date` + `time` вместе с полной арифметикой.

## Содержание

- [[#🟢 Создание|Создание]]
- [[#🔵 now() и today() — в чём разница|now() и today()]]
- [[#🔴 Атрибуты и методы|Атрибуты и методы]]
- [[#🟡 timestamp — перевод в Unix-время|timestamp]]
- [[#🟣 combine — сборка из date и time|combine()]]
- [[#💻 Практические примеры|Практические примеры]]

---

## Справка

| Что нужно | Код |
|---|---|
| Сейчас (локально) | `datetime.now()` |
| Сейчас (UTC) | `datetime.now(timezone.utc)` |
| Конкретные дата и время | `datetime(2024, 12, 31, 23, 59, 59)` |
| Из строки по формату | `datetime.strptime('31.12.2024', '%d.%m.%Y')` |
| Из ISO-строки | `datetime.fromisoformat('2024-12-31 23:59:59')` |
| Из Unix timestamp | `datetime.fromtimestamp(1704063599)` |
| В Unix timestamp | `dt.timestamp()` |
| Собрать из date + time | `datetime.combine(d, t)` |
| Достать только дату | `dt.date()` |
| Достать только время | `dt.time()` |
| В свой формат | `dt.strftime('%d.%m.%Y')` |

---

## 🟢 Создание

```python
from datetime import datetime

now = datetime.now()                        # текущие дата и время (локальные)
dt = datetime(2024, 12, 31, 23, 59, 59)     # конкретные дата и время

# Из строки
datetime.strptime('31.12.2024', '%d.%m.%Y')          # свой формат — см. [[05 — 📝 Форматирование]]
datetime.fromisoformat('2024-12-31 23:59:59')         # готовый ISO-формат

# Из Unix timestamp
datetime.fromtimestamp(1704063599)
```

**Насчёт `strptime()` у `date`/`time`:** до Python 3.14 этот метод был доступен только у `datetime` — вызов `date.strptime(...)` или `time.strptime(...)` выбрасывал `AttributeError`. Начиная с Python 3.14, `strptime()` появился и у `date`, и у `time` напрямую, без необходимости создавать полный `datetime` и потом обрезать его до нужной части.

---

## 🔵 now() и today() — в чём разница

Оба возвращают "текущий момент", но по-разному:

```python
from datetime import datetime, date, timezone, timedelta

datetime.now()                              # 2024-12-31 23:59:59.123456 — дата И время
date.today()                                # 2024-12-31 — ТОЛЬКО дата

# datetime.now() поддерживает часовой пояс, date.today() — нет
datetime.now(timezone.utc)                          # UTC-время
datetime.now(timezone(timedelta(hours=3)))          # своё смещение, например +3
```

`datetime.utcnow()` — устаревший способ получить UTC-время, вместо него используй `datetime.now(timezone.utc)`.

**С Python 3.12** `utcnow()` официально устарел и готовится к удалению в будущих версиях. Взамен появилась укороченная константа `UTC` прямо в модуле `datetime` — она эквивалентна `timezone.utc`, но короче писать:

```python
from datetime import datetime, UTC

datetime.now(UTC)   # то же самое, что datetime.now(timezone.utc), но компактнее
```

---

## 🔴 Атрибуты и методы

```python
dt = datetime(2024, 12, 31, 23, 59, 59)

# Атрибуты — всё что есть у date, плюс всё что есть у time
dt.year, dt.month, dt.day
dt.hour, dt.minute, dt.second, dt.microsecond

# Разбить datetime на составляющие
dt.date()       # date(2024, 12, 31) — только дата
dt.time()       # time(23, 59, 59) — только время
dt.weekday()    # день недели, 0 = понедельник

dt.isoformat()  # '2024-12-31T23:59:59'
dt.replace(year=2025, month=1, day=1)   # новый объект с заменёнными полями
```

---

## 🟡 timestamp — перевод в Unix-время

Unix timestamp — количество секунд, прошедших с полуночи 1 января 1970 года (UTC). Универсальный формат для передачи времени между системами, базами данных, API — не зависит от часового пояса или формата отображения.

```python
from datetime import datetime

dt = datetime(2024, 12, 31, 23, 59, 59)
timestamp = dt.timestamp()              # 1704063599.0 — datetime → timestamp

dt = datetime.fromtimestamp(1704063599)                       # timestamp → datetime (локальное время)
dt = datetime.fromtimestamp(1704063599, tz=timezone.utc)      # timestamp → datetime (UTC)

# Получить текущий timestamp без создания datetime
import time
current = time.time()
```

---

## 🟣 combine — сборка из date и time

Полезно когда дата и время приходят из разных источников (например, дата выбрана в календаре UI, время — из отдельного поля):

```python
from datetime import datetime, date, time

d = date(2024, 12, 31)
t = time(23, 59, 59)
dt = datetime.combine(d, t)                 # datetime(2024, 12, 31, 23, 59, 59)

# Частый паттерн — начало и конец дня
today = date.today()
start_of_day = datetime.combine(today, time.min)   # сегодня 00:00:00.000000
end_of_day = datetime.combine(today, time.max)      # сегодня 23:59:59.999999
```

**Зачем `time.min`/`time.max`:** это готовые константы для "начала суток" (00:00:00) и "конца суток" (23:59:59.999999). Пригождаются в SQL-подобных фильтрах: "все события за сегодня" = `WHERE dt BETWEEN start_of_day AND end_of_day`.

---

## 💻 Практические примеры

```python
from datetime import datetime, timedelta

# Дедлайн через N дней от текущего момента
now = datetime.now()
deadline = now + timedelta(days=7)
print(f"Дедлайн: {deadline.strftime('%d.%m.%Y %H:%M')}")

# Проверка — прошло ли событие
event_time = datetime(2025, 10, 20, 15, 0)
if datetime.now() > event_time:
    print("Событие уже прошло")
else:
    time_left = event_time - datetime.now()
    print(f"До события: {time_left.days} дней")

# Работа с часовыми поясами
from datetime import timezone
utc_now = datetime.now(timezone.utc)
moscow_tz = timezone(timedelta(hours=3))
moscow_time = datetime.now(moscow_tz)
```

---

## 🔗 Связанные темы

- [[00 — 🏠 Обзор]]
- [[01 — 📆 date]]
- [[02 — ⏰ time (тип)]]
- [[04 — ⏱ timedelta]]
- [[05 — 📝 Форматирование]]
