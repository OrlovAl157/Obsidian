---
tags:
  - python
  - тема/файлы
  - тема/форматы_данных
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
---

# 🗂️ Работа с JSON-файлами

## 📌 Коротко

> JSON (JavaScript Object Notation) — текстовый формат обмена данными. Модуль `json` обеспечивает сериализацию Python объектов в JSON и наоборот с поддержкой различных параметров форматирования и кодировки.

---

## 💡 Полезные советы

- **Используйте `ensure_ascii=False`** — для сохранения кириллицы без экранирования
- **Кортежи становятся списками** — потеря информации при сериализации!
- **Числовые ключи преобразуются в строки** — `{1: 'a'}` → `{"1": "a"}`
- **`indent=4` — красивый формат** — для разработки и отладки
- **Обрабатывайте `JSONDecodeError`** — при парсинге неправильного JSON
- **UTF-8 — стандарт** — всегда используйте `encoding='utf-8'`
- **`dumps()` vs `dump()`** — s в конце = строка, без s = файл

---

## 🚀 Быстрая справка

`dump()` и `load()` — файловый объект, 
`dumps()` и `loads()` — строка. 
Различить легко, в методах `dumps()` и `loads()` последняя буква `s` — string, строка.

```
dumps: dict → JSON string
loads: JSON string → dict

dump:  dict → JSON file
load:  JSON file → dict
```

| Метод | Назначение | Работает с | Пример |
|---|---|---|---|
| `json.dumps()` | Сериализует в строку | Объект → строка | `s = json.dumps(data)` |
| `json.dump()` | Сериализует в файл | Объект → файл | `json.dump(data, f)` |
| `json.loads()` | Десериализует из строки | Строка → объект | `data = json.loads(s)` |
| `json.load()` | Десериализует из файла | Файл → объект | `data = json.load(f)` |

---

## 📖 Теория

### Что такое JSON?

**JSON** (JavaScript Object Notation) — лёгкий текстовый формат для обмена данными между системами.

**💡 Особенности JSON:**
- ✅ Компактный и читаемый
- ✅ Поддерживается всеми языками программирования
- ✅ Стандарт для веб-API и конфигураций
- ✅ Быстрый парсинг
- ✅ Безопаснее Pickle (не выполняет код)

**💻 Пример JSON:**
```json
{
    "firstName": "John",
    "lastName": "Doe",
    "age": 30,
    "married": true,
    "address": {
        "street": "123 Main St",
        "city": "New York"
    },
    "hobbies": ["reading", "gaming"]
}
```

### Типы данных JSON

| JSON | Python | Пример |
|---|---|---|
| `string` | `str` | `"Hello"` |
| `number` (int) | `int` | `42` |
| `number` (float) | `float` | `3.14` |
| `true` | `True` | `true` |
| `false` | `False` | `false` |
| `null` | `None` | `null` |
| `array` | `list` | `[1, 2, 3]` |
| `object` | `dict` | `{"key": "value"}` |

**⚠️ Важные особенности:**
- Кортежи `(1, 2)` → списки `[1, 2]`
- Числовые ключи → строки: `{1: "a"}` → `{"1": "a"}`
- `set` и `frozenset` → ошибка (не сериализуются)

### Синтаксис JSON

**📝 Обязательные правила:**
- Двойные кавычки: `"ключ"` (не одинарные)
- Запятые между элементами
- Двоеточие после ключей: `"key": value`
- Отступы необязательны (только для читаемости)

```json
// ✅ Правильно
{
    "name": "Alice",
    "age": 25,
    "tags": ["python", "web"]
}

// ❌ Неправильно (одинарные кавычки)
{'name': 'Alice'}

// ❌ Неправильно (без кавычек вокруг ключа)
{name: "Alice"}
```

---

## 💻 Сериализация (Python → JSON)

### Способ 1: В строку (dumps)

```python
import json

data = {'name': 'Alice', 'age': 25, 'city': 'Moscow'}

# Простой вывод (компактный)
json_str = json.dumps(data)
# '{"name": "Alice", "age": 25, "city": "Moscow"}'

# С форматированием и сортировкой
json_str = json.dumps(data, indent=4, ensure_ascii=False, sort_keys=True)
# {
#     "age": 25,
#     "city": "Moscow",
#     "name": "Alice"
# }

# С кастомными разделителями
json_str = json.dumps(data, separators=(';', ' = '))
# {"name" = "Alice"; "age" = 25; ...}
```

### Способ 2: В файл (dump)

```python
import json

data = {'name': 'Alice', 'age': 25}

with open('data.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=4, ensure_ascii=False)
```

### Параметры сериализации

| Параметр | По умолчанию | Описание |
|---|---|---|
| `indent` | `None` | Отступ (None = компактно, 4 = красиво) |
| `sort_keys` | `False` | Сортировать ключи по алфавиту |
| `ensure_ascii` | `True` | Экранировать кириллицу (False = оставить как есть) |
| `separators` | `(', ', ': ')` | Разделители между элементами |
| `skipkeys` | `False` | Пропустить несериализуемые ключи |

```python
import json

data = {'фамилия': 'Петров', 'возраст': 30}

# ❌ По умолчанию (экранирует кириллицу)
s1 = json.dumps(data)
# {"фамилия": "\u043f\u0435\u0442\u0440\u043e\u0432", ...}

# ✅ С ensure_ascii=False (сохраняет текст)
s2 = json.dumps(data, ensure_ascii=False)
# {"фамилия": "Петров", "возраст": 30}
```

---

## 💻 Десериализация (JSON → Python)

### Способ 1: Из строки (loads)

```python
import json

json_str = '{"name": "Alice", "age": 25}'

data = json.loads(json_str)
print(data)  # {'name': 'Alice', 'age': 25}
print(type(data))  # <class 'dict'>
print(data['name'])  # Alice
```

### Способ 2: Из файла (load)

```python
import json

with open('data.json', 'r', encoding='utf-8') as f:
    data = json.load(f)
    print(data)

# Работаем как с обычным словарём
for key, value in data.items():
    print(f'{key}: {value}')
```

### Обработка ошибок

```python
import json

# Неверный JSON
json_str = '{"name":, "age": 25}'

try:
    data = json.loads(json_str)
except json.JSONDecodeError as e:
    print(f"❌ Ошибка JSON: {e}")
    print(f"Строка {e.lineno}, символ {e.colno}")
```

---

## 💻 Полный цикл

```python
import json

# Создание данных
user = {
    'name': 'Alice',
    'age': 25,
    'emails': ['alice@mail.com', 'alice@work.com'],
    'address': {
        'city': 'Moscow',
        'zip': '123456'
    }
}

# СОХРАНЕНИЕ В ФАЙЛ
with open('user.json', 'w', encoding='utf-8') as f:
    json.dump(user, f, indent=4, ensure_ascii=False)

# ЗАГРУЗКА ИЗ ФАЙЛА
with open('user.json', 'r', encoding='utf-8') as f:
    loaded_user = json.load(f)

# РАБОТА С ДАННЫМИ
print(loaded_user['name'])  # Alice
print(loaded_user['address']['city'])  # Moscow
```

---

## 💻 Практические примеры

### Работа с русским текстом

```python
import json

data = {'firstName': 'Иван', 'lastName': 'Петров', 'город': 'Москва'}

# ❌ По умолчанию (экранирует кириллицу)
s1 = json.dumps(data)
# {"firstName": "\u0418\u0432\u0430\u043d", ...}

# ✅ Правильно (сохраняет текст)
s2 = json.dumps(data, ensure_ascii=False, indent=2)
# {
#   "firstName": "Иван",
#   "lastName": "Петров",
#   "город": "Москва"
# }
```

### Сохранение конфигурации

```python
import json

config = {
    'database': {
        'host': 'localhost',
        'port': 5432,
        'name': 'myapp'
    },
    'debug': True,
    'api_keys': ['key1', 'key2']
}

# Сохранение
with open('config.json', 'w', encoding='utf-8') as f:
    json.dump(config, f, indent=2)

# Загрузка и использование
with open('config.json', 'r', encoding='utf-8') as f:
    loaded_config = json.load(f)
    db_host = loaded_config['database']['host']
```

### Работа с вложенными данными

```python
import json

# Список словарей
users = [
    {
        'id': 1,
        'name': 'Alice',
        'tags': ['python', 'web']
    },
    {
        'id': 2,
        'name': 'Bob',
        'tags': ['rust', 'systems']
    }
]

# Сохранение и загрузка
with open('users.json', 'w', encoding='utf-8') as f:
    json.dump(users, f, indent=2, ensure_ascii=False)

with open('users.json', 'r', encoding='utf-8') as f:
    loaded_users = json.load(f)
    
for user in loaded_users:
    print(f"{user['name']}: {user['tags']}")
```

### Преобразование типов вручную

```python
import json

# Проблема: кортежи становятся списками
data = {'coords': (10, 20, 30)}
json_str = json.dumps(data)
new_data = json.loads(json_str)

print(type(new_data['coords']))  # <class 'list'>, не tuple!

# Решение: преобразуйте обратно вручную
new_data['coords'] = tuple(new_data['coords'])
```

---

## ⚠️ Важные ограничения

```python
import json

# ❌ Нельзя сериализовать кортежи как ключи
data = {(1, 2): 'value'}
try:
    json.dumps(data)  # TypeError!
except TypeError:
    print("Кортежи нельзя использовать как ключи")

# ✅ Используйте строки
data = {'(1, 2)': 'value'}
json_str = json.dumps(data)

# ❌ Числовые ключи преобразуются в строки
data = {1: 'one', 2: 'two'}
json_str = json.dumps(data)
new_data = json.loads(json_str)
# Ключи теперь строки: {'1': 'one', '2': 'two'}

# ❌ Нельзя с пользовательскими объектами (по умолчанию)
class Person:
    def __init__(self, name):
        self.name = name

try:
    json.dumps(Person('Alice'))  # TypeError!
except TypeError:
    print("Нужен custom encoder")

# ✅ Используйте custom JSONEncoder
class PersonEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj, Person):
            return {'name': obj.name}
        return super().default(obj)

json_str = json.dumps(Person('Alice'), cls=PersonEncoder)
```

---

## ⚠️ Частые ошибки

```python
import json

# ❌ Ошибка 1: Забыли encoding при записи
with open('data.json', 'w') as f:  # ❌ Проблема с кириллицей на Windows!
    json.dump({'name': 'Иван'}, f)

# ✅ Всегда указывайте UTF-8
with open('data.json', 'w', encoding='utf-8') as f:
    json.dump({'name': 'Иван'}, f, ensure_ascii=False)

# ❌ Ошибка 2: Забыли ensure_ascii=False
data = {'name': 'Иван'}
s = json.dumps(data)  # {"name": "\u0418\u0432\u0430\u043d"}

# ✅ Добавьте ensure_ascii=False
s = json.dumps(data, ensure_ascii=False)  # {"name": "Иван"}

# ❌ Ошибка 3: Не обработали JSONDecodeError
data = json.loads('invalid json')  # ❌ Вызовет исключение

# ✅ Обрабатывайте ошибку
try:
    data = json.loads(json_str)
except json.JSONDecodeError as e:
    print(f"Ошибка в JSON: {e}")

# ❌ Ошибка 4: Проверили файл ПОСЛЕ открытия
with open('file.json') as f:  # ❌ FileNotFoundError если файла нет!
    data = json.load(f)

# ✅ Проверьте перед открытием
from pathlib import Path

if Path('file.json').exists():
    with open('file.json') as f:
        data = json.load(f)
else:
    data = {}
```

---

## 📝 Шпаргалка

```python
import json

# СЕРИАЛИЗАЦИЯ (Python → JSON)

# В строку
json_string = json.dumps(data, indent=4, ensure_ascii=False, sort_keys=True)

# В файл
with open('file.json', 'w', encoding='utf-8') as f:
    json.dump(data, f, indent=4, ensure_ascii=False)

# ДЕСЕРИАЛИЗАЦИЯ (JSON → Python)

# Из строки
data = json.loads(json_string)

# Из файла
with open('file.json', 'r', encoding='utf-8') as f:
    data = json.load(f)

# С обработкой ошибок
try:
    data = json.loads(json_string)
except json.JSONDecodeError as e:
    print(f"Ошибка JSON: {e}")
```

---

## 🔗 Связанные темы

- [[00 — Обзор работы с файлами]]
- [[04 — 📖 Словарь ( dict ) 🔹]]
- [[01 — Работа с текстовыми файлами]]

## ❓ Вопросы / Непонятное
