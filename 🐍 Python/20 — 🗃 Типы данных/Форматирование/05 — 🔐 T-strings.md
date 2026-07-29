---
tags: [python, строки, t-strings, python-3.14]
difficulty: advanced
---

# 🔐 T-строки (Template strings) — памятка

> T-строки (Python 3.14+) — способ форматирования строк, при котором значения хранятся **отдельно** от шаблона. В отличие от f-строк не подставляют значения сразу — вместо этого возвращают объект `Template`, который можно обработать безопасно. Основное применение — защита от SQL и HTML инъекций.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 T-строка vs F-строка|T-строка vs F-строка]]
- [[#🔵 Класс Template и его атрибуты|Класс Template и его атрибуты]]
- [[#🔴 Класс Interpolation|Класс Interpolation]]
- [[#🟡 Функция convert()|Функция convert()]]
- [[#🟣 Получение итоговой строки|Получение итоговой строки]]
- [[#🔐 Защита от SQL-инъекций|Защита от SQL-инъекций]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

---

## Справка

| Элемент | Что это | Пример |
|---|---|---|
| `t"..."` | T-строка — возвращает `Template` | `t"Hello {name}"` |
| `Template` | Объект с атрибутами strings, interpolations, values | `result.strings` |
| `Interpolation` | Один заполнитель `{expr!conv:fmt}` | `interp.value` |
| `convert()` | Применяет флаг `!s`, `!r`, `!a` к значению | `convert(val, 'r')` |
| `strings` | Кортеж подстрок между заполнителями | `('Hello ', '')` |
| `interpolations` | Кортеж объектов Interpolation | `(Interpolation(...),)` |
| `values` | Кортеж вычисленных значений | `('Alice',)` |

---

## 📊 Общая схема

```
t"Hello {name!r:>10}, age {age:03d}"
         │                  │
         │                  └── Interpolation 2
         │                       .value      = 25
         │                       .expression = 'age'
         │                       .format_spec = '03d'
         │                       .conversion  = None
         │
         └── Interpolation 1
              .value      = 'Alice'
              .expression = 'name'
              .format_spec = '>10'
              .conversion  = 'r'

Template.strings         = ('Hello ', ', age ', '')
Template.interpolations  = (Interpolation1, Interpolation2)
Template.values          = ('Alice', 25)

Порядок при итерации:
strings[0] → interpolations[0] → strings[1] → interpolations[1] → strings[2]
"Hello "      name                ", age "      age                ""
```

---

## 🟢 T-строка vs F-строка

| Параметр | f-строка | T-строка |
|---|---|---|
| Префикс | `f"..."` | `t"..."` |
| Результат | `str` — готовая строка | `Template` — объект |
| Подстановка | Сразу при создании | Отложена — ты контролируешь |
| Безопасность | Опасна для SQL/HTML | Защищает от инъекций |
| Применение | Вывод, логирование | SQL, HTML, шаблоны |
| Python | 3.6+ | 3.14+ |

```python
name = 'James'
surname = 'Bond'

# F-строка — сразу подставляет, возвращает str
f_result = f'My name is {surname}, {name.upper()} {surname.upper()}'
print(type(f_result))   # <class 'str'>
print(f_result)         # My name is Bond, JAMES BOND

# T-строка — хранит отдельно, возвращает Template
t_result = t'My name is {surname}, {name.upper()} {surname.upper()}'
print(type(t_result))   # <class 'string.templatelib.Template'>
print(t_result)         # Template(strings=(...), interpolations=(...))
```

---

## 🔵 Класс Template и его атрибуты

```python
from string.templatelib import Template

name = 'James'
surname = 'Bond'
result = t'My name is {surname}, {name.upper()} {surname.upper()}'

# strings — подстроки между заполнителями
print(result.strings)
# ('My name is ', ', ', ' ', '')

# interpolations — объекты Interpolation
print(result.interpolations)
# (Interpolation(...), Interpolation(...), Interpolation(...))

# values — вычисленные значения (без format и conversion)
print(result.values)
# ('Bond', 'JAMES', 'BOND')
```

**Правило чередования:** элементы при итерации всегда идут как `str → Interpolation → str → Interpolation → ... → str`. Строк всегда на одну больше чем заполнителей.

```
"My name is {surname}, {name.upper()} {surname.upper()}"
 strings[0]  interp[0]  strings[1]  interp[1]  strings[2]  interp[2]  strings[3]
"My name is"  surname    ", "       name.upper()  " "      surname.upper()  ""
```

---

## 🔴 Класс Interpolation

Каждый заполнитель `{выражение!conversion:format_spec}` — это объект `Interpolation` с четырьмя атрибутами:

```python
result = t'My name is {surname!s:<15}, {name.upper()!r}'

for interp in result.interpolations:
    print(f"value:       {interp.value}")       # вычисленное значение
    print(f"expression:  {interp.expression}")  # исходный код выражения
    print(f"format_spec: {interp.format_spec}") # спецификатор формата
    print(f"conversion:  {interp.conversion}")  # флаг !s / !r / !a
    print()
```

Вывод:
```
value:       Bond
expression:  surname
format_spec: <15
conversion:  s

value:       JAMES
expression:  name.upper()
format_spec:
conversion:  r
```

**Синтаксис заполнителя:**

```python
{выражение!conversion:format_spec}

{name}          # expression='name', conversion=None, format_spec=''
{name!s}        # conversion='s'
{name:>10}      # format_spec='>10'
{name!r:^20}    # conversion='r', format_spec='^20'
```

---

## 🟡 Функция convert()

`convert(value, flag)` — применяет флаг преобразования к значению:

```python
from string.templatelib import convert

text = 'hello'

convert(text, None)   # → 'hello'     без изменений
convert(text, 's')    # → 'hello'     str()
convert(text, 'r')    # → "'hello'"   repr() — с кавычками
convert(text, 'a')    # → "'hello'"   ascii()
```

| Флаг | Функция | Когда использовать |
|---|---|---|
| `None` | без изменений | по умолчанию |
| `'s'` | `str()` | явное преобразование в строку |
| `'r'` | `repr()` | отладка, отображение с кавычками |
| `'a'` | `ascii()` | только ASCII символы |

---

## 🟣 Получение итоговой строки

T-строка не подставляет значения сама — ты делаешь это вручную через итерацию:

```python
from string.templatelib import convert

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
# → My name is Bond, JAMES
```

**Компактная версия:**

```python
from string.templatelib import convert, Interpolation

result = ''.join(
    e if isinstance(e, str)
    else convert(format(e.value, e.format_spec), e.conversion)
    for e in template
)
```

---

## 🔐 Защита от SQL-инъекций

**Проблема с f-строками:**

```python
# ❌ F-строка уязвима
artist = "' OR '1'='1"
query = f"SELECT * FROM Songs WHERE artist = '{artist}'"
print(query)
# SELECT * FROM Songs WHERE artist = '' OR '1'='1'
# ← SQL инъекция! Вернёт ВСЕ записи!
```

**Решение с T-строками:**

```python
def clean_sql(template):
    """Заменяет заполнители на ? — безопасный плейсхолдер"""
    parts = []
    for element in template:
        if isinstance(element, str):
            parts.append(element)
        else:
            parts.append('?')
    return ''.join(parts), template.values

artist = "' OR '1'='1"
template = t"SELECT * FROM Songs WHERE artist = '{artist}'"

query, values = clean_sql(template)
print(query)   # SELECT * FROM Songs WHERE artist = ?
print(values)  # ("' OR '1'='1",)

# Теперь безопасно передаём в БД:
# db.execute(query, values)
```

---

## ⚡ Быстрые примеры

```python
from string.templatelib import convert, Interpolation

name = 'Alice'
age = 25

# Создание
t = t"Hello {name}, age {age}"

# Атрибуты
t.strings         # ('Hello ', ', age ', '')
t.values          # ('Alice', 25)
t.interpolations  # (Interpolation(...), Interpolation(...))

# Итерация
for part in t:
    print(type(part).__name__, repr(part) if isinstance(part, str) else part.value)

# Конкатенация T + T
t1 = t'Hello {name}, '
t2 = t'age {age}'
combined = t1 + t2          # ✅ Template

# Конкатенация T + str
combined = t1 + f'age {age}'  # ❌ TypeError

# SQL защита
def safe_sql(tmpl):
    return ''.join('?' if isinstance(e, Interpolation) else e for e in tmpl)
```

---

## 💡 Практические замечания

- T-строки доступны только в Python 3.14+
- Используй T-строки только когда нужна защита от инъекций — для обычного вывода f-строки лучше
- `template.values` — значения без `format()` и `convert()`, только сырые результаты выражений
- При конкатенации можно объединять только T-строки между собой
- Всегда применяй `format()` перед `convert()`, не наоборот

---

## ⚠️ Частые ошибки

**❌ Забыл применить format() и convert():**
```python
for interp in template.interpolations:
    print(interp.value)              # ❌ без форматирования

for interp in template.interpolations:
    v = format(interp.value, interp.format_spec)
    v = convert(v, interp.conversion)
    print(v)                         # ✅
```

**❌ Смешал T-строку с f-строкой или str:**
```python
t'Hello {name}' + f'World {name}'   # ❌ TypeError
t'Hello {name}' + t'World {name}'   # ✅
```

**❌ Неправильный флаг conversion:**
```python
convert(text, 'x')   # ❌ ValueError — нет такого флага
convert(text, 'r')   # ✅ только None, 's', 'r', 'a'
```

**❌ Перепутал порядок format() и convert():**
```python
convert(format(value, fmt), conv)   # ✅ сначала format, потом convert
format(convert(value, conv), fmt)   # ❌ convert применяется к уже отформатированному
```

---

## ✅ Главные правила

✅ T-строка возвращает `Template`, а не `str` — значения не подставляются сразу  
✅ Элементы Template чередуются: `str → Interpolation → str → ...`  
✅ `Interpolation` имеет четыре атрибута: `value`, `expression`, `format_spec`, `conversion`  
✅ Всегда применяй `format()` перед `convert()`  
✅ T-строки можно объединять только с T-строками, не со `str`  
✅ Для простого вывода используй f-строки — T-строки только для защиты от инъекций  
✅ `clean_sql()` — типичный паттерн: заменяй заполнители на `?`, передавай значения отдельно  

---

## 🔗 Связанные темы

- [[00 — 📊 Обзор и сравнение способов]]
- [[03 — ✨ f-строки]]
- [[04 — 🎯 Спецификатор формата]]

---

#python/строки #форматирование #t-strings #python-3.14
