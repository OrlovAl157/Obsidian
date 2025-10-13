## ⚡ БЫСТРАЯ ШПАРГАЛКА (самое нужное)

```python
from datetime import datetime, date, timedelta

# Получить текущие дату и время
datetime.now()                  # 2024-12-31 23:59:59.123456
date.today()                    # 2024-12-31

# Создать конкретную дату/время
date(2024, 12, 31)                          # дата
datetime(2024, 12, 31, 23, 59, 59)          # дата и время

# Из строки
datetime.strptime('31.12.2024', '%d.%m.%Y')         # из строки
datetime.fromisoformat('2024-12-31 23:59:59')       # из ISO

# В строку
dt.strftime('%d.%m.%Y')                     # '31.12.2024'
dt.strftime('%Y-%m-%d %H:%M:%S')            # '2024-12-31 23:59:59'

# Арифметика с датами
today = date.today()
today + timedelta(days=1)       # завтра
today - timedelta(days=7)       # неделю назад
today + timedelta(hours=3)      # через 3 часа (для datetime)

# Разница между датами
d1 - d2                         # вернет timedelta
(d1 - d2).days                  # количество дней

# Получить компоненты
dt.year, dt.month, dt.day       # год, месяц, день
dt.hour, dt.minute, dt.second   # час, минута, секунда
dt.weekday()                    # день недели (0-пн, 6-вс)

# Сравнение
date1 > date2                   # True/False
date1 == date2                  # True/False

# Преобразование в строку
str(dt)                         # '2024-12-31 23:59:59'
repr(dt)                        # 'datetime.datetime(2024, 12, 31, 23, 59, 59)'

# Порядковый номер дня (с 01.01.0001)
date.toordinal()                # 738885 (дней с начала эры)
date.fromordinal(738885)        # date(2024, 12, 31)
```

### Самые частые форматы

```python
'%d.%m.%Y'          # 31.12.2024
'%Y-%m-%d'          # 2024-12-31
'%d/%m/%y'          # 31/12/24
'%H:%M'             # 23:59
'%H:%M:%S'          # 23:59:59
'%d.%m.%Y %H:%M'    # 31.12.2024 23:59
```

---

## 📚 ПОДРОБНАЯ ДОКУМЕНТАЦИЯ

### 1. Тип данных date (дата)

```python
from datetime import date

# Создание даты
today = date.today()                    # Сегодняшняя дата
d = date(2024, 12, 31)                  # Конкретная дата (год, месяц, день)

# Атрибуты
d.year          # 2024 (год)
d.month         # 12 (месяц)
d.day           # 31 (день)

# Методы
d.weekday()     # День недели (0-понедельник, 6-воскресенье)
d.isoweekday()  # День недели (1-понедельник, 7-воскресенье)
d.isoformat()   # '2024-12-31' (строка в формате ISO)
d.strftime('%d.%m.%Y')  # '31.12.2024' (форматирование)

# Замена значений
d.replace(year=2025)    # date(2025, 12, 31)
d.replace(month=1)      # date(2024, 1, 31)

# Создание даты из строки
date.fromisoformat('2024-12-31')     # Из ISO формата
```

---

### 2. Тип данных time (время)

```python
from datetime import time

# Создание времени
t = time(14, 30, 45)              # 14:30:45 (час, минута, секунда)
t = time(14, 30, 45, 500000)      # 14:30:45.500000 (с микросекундами)

# Атрибуты
t.hour          # 14 (час)
t.minute        # 30 (минута)
t.second        # 45 (секунда)
t.microsecond   # 500000 (микросекунда)

# Методы
t.isoformat()   # '14:30:45' (строка в формате ISO)
t.strftime('%H:%M')  # '14:30' (форматирование)

# Замена значений
t.replace(hour=15)      # time(15, 30, 45)
t.replace(minute=0)     # time(14, 0, 45)
```

---

### 3. Тип данных datetime (дата и время)

```python
from datetime import datetime

# Создание datetime
now = datetime.now()                    # Текущие дата и время
dt = datetime(2024, 12, 31, 23, 59, 59) # Конкретные дата и время

# Атрибуты (все от date + все от time)
dt.year, dt.month, dt.day
dt.hour, dt.minute, dt.second, dt.microsecond

# Методы
dt.date()       # Получить только дату (объект date)
dt.time()       # Получить только время (объект time)
dt.weekday()    # День недели
dt.isoformat()  # '2024-12-31T23:59:59' (строка ISO)

# Замена значений
dt.replace(year=2025, month=1, day=1)  # Новый datetime
```

#### Создание datetime из строки

```python
# Из ISO формата
datetime.fromisoformat('2024-12-31 23:59:59')
datetime.fromisoformat('2024-12-31T23:59:59')

# Парсинг строки с указанием формата
datetime.strptime('31.12.2024 23:59', '%d.%m.%Y %H:%M')

# Из timestamp (Unix время)
datetime.fromtimestamp(1704063599)

# Из даты и времени
d = date(2024, 12, 31)
t = time(23, 59, 59)
datetime.combine(d, t)  # datetime(2024, 12, 31, 23, 59, 59)
```

#### Форматирование datetime

```python
dt = datetime(2024, 12, 31, 23, 59, 59)

# strftime - datetime → строка
dt.strftime('%d.%m.%Y')           # '31.12.2024'
dt.strftime('%Y-%m-%d %H:%M:%S')  # '2024-12-31 23:59:59'
dt.strftime('%A, %d %B %Y')       # 'Tuesday, 31 December 2024'
dt.strftime('%d/%m/%y')           # '31/12/24'
```

#### Коды форматирования

```
%Y - год (4 цифры)      %y - год (2 цифры)
%m - месяц (01-12)      %B - название месяца (December)
%d - день (01-31)       %b - сокращ. месяц (Dec)
%H - час (00-23)        %A - день недели (Monday)
%M - минута (00-59)     %a - сокращ. день (Mon)
%S - секунда (00-59)    %w - день недели числом (0-6)
%f - микросекунды       %j - день года (001-366)
```

---

### 4. Тип данных timedelta (интервал времени)

```python
from datetime import timedelta

# Создание интервала
td = timedelta(days=5)                      # 5 дней
td = timedelta(hours=3, minutes=30)         # 3 часа 30 минут
td = timedelta(weeks=2, days=3, hours=5)    # 2 недели, 3 дня, 5 часов

# Атрибуты
td.days         # Количество дней
td.seconds      # Секунды (0-86399, без учета дней)
td.microseconds # Микросекунды

# Методы
td.total_seconds()  # Общее количество секунд

# Арифметика с датами
today = date.today()
tomorrow = today + timedelta(days=1)         # Завтра
yesterday = today - timedelta(days=1)        # Вчера
week_ago = today - timedelta(weeks=1)        # Неделю назад

# Разница между датами
d1 = date(2024, 12, 31)
d2 = date(2024, 1, 1)
diff = d1 - d2          # timedelta(days=365)
diff.days               # 365

# Арифметика с datetime
now = datetime.now()
future = now + timedelta(hours=3, minutes=30)
past = now - timedelta(days=7)

# Сравнение
td1 = timedelta(days=1)
td2 = timedelta(hours=24)
td1 == td2  # True

# Умножение и деление
td = timedelta(days=1)
td * 3                  # timedelta(days=3)
td / 2                  # timedelta(hours=12)
```

#### Примеры использования timedelta

```python
from datetime import datetime, timedelta

# Добавить 10 рабочих дней
def add_business_days(start_date, days):
    current = start_date
    while days > 0:
        current += timedelta(days=1)
        if current.weekday() < 5:  # Пн-Пт
            days -= 1
    return current

# Найти последний день месяца
from datetime import date
def last_day_of_month(d):
    next_month = d.replace(day=28) + timedelta(days=4)
    return next_month - timedelta(days=next_month.day)

# Вычислить возраст
def calculate_age(birth_date):
    today = date.today()
    age = today.year - birth_date.year
    if (today.month, today.day) < (birth_date.month, birth_date.day):
        age -= 1
    return age
```

---

### 5. Модуль time

```python
import time

# Текущее время (timestamp Unix)
time.time()             # 1704063599.123456 (секунды с 1970-01-01)

# Приостановка выполнения
time.sleep(2)           # Пауза на 2 секунды
time.sleep(0.5)         # Пауза на 0.5 секунды

# Измерение времени выполнения
start = time.time()
# ... код ...
end = time.time()
print(f"Время выполнения: {end - start} сек")

# Более точное измерение (для небольших интервалов)
start = time.perf_counter()
# ... код ...
end = time.perf_counter()
print(f"Время: {end - start} сек")

# Структура времени
t = time.localtime()    # Локальное время
t.tm_year               # Год
t.tm_mon                # Месяц
t.tm_mday               # День
t.tm_hour, t.tm_min, t.tm_sec

# Форматирование
time.strftime('%d.%m.%Y %H:%M:%S', time.localtime())

# Парсинг строки
time.strptime('31.12.2024', '%d.%m.%Y')
```

---

### 6. Модуль calendar

```python
import calendar

# Проверка високосного года
calendar.isleap(2024)           # True
calendar.isleap(2023)           # False

# Количество дней в месяце
calendar.monthrange(2024, 2)    # (3, 29) - (день недели 1-го числа, кол-во дней)
calendar.monthrange(2024, 2)[1] # 29 дней

# Текстовый календарь месяца
print(calendar.month(2024, 12))

# Календарь года
print(calendar.calendar(2024))

# Матрица дней месяца
calendar.monthcalendar(2024, 12)
# [[0, 0, 0, 0, 0, 0, 1],
#  [2, 3, 4, 5, 6, 7, 8],
#  ...
#  [30, 31, 0, 0, 0, 0, 0]]

# Названия дней недели и месяцев
calendar.day_name[0]        # 'Monday'
calendar.month_name[1]      # 'January'

# Установка первого дня недели (0 - понедельник, 6 - воскресенье)
calendar.setfirstweekday(calendar.SUNDAY)
```

---

### 7. Практические примеры

#### Вычисление разницы между датами

```python
from datetime import date

birth = date(1990, 5, 15)
today = date.today()
age_days = (today - birth).days
age_years = age_days // 365
print(f"Возраст: {age_years} лет ({age_days} дней)")
```

#### Работа с временными зонами (timezone)

```python
from datetime import datetime, timezone, timedelta

# UTC время
utc_now = datetime.now(timezone.utc)

# Время с часовым поясом
moscow_tz = timezone(timedelta(hours=3))
moscow_time = datetime.now(moscow_tz)
```

#### Форматирование для разных локалей

```python
import locale
from datetime import datetime

# Установка русской локали
locale.setlocale(locale.LC_TIME, 'ru_RU.UTF-8')

now = datetime.now()
now.strftime('%A, %d %B %Y')  # 'понедельник, 31 декабря 2024'
```

#### Итерация по датам

```python
from datetime import date, timedelta

start = date(2024, 1, 1)
end = date(2024, 1, 10)
delta = timedelta(days=1)

current = start
while current <= end:
    print(current)
    current += delta
```

#### Поиск всех понедельников в месяце

```python
from datetime import date, timedelta

def get_mondays(year, month):
    d = date(year, month, 1)
    # Находим первый понедельник
    d += timedelta(days=(7 - d.weekday()) % 7)
    
    mondays = []
    while d.month == month:
        mondays.append(d)
        d += timedelta(weeks=1)
    return mondays

mondays = get_mondays(2024, 12)
```

---

### 8. Сравнительная таблица

|Операция|date|time|datetime|
|---|---|---|---|
|Создание текущего|`date.today()`|-|`datetime.now()`|
|Атрибуты|year, month, day|hour, minute, second|все|
|Арифметика|✅ (с timedelta)|❌|✅ (с timedelta)|
|Сравнение|✅|✅|✅|
|Форматирование|✅|✅|✅|

---

## 💡 Полезные советы

1. **Для хранения дат в БД** используйте ISO формат: `date.isoformat()`
2. **Для вычислений** используйте `datetime` и `timedelta`
3. **Для измерения времени выполнения** используйте `time.perf_counter()`
4. **Всегда учитывайте часовые пояса** при работе с международными приложениями
5. **Для календарных вычислений** используйте модуль `calendar`