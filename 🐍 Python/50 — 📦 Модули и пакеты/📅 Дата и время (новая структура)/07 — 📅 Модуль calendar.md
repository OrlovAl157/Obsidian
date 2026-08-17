---
tags:
  - python
  - тема/модули
  - тема/datetime
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# 📅 Модуль calendar — памятка

> Модуль `calendar` — для работы с календарными расчётами и текстовым отображением: проверка високосных лет, количество дней в месяце, печать календаря месяца/года, названия дней и месяцев.

## Содержание

- [[#🟢 Проверка високосных лет|Високосные годы]]
- [[#🔵 Дни недели и количество дней в месяце|Дни недели и месяцы]]
- [[#🔴 Текстовый календарь|Текстовый календарь]]
- [[#🟡 Названия дней и месяцев|Названия]]
- [[#🟣 Локализация и первый день недели|Локализация]]

---

## Справка

| Что нужно | Код |
|---|---|
| Проверить високосный год | `calendar.isleap(2024)` |
| Число високосных лет в диапазоне | `calendar.leapdays(2020, 2025)` |
| День недели для даты | `calendar.weekday(2024, 12, 31)` |
| Дней в месяце | `calendar.monthrange(2024, 2)[1]` |
| Матрица дней месяца | `calendar.monthcalendar(2024, 12)` |
| Текстовый календарь месяца | `calendar.month(2024, 12)` |
| Текстовый календарь года | `calendar.calendar(2024)` |
| Названия дней/месяцев | `calendar.day_name`, `calendar.month_name` |

---

## 🟢 Проверка високосных лет

```python
import calendar

calendar.isleap(2024)           # True
calendar.isleap(2023)           # False

calendar.leapdays(2020, 2025)   # 2 — сколько високосных лет в диапазоне [2020, 2025)
```

---

## 🔵 Дни недели и количество дней в месяце

```python
import calendar

calendar.weekday(2024, 12, 31)   # 1 — день недели для конкретной даты (0=пн, ..., 6=вс)

calendar.monthrange(2024, 2)     # (3, 29) — (день недели 1-го числа, количество дней в месяце)
calendar.monthrange(2024, 2)[1]  # 29 — сколько дней в феврале 2024 (високосный!)

# Матрица дней месяца — готовая структура для отрисовки календаря в UI
calendar.monthcalendar(2024, 12)
# [[0, 0, 0, 0, 0, 0, 1],
#  [2, 3, 4, 5, 6, 7, 8],
#  ...
#  [30, 31, 0, 0, 0, 0, 0]]
# 0 означает "день не из этого месяца" — заполнитель для выравнивания недель
```

---

## 🔴 Текстовый календарь

```python
import calendar

print(calendar.month(2024, 12))   # текстовый календарь одного месяца
calendar.prmonth(2024, 12)        # то же самое, но сразу печатает (без print)

print(calendar.calendar(2024))    # календарь целого года
calendar.prcal(2024)              # то же самое, сразу печатает

# Параметры для настройки вида:
# month(year, month, w=0, l=0) — w=ширина столбца, l=строк на неделю
calendar.month(2024, 12, w=3, l=2)

# calendar(year, w=2, l=1, c=6, m=3) — плюс c=пробелы между месяцами, m=месяцев в ряд
calendar.calendar(2024, m=4)      # 4 месяца в ряд вместо стандартных 3
```

---

## 🟡 Названия дней и месяцев

```python
import calendar

calendar.day_name[0]            # 'Monday'
calendar.day_name[6]            # 'Sunday'
list(calendar.day_name)         # ['Monday', 'Tuesday', ...]

calendar.day_abbr[0]            # 'Mon' — сокращённое название
list(calendar.day_abbr)         # ['Mon', 'Tue', ...]

# ⚠️ Месяцы индексируются с 1, а не с 0! (в отличие от дней недели)
calendar.month_name[1]          # 'January'
calendar.month_name[12]         # 'December'
list(calendar.month_name)       # ['', 'January', 'February', ...] — нулевой элемент пустой!

calendar.month_abbr[1]          # 'Jan'

# Константы дней недели — удобны вместо "магических чисел" в коде
calendar.MONDAY                 # 0
calendar.SUNDAY                 # 6
```

**Важная деталь:** `month_name` и `month_abbr` начинаются с пустой строки на индексе 0 — это намеренно, чтобы номер месяца (1-12) совпадал с индексом в списке напрямую, без вычитания единицы.

---

## 🟣 Локализация и первый день недели

```python
import calendar

# По умолчанию первый день недели — понедельник (0)
calendar.firstweekday()                        # 0

calendar.setfirstweekday(calendar.SUNDAY)       # сделать воскресенье первым (US-стиль)
calendar.firstweekday()                         # 6
calendar.setfirstweekday(calendar.MONDAY)       # вернуть обратно
```

### Локализация на русский

```python
import calendar, locale

locale.setlocale(locale.LC_ALL, 'ru_RU.UTF-8')   # может отличаться в зависимости от ОС

list(calendar.day_name)    # ['понедельник', 'вторник', ...]
list(calendar.day_abbr)    # ['Пн', 'Вт', 'Ср', ...]
list(calendar.month_name)  # ['', 'Январь', 'Февраль', ...]

calendar.prmonth(2024, 12)              # календарь уже на русском

# Заглавная буква для названий дней, если нужно
[name.title() for name in calendar.day_name]
```

---

## 🔗 Связанные темы

- [[00 — 🏠 Обзор]]
- [[01 — 📆 date]]
- [[05 — 📝 Форматирование]]
