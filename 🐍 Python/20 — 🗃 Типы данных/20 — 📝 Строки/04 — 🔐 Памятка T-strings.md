---
tags:
  - python
  - строки
  - t-strings
  - template
  - python-3.14
  - памятка
уровень: продвинутое
---

# 🔐 Памятка: T-строки (Template strings) в Python 3.14

## 🎯 Суть в 30 секунд

**T-строки** — новый способ формирования строк в Python 3.14, который хранит значения отдельно от строки. Защищают от инъекций (SQL, HTML) в отличие от f-строк.

```python
name = 'James'
# F-строка (опасна для SQL)
query = f"SELECT * WHERE name = {name}"

# T-строка (безопаснее, значения отдельно)
template = t"SELECT * WHERE name = {name}"
```

---

## 📌 Часть 1: Основы T-строк

### 🚀 T-строка vs F-строка

| Параметр | **F-строка** | **T-строка** |
|----------|-----------|-----------|
| **Префикс** | `f"..."` или `f'...'` | `t"..."` или `t'...'` |
| **Выполнение** | Подставляет значения сразу | Хранит значения отдельно |
| **Тип** | `str` | `Template` (объект) |
| **Безопасность** | Опасна для SQL/HTML | Защищает от инъекций |
| **Использование** | Вывод, логирование | Динамический код (SQL, HTML) |

### 💻 Пример различия

```python
from string.templatelib import Template

name = 'James'
surname = 'Bond'

# F-строка — сразу подставляет
f_result = f'My name is {surname}, {name.upper()} {surname.upper()}'
print(f_result)  # My name is Bond, JAMES BOND (обычная строка)

# T-строка — хранит отдельно
t_result = t'My name is {surname}, {name.upper()} {surname.upper()}'
print(t_result)
# Template(strings=(...), interpolations=(...))
```

---

## 📌 Часть 2: Структура T-строки

### 🏗️ Класс Template

T-строка — это объект класса `Template`, имеющий атрибуты:

```python
from string.templatelib import Template

name = 'James'
surname = 'Bond'
result = t'My name is {surname}, {name.upper()} {surname.upper()}'

# Атрибут strings — подстроки между заполнителями
print(result.strings)
# ('My name is ', ', ', ' ', '')

# Атрибут interpolations — заполнители (объекты Interpolation)
print(result.interpolations)
# (Interpolation(...), Interpolation(...), Interpolation(...))

# Атрибут values — значения выражений (без format и conversion)
print(result.values)
# ('Bond', 'JAMES', 'BOND')
```

### 📊 Порядок элементов

```
Строка:          My name is {surname}, {name.upper()} {surname.upper()}
                 ^            ^       ^              ^                 ^

strings[0]       strings[1]   strings[2]             strings[3]
"My name is "    ", "         " "                    ""

               interpolations[0]  interpolations[1]  interpolations[2]
               surname             name.upper()     surname.upper()
```

**Правило:** Элементы Template чередуются: строка → заполнитель → строка → заполнитель → ... → строка

---

## 📌 Часть 3: Класс Interpolation

### 🎯 Атрибуты Interpolation

```python
from string.templatelib import Template

name = 'James'
surname = 'Bond'
result = t'My name is {surname!s:<15}, {name.upper()!r} {surname.upper():!^10}'

for interp in result.interpolations:
    print(f"Значение:     {interp.value}")
    print(f"Выражение:    {interp.expression}")
    print(f"Format spec:  {interp.format_spec}")
    print(f"Conversion:   {interp.conversion}")
    print()
```

**Вывод:**
```
Значение:     Bond
Выражение:    surname
Format spec:  <15
Conversion:   s

Значение:     JAMES
Выражение:    name.upper()
Format spec:  
Conversion:   r

Значение:     BOND
Выражение:    surname.upper()
Format spec:  !^10
Conversion:   None
```

### 📋 Части Interpolation

```python
{выражение!conversion:format_spec}
       ↓        ↓           ↓
   expression conversion  format_spec

# Примеры
{name}                    # expression='name', conversion=None, format_spec=''
{name!s}                  # conversion='s'
{name:>10}                # format_spec='>10'
{name!r:^20}              # conversion='r', format_spec='^20'
```

---

## 📌 Часть 4: Функция convert()

### 🔧 Что это?

`convert()` — применяет флаг преобразования к объекту.

```python
from string.templatelib import convert

text = 'beegeek'

# None — без изменений
print(convert(text, None))   # beegeek

# 's' — str()
print(convert(text, 's'))    # beegeek

# 'r' — repr()
print(convert(text, 'r'))    # 'beegeek'

# 'a' — ascii()
print(convert(text, 'a'))    # 'beegeek'
```

### 📊 Флаги преобразования

| Флаг | Функция | Результат |
|------|---------|-----------|
| `None` | Без изменений | Объект как есть |
| `'s'` | `str()` | Строковое представление |
| `'r'` | `repr()` | Представление для разработчика |
| `'a'` | `ascii()` | ASCII-представление |

---

## 📌 Часть 5: Получение итоговой строки

### 🔄 Алгоритм обработки

```python
from string.templatelib import convert, Interpolation

name = 'James'
surname = 'Bond'
result = t'My name is {surname!s:<15}, {name.upper()!r} {surname.upper():!^10}'

final_parts = []

# 1. Итерируемся по элементам шаблона
for part in result:
    # 2. Если это строка — добавляем как есть
    if isinstance(part, str):
        final_parts.append(part)
    
    # 3. Если это заполнитель — обрабатываем
    else:
        # Получаем компоненты
        conversion = part.conversion
        format_spec = part.format_spec
        value = part.value
        
        # Применяем format()
        formatted_value = format(value, format_spec)
        
        # Применяем convert()
        converted_value = convert(formatted_value, conversion)
        
        # Добавляем в результат
        final_parts.append(converted_value)

# 4. Объединяем все части в одну строку
final_string = ''.join(final_parts)
print(final_string)
# My name is Bond       , 'JAMES' !!!BOND!!!
```

### 💻 Функция для обработки T-строки

```python
from string.templatelib import convert, Interpolation

def process_template(template):
    """Преобразует t-строку в обычную строку"""
    parts = []
    
    for element in template:
        if isinstance(element, str):
            parts.append(element)
        else:  # Interpolation
            value = format(element.value, element.format_spec)
            value = convert(value, element.conversion)
            parts.append(value)
    
    return ''.join(parts)

# Использование
name = 'James'
surname = 'Bond'
template = t'My name is {surname}, {name.upper()}'
print(process_template(template))
# My name is Bond, JAMES
```

---

## 📌 Часть 6: Защита от SQL-инъекций

### 🔐 Проблема с F-строками

```python
# ❌ ОПАСНО! F-строка уязвима
artist = "' OR '1'='1"
query = f"SELECT * FROM Songs WHERE artist = '{artist}'"
print(query)
# SELECT * FROM Songs WHERE artist = '' OR '1'='1'
# ← SQL инъекция! Вернёт ВСЕ записи!
```

### ✅ Решение с T-строками

```python
def clean_sql(template):
    """Очищает t-строку от опасного кода, заменяя значения на ?"""
    parts = []
    
    for element in template:
        if isinstance(element, str):
            parts.append(element)
        else:
            # Заменяем значение на ? (безопасный плейсхолдер)
            parts.append('?')
    
    return ''.join(parts)

# Использование
artist = "' OR '1'='1"
template = t"SELECT * FROM Songs WHERE artist = '{artist}'"

# Очищенный запрос
safe_query = clean_sql(template)
print(safe_query)
# SELECT * FROM Songs WHERE artist = ?

# Теперь безопасно подставляем значение
# Значение хранится в template.values[0]
values = template.values
print(values)  # ("' OR '1'='1",)
```

### 🔄 Полная схема безопасного SQL

```python
from string.templatelib import convert

def safe_sql_query(template, execute_func):
    """Формирует безопасный SQL-запрос и выполняет его"""
    # 1. Очищаем шаблон
    parts = []
    for element in template:
        if isinstance(element, str):
            parts.append(element)
        else:
            parts.append('?')
    
    query = ''.join(parts)
    
    # 2. Получаем значения заполнителей
    values = template.values
    
    # 3. Выполняем с параметризованным запросом
    return execute_func(query, values)

# Использование
# artist = user_input  # Опасный ввод от пользователя
# template = t"SELECT * FROM Songs WHERE artist = '{artist}'"
# result = safe_sql_query(template, db.execute)
```

---

## 📌 Часть 7: Конкатенация T-строк

### ✅ T-строка + T-строка

```python
name = 'James'
surname = 'Bond'

result1 = t'My name is {surname}, '
result2 = t'{name.upper()} {surname.upper()}'

combined = result1 + result2  # ✅ Работает!
print(combined)
# Template(strings=(...), interpolations=(...))
```

### ❌ T-строка + F-строка

```python
result1 = t'My name is {surname}, '
result2 = f'{name.upper()} {surname.upper()}'  # f-строка (тип str)

combined = result1 + result2  # ❌ TypeError!
# TypeError: can only concatenate Template (not "str") to Template
```

**Правило:** T-строки можно объединять между собой, но не с обычными строками.

---

## 🔥 Частые ошибки

### ❌ Ошибка 1: Забыть применить format() и convert()

```python
# ❌ НЕПРАВИЛЬНО
result = t'{value:>10!s}'
for part in result.interpolations:
    print(part.value)  # Выведет БЕЗ форматирования!

# ✅ ПРАВИЛЬНО
for part in result.interpolations:
    formatted = format(part.value, part.format_spec)
    converted = convert(formatted, part.conversion)
    print(converted)  # С форматированием
```

### ❌ Ошибка 2: Смешивать T-строки с F-строками

```python
# ❌ НЕПРАВИЛЬНО
t_str = t'Hello {name}'
f_str = f'World {name}'
result = t_str + f_str  # TypeError!

# ✅ ПРАВИЛЬНО
result = t_str + t'World {name}'
```

### ❌ Ошибка 3: Неправильный флаг conversion

```python
from string.templatelib import convert

text = 'beegeek'
convert(text, 'x')  # ❌ ValueError: invalid conversion specifier: x

# ✅ Используй только: None, 's', 'r', 'a'
convert(text, 's')  # ✅ OK
```

### ❌ Ошибка 4: Не учитывать порядок элементов

```python
result = t'Hello {name}, you are {age} years old'

# ❌ НЕПРАВИЛЬНЫЙ ПОРЯДОК
print(result.interpolations)  # (name, age)
print(result.strings)         # ('Hello ', ', you are ', ' years old')

# ✅ ПРАВИЛЬНЫЙ ПОРЯДОК (чередующийся)
# strings[0] + interpolations[0] + strings[1] + interpolations[1] + strings[2]
```

---

## 💎 Лучшие практики

### ✅ DO

```python
from string.templatelib import convert

# 1. Используй функцию-обработчик
def process_template(template):
    parts = []
    for element in template:
        if isinstance(element, str):
            parts.append(element)
        else:
            value = format(element.value, element.format_spec)
            value = convert(value, element.conversion)
            parts.append(value)
    return ''.join(parts)

# 2. Для SQL используй параметризованные запросы
def safe_query(template):
    query_parts = []
    for element in template:
        if isinstance(element, str):
            query_parts.append(element)
        else:
            query_parts.append('?')
    return ''.join(query_parts), template.values

# 3. Проверяй тип перед обработкой
from string.templatelib import Interpolation
for element in template:
    if isinstance(element, Interpolation):
        # Обрабатываем заполнитель
        pass
```

### ❌ DON'T

```python
# 1. Не пропускай format() и convert()
for interp in template.interpolations:
    print(interp.value)  # ❌ Без форматирования

# 2. Не объединяй t-строки с f-строками
result = t'...' + f'...'  # ❌ TypeError

# 3. Не используй для простых строк
# Используй f-строки, если не нужна защита от инъекций
text = f'Hello {name}'  # ✅ Правильнее, чем t'Hello {name}'

# 4. Не забывай про порядок элементов
# strings и interpolations чередуются!
```

---

## 🔗 Связанные темы

- [[05 — F-строки]]
- [[20 — Форматирование строк]]
- [[25 — Работа с базами данных (SQL)]]

---

## ✅ Шпаргалка

```python
from string.templatelib import convert, Interpolation

# Создание t-строки
template = t'Hello {name!s:>10}, age {age:03d}'

# Доступ к компонентам
print(template.strings)         # Подстроки
print(template.interpolations)  # Заполнители
print(template.values)          # Значения выражений

# Итерирование
for element in template:
    if isinstance(element, str):
        # Обычная строка
        pass
    else:
        # Interpolation объект
        value = element.value
        expr = element.expression
        fmt = element.format_spec
        conv = element.conversion

# Форматирование и преобразование
formatted = format(value, format_spec)
converted = convert(formatted, conversion)

# Получение итоговой строки
result = ''.join([
    (e if isinstance(e, str) else 
     convert(format(e.value, e.format_spec), e.conversion))
    for e in template
])

# Конкатенация
template1 = t'Hello {name}, '
template2 = t'age {age}'
combined = template1 + template2  # ✅ Template + Template

# SQL защита
def clean_sql(tmpl):
    return ''.join('?' if isinstance(e, Interpolation) else e for e in tmpl)
```
