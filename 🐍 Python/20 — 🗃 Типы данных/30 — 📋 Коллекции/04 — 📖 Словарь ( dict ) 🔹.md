---
tags:
  - python
  - тема/типы_данных
  - тема/коллекции
  - статус/завершён
уровень: начальный
изменяемый: true
хешируемый: false
dict_key: false
упорядоченный: true
итерируемый: true
индексируемый: true
---

```python
# 📚 СЛОВАРЬ (dict)
{
    ключ: полноценное_значение,
    ключ: полноценное_значение
}
# Память: (hash, key, value) + указатели порядка

- Хранит: ключи и полноценные значения
- Порядок: сохраняется через двусвязный список
- Память: больше (значения + указатели порядка)
- Поиск: O(1) в среднем случае
```
# 📚 Памятка: Словари (dict) в Python

`dict` — это неупорядоченная (до 3.7) и упорядоченная (с 3.7+) коллекция пар «ключ: значение». Работает на основе хэш-таблиц.

### 💡 Ключевые особенности

- **Хранение:** Хранит уникальные ключи и связанные с ними значения.
    
- **Структура памяти:** `(hash, key, value)` + указатели порядка.
    
- **Порядок:** Сохраняется через двусвязный список (в современных версиях Python).
    
- **Память:** Потребляет больше памяти, чем списки или кортежи (из-за хранения хэшей и указателей).
    
- **Скорость:** Мгновенный поиск, добавление и удаление (O(1)).
    

### 💡 Полезные советы

- **Словари изменяемы**: можно свободно добавлять, изменять и удалять пары ключ-значение.
    
- **Ключи должны быть неизменяемыми**: строки, числа, кортежи (но не списки или сами словари).
    
- **Ключи уникальны**: если записать значение по уже существующему ключу, старое перезапишется.
    
- **Быстрый доступ**: поиск по ключу работает очень быстро (`O(1)` в среднем случае).
    
- **Порядок сохраняется**: начиная с Python 3.7+, словари сохраняют порядок добавления элементов.
    
- **Используйте `.get()`**: это безопасный доступ. Если ключа нет, вернется `None`, а не ошибка.
    
- **Dict comprehension**: быстрый способ создания словарей: `{k: v for k, v in ...}`.
    
- **Методы `.keys()`, `.values()`, `.items()`**: основные инструменты для итерации.
    

### 🚀 Быстрая справка (Методы)

|**Задача**|**Метод / Оператор**|**Сложность (O)**|**Пример**|
|---|---|---|---|
|**Создать словарь**|`{}` или `dict()`|O(n)|`{"a": 1, "b": 2}`|
|**Добавить / Изменить**|`[key] = value`|O(1)|`d["c"] = 3`|
|**Получить значение**|`.get(key)`|O(1)|`d.get("a", 0)`|
|**Удалить элемент**|`del` или `.pop()`|O(1)|`del d["a"]`|
|**Проверить ключ**|`in`|O(1)|`"a" in d`|
|**Все ключи**|`.keys()`|O(1)|`d.keys()`|
|**Все значения**|`.values()`|O(1)|`d.values()`|
|**Пары ключ-значение**|`.items()`|O(1)|`d.items()`|
|**Объединить словари**|`.update()` или `\|`|O(k)|`d1.update(d2)`|
|**Длина словаря**|`len()`|O(1)|`len(d)`|
|**Очистить всё**|`.clear()`|O(1)|`d.clear()`|
|**Удалить и вернуть**|`.pop(key)`|O(1)|`val = d.pop("a")`|

## 📝 Создание словарей

```python
# Пустой словарь
empty = {}
empty = dict()

# С парами ключ-значение
person = {"name": "John", "age": 25, "city": "Moscow"}
numbers = {1: "one", 2: "two", 3: "three"}
mixed = {"a": 1, "b": [1, 2], "c": {"nested": "dict"}}

# Через конструктор dict()
person = dict(name="John", age=25, city="Moscow")
# → {"name": "John", "age": 25, "city": "Moscow"}

# Из списка кортежей
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = dict(pairs)                 # → {"a": 1, "b": 2, "c": 3}

# Из двух списков (zip)
keys = ["name", "age", "city"]
values = ["John", 25, "Moscow"]
d = dict(zip(keys, values))

# Со значениями по умолчанию
d = dict.fromkeys(["a", "b", "c"], 0)
# → {"a": 0, "b": 0, "c": 0}

d = dict.fromkeys(["x", "y", "z"])
# → {"x": None, "y": None, "z": None}

# Dict comprehension
squares = {x: x**2 for x in range(5)}
# → {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

evens = {x: x*2 for x in range(10) if x % 2 == 0}
# → {0: 0, 2: 4, 4: 8, 6: 12, 8: 16}
```

## 🔍 Доступ к элементам

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# Получить значение по ключу
person["name"]                  # → "John"
person["age"]                   # → 25

# Ошибка при отсутствии ключа
# person["salary"]              # ❌ KeyError!

# Безопасный доступ через get()
person.get("name")              # → "John"
person.get("salary")            # → None (нет ошибки)
person.get("salary", 0)         # → 0 (значение по умолчанию)

# Проверка наличия ключа
"name" in person                # → True
"salary" in person              # → False
"salary" not in person          # → True

# Длина словаря
len(person)                     # → 3

# Проверка на пустоту
if person:                      # → True (не пустой)
    print("Словарь не пустой")
```

## ➕ Добавление и изменение

```python
person = {"name": "John", "age": 25}

# Добавить новую пару
person["city"] = "Moscow"       # → {"name": "John", "age": 25, "city": "Moscow"}
person["country"] = "Russia"

# Изменить существующее значение
person["age"] = 26              # → возраст изменён на 26

# Добавить, если ключа нет (не перезаписывает)
person.setdefault("name", "Bob")    # → "John" (не изменилось)
person.setdefault("salary", 50000)  # → добавлено "salary": 50000

# Обновить несколько значений
person.update({"age": 27, "city": "SPb"})
person.update(job="Developer", salary=60000)

# Объединение словарей (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
combined = dict1 | dict2        # → {"a": 1, "b": 2, "c": 3, "d": 4}

# Объединение с перезаписью
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}
combined = dict1 | dict2        # → {"a": 1, "b": 3, "c": 4}

# Изменить на месте
dict1 |= dict2                  # → dict1 изменён
```

## ➖ Удаление элементов

```python
person = {"name": "John", "age": 25, "city": "Moscow", "job": "Developer"}

# Удалить по ключу (вызовет ошибку если нет)
del person["job"]               # → {"name": "John", "age": 25, "city": "Moscow"}
# del person["salary"]          # ❌ KeyError!

# Удалить и вернуть значение
age = person.pop("age")         # → 25, person без "age"
salary = person.pop("salary", 0)  # → 0 (нет ошибки, вернули default)

# Удалить последнюю пару (с Python 3.7+)
item = person.popitem()         # → ("city", "Moscow")

# Очистить весь словарь
person.clear()                  # → {}
```

## 🔄 Итерация по словарю

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# По ключам (по умолчанию)
for key in person:
    print(key)                  # → "name", "age", "city"

for key in person.keys():
    print(key)                  # → то же самое

# По значениям
for value in person.values():
    print(value)                # → "John", 25, "Moscow"

# По парам ключ-значение
for key, value in person.items():
    print(f"{key}: {value}")    # → "name: John", "age: 25", ...

# С индексом
for i, (key, value) in enumerate(person.items()):
    print(f"{i}: {key} = {value}")

# Отсортированная итерация
for key in sorted(person.keys()):
    print(f"{key}: {person[key]}")

# По значениям отсортировано
for key in sorted(person, key=lambda k: person[k]):
    print(f"{key}: {person[key]}")
```

## 📊 Методы получения данных

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# Все ключи
keys = person.keys()            # → dict_keys(["name", "age", "city"])
keys_list = list(person.keys()) # → ["name", "age", "city"]

# Все значения
values = person.values()        # → dict_values(["John", 25, "Moscow"])
values_list = list(person.values())  # → ["John", 25, "Moscow"]

# Все пары ключ-значение
items = person.items()          # → dict_items([("name", "John"), ...])
items_list = list(person.items())  # → [("name", "John"), ("age", 25), ...]

# Преобразовать обратно в словарь
new_dict = dict(items_list)     # → {"name": "John", "age": 25, ...}
```

## 📋 Копирование словарей

```python
original = {"a": 1, "b": 2, "c": 3}

# Поверхностная копия
copy1 = original.copy()
copy2 = dict(original)
copy3 = {**original}            # распаковка словаря

# Важно! Присваивание НЕ создаёт копию
reference = original            # → та же ссылка!
reference["d"] = 4              # → изменит и original

# Для вложенных словарей нужна глубокая копия
import copy
nested = {"a": {"b": 1}}
deep = copy.deepcopy(nested)
deep["a"]["b"] = 2              # → не изменит nested
```

## 🎯 Практические примеры

### Подсчёт элементов

```python
# Подсчёт слов в тексте
text = "hello world hello python world"
words = text.split()

word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1
# → {"hello": 2, "world": 2, "python": 1}

# Через setdefault
word_count = {}
for word in words:
    word_count.setdefault(word, 0)
    word_count[word] += 1

# Через Counter (рекомендуется)
from collections import Counter
word_count = Counter(words)
# → Counter({"hello": 2, "world": 2, "python": 1})

# Подсчёт символов
text = "hello"
char_count = {char: text.count(char) for char in set(text)}
# → {"h": 1, "e": 1, "l": 2, "o": 1}
```

### Группировка данных

```python
# Группировка по категории
students = [
    {"name": "Alice", "grade": "A"},
    {"name": "Bob", "grade": "B"},
    {"name": "Charlie", "grade": "A"},
    {"name": "David", "grade": "B"}
]

by_grade = {}
for student in students:
    grade = student["grade"]
    by_grade.setdefault(grade, [])
    by_grade[grade].append(student["name"])
# → {"A": ["Alice", "Charlie"], "B": ["Bob", "David"]}

# Через defaultdict
from collections import defaultdict
by_grade = defaultdict(list)
for student in students:
    by_grade[student["grade"]].append(student["name"])
```

### Инверсия словаря

```python
# Поменять ключи и значения местами
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
# → {1: "a", 2: "b", 3: "c"}

# Если значения могут повторяться
original = {"a": 1, "b": 2, "c": 1}
inverted = {}
for k, v in original.items():
    inverted.setdefault(v, []).append(k)
# → {1: ["a", "c"], 2: ["b"]}
```

### Объединение словарей

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"e": 5, "f": 6}

# Через update
combined = {}
combined.update(dict1)
combined.update(dict2)
combined.update(dict3)

# Через unpacking (Python 3.5+)
combined = {**dict1, **dict2, **dict3}

# Через | (Python 3.9+)
combined = dict1 | dict2 | dict3

# ChainMap (не создаёт копию)
from collections import ChainMap
combined = ChainMap(dict1, dict2, dict3)
```

### Фильтрация словаря

```python
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}

# Только высокие баллы
high_scores = {k: v for k, v in scores.items() if v >= 90}
# → {"Bob": 92, "David": 95}

# Только определённые ключи
keys_to_keep = ["Alice", "Charlie"]
filtered = {k: scores[k] for k in keys_to_keep}
# → {"Alice": 85, "Charlie": 78}

# Удалить определённые ключи
keys_to_remove = ["Bob"]
filtered = {k: v for k, v in scores.items() if k not in keys_to_remove}
```

### Сортировка словаря

```python
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}

# По ключам
sorted_by_keys = dict(sorted(scores.items()))
# → {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}

# По значениям
sorted_by_values = dict(sorted(scores.items(), key=lambda x: x[1]))
# → {"Charlie": 78, "Alice": 85, "Bob": 92, "David": 95}

# По значениям (убывание)
sorted_desc = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True))
# → {"David": 95, "Bob": 92, "Alice": 85, "Charlie": 78}

# Топ-3
top3 = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True)[:3])
```

### Вложенные словари

```python
# Создание
users = {
    "user1": {"name": "Alice", "age": 25, "city": "Moscow"},
    "user2": {"name": "Bob", "age": 30, "city": "SPb"},
    "user3": {"name": "Charlie", "age": 35, "city": "Moscow"}
}

# Доступ к вложенным данным
users["user1"]["name"]          # → "Alice"
users["user2"]["age"]           # → 30

# Безопасный доступ
users.get("user1", {}).get("name", "Unknown")  # → "Alice"
users.get("user99", {}).get("name", "Unknown")  # → "Unknown"

# Изменение вложенных данных
users["user1"]["age"] = 26

# Добавление нового пользователя
users["user4"] = {"name": "David", "age": 28, "city": "Kazan"}

# Поиск по вложенному значению
moscow_users = {k: v for k, v in users.items() if v["city"] == "Moscow"}
```

### Конфигурация приложения

```python
config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "mydb"
    },
    "server": {
        "host": "0.0.0.0",
        "port": 8000
    },
    "debug": True
}

# Доступ к конфигурации
db_host = config["database"]["host"]
server_port = config["server"]["port"]
```

### Кэширование результатов

```python
# Простой кэш
cache = {}

def expensive_function(x):
    if x in cache:
        print("Из кэша")
        return cache[x]
    
    print("Вычисляем...")
    result = x ** 2  # долгая операция
    cache[x] = result
    return result

expensive_function(5)           # → Вычисляем... 25
expensive_function(5)           # → Из кэша 25

# Через декоратор functools.lru_cache (рекомендуется)
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(x):
    return x ** 2
```

### Статистика данных

```python
data = [
    {"name": "Alice", "age": 25, "salary": 50000},
    {"name": "Bob", "age": 30, "salary": 60000},
    {"name": "Charlie", "age": 35, "salary": 70000}
]

# Средний возраст
avg_age = sum(person["age"] for person in data) / len(data)

# Общая зарплата
total_salary = sum(person["salary"] for person in data)

# Максимальная зарплата
max_salary = max(person["salary"] for person in data)
max_person = max(data, key=lambda x: x["salary"])
```

### Преобразование форматов данных

```python
# CSV-подобные данные в словарь
csv_data = [
    ["name", "age", "city"],
    ["Alice", 25, "Moscow"],
    ["Bob", 30, "SPb"]
]

headers = csv_data[0]
users = [dict(zip(headers, row)) for row in csv_data[1:]]
# → [{"name": "Alice", "age": 25, "city": "Moscow"}, ...]

# Словарь в список пар
person = {"name": "John", "age": 25}
pairs = [[k, v] for k, v in person.items()]
# → [["name", "John"], ["age", 25]]
```

## 🔧 defaultdict - словарь со значениями по умолчанию

```python
from collections import defaultdict

# С int (по умолчанию 0)
counter = defaultdict(int)
counter["a"] += 1               # → не нужна проверка
counter["b"] += 1
counter["a"] += 1
# → {"a": 2, "b": 1}

# Со списком (по умолчанию [])
groups = defaultdict(list)
groups["fruits"].append("apple")
groups["fruits"].append("banana")
groups["vegetables"].append("carrot")
# → {"fruits": ["apple", "banana"], "vegetables": ["carrot"]}

# С множеством (по умолчанию set())
tags = defaultdict(set)
tags["post1"].add("python")
tags["post1"].add("tutorial")

# С пользовательской функцией
counter = defaultdict(lambda: 100)
counter["a"]                    # → 100 (начальное значение)
```

## 📖 OrderedDict - упорядоченный словарь

```python
from collections import OrderedDict

# В Python 3.7+ обычные dict уже упорядочены
# OrderedDict нужен для особых случаев

d = OrderedDict()
d["a"] = 1
d["b"] = 2
d["c"] = 3

# Переместить в конец
d.move_to_end("a")              # → {"b": 2, "c": 3, "a": 1}

# Переместить в начало
d.move_to_end("c", last=False)  # → {"c": 3, "b": 2, "a": 1}

# Удалить последний элемент
d.popitem(last=True)            # → ("a", 1)

# Удалить первый элемент
d.popitem(last=False)           # → ("c", 3)
```

## ⚠️ Важные особенности

```python
# ❌ Ключи должны быть неизменяемыми
# d = {[1, 2]: "value"}         # TypeError! список не хешируемый
# d = {{1, 2}: "value"}         # TypeError! множество не хешируемое

# ✅ Можно использовать
d = {(1, 2): "value"}           # кортеж ✅
d = {1: "value"}                # число ✅
d = {"key": "value"}            # строка ✅
d = {frozenset([1, 2]): "val"}  # frozenset ✅

# ⚠️ Изменение словаря во время итерации
d = {"a": 1, "b": 2, "c": 3}
# for key in d:
#     if key == "b":
#         del d[key]            # ❌ RuntimeError!

# ✅ Создайте копию ключей
for key in list(d.keys()):
    if key == "b":
        del d[key]              # ✅ Работает

# ⚠️ Значения могут быть изменяемыми
d = {"a": [1, 2, 3]}
d["a"].append(4)                # → {"a": [1, 2, 3, 4]}

# ⚠️ Дублирующиеся ключи перезаписываются
d = {"a": 1, "b": 2, "a": 3}    # → {"a": 3, "b": 2}
```

## 🎯 Когда использовать словари

**Используйте словари когда:**

- Нужна связь ключ-значение
- Требуется быстрый доступ по ключу
- Храните настройки, конфигурации
- Подсчитываете элементы
- Группируете данные
- Кэшируете результаты
- Храните атрибуты объектов

**Используйте списки когда:**

- Важен порядок и индексация
- Нужна коллекция однотипных элементов
- Часто добавляете в конец

**Используйте множества когда:**

- Нужны только уникальные элементы
- Важны операции объединения/пересечения
- Порядок не важен

## 🔗 Связанные темы

- [[04.1  ⤷ Структура словаря Python]]
- [[04.1 .0 ⤷ Структура словаря Python📖 Устройство словаря (dict) под капотом]]
- [[04.2  ⤷ Упорядоченность и хранение словаря]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[03 — ⚡ Множества (set) 🔹]]
- [[00 — 🗂️ Памятка Коллекции Python]]
- [[defaultdict]]
- [[Counter]]
- [[OrderedDict]]
- [[Все про Хеш-функции]]
