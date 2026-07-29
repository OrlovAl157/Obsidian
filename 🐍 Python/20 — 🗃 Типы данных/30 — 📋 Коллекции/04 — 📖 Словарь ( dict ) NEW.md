---
tags: [python, типы_данных, коллекции, dict]
difficulty: beginner
---

# 📖 Словарь (dict) — памятка

> `dict` — изменяемая коллекция пар «ключ: значение» на основе хэш-таблицы. Порядок вставки сохраняется (Python 3.7+). Мгновенный доступ по ключу — O(1).

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Создание словарей|Создание словарей]]
- [[#🔵 Доступ к элементам|Доступ к элементам]]
- [[#🔴 Добавление и изменение|Добавление и изменение]]
- [[#🟡 Удаление элементов|Удаление элементов]]
- [[#🟣 Итерация по словарю|Итерация по словарю]]
- [[#⚙️ Методы получения данных|Методы получения данных]]
- [[#📋 Копирование словарей|Копирование словарей]]
- [[#🔧 defaultdict и OrderedDict|defaultdict и OrderedDict]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

---

## Справка

| Задача | Метод / Оператор | Сложность | Пример |
|---|---|---|---|
| Создать словарь | `{}` или `dict()` | O(n) | `{"a": 1, "b": 2}` |
| Добавить / Изменить | `[key] = value` | O(1) | `d["c"] = 3` |
| Получить значение | `.get(key)` | O(1) | `d.get("a", 0)` |
| Удалить элемент | `del` или `.pop()` | O(1) | `del d["a"]` |
| Проверить ключ | `in` | O(1) | `"a" in d` |
| Все ключи | `.keys()` | O(1) | `d.keys()` |
| Все значения | `.values()` | O(1) | `d.values()` |
| Пары ключ-значение | `.items()` | O(1) | `d.items()` |
| Объединить словари | `.update()` или `\|` | O(k) | `d1.update(d2)` |
| Длина словаря | `len()` | O(1) | `len(d)` |
| Очистить всё | `.clear()` | O(1) | `d.clear()` |
| Удалить и вернуть | `.pop(key)` | O(1) | `val = d.pop("a")` |

---

## 📊 Общая схема

```
dict = {ключ: значение, ключ: значение}
         │       │
         │       └── любой объект Python
         └── только неизменяемые (str, int, tuple, frozenset)

Внутри: (hash, key, value) + указатели порядка

Особенности:
├── Порядок сохраняется (Python 3.7+)
├── Ключи уникальны — дублирующийся перезапишет старый
├── Поиск O(1) — на основе хэш-таблицы
└── Изменяемый — можно добавлять, менять, удалять
```

---

## 🟢 Создание словарей

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
d = dict(pairs)                      # → {"a": 1, "b": 2, "c": 3}

# Из двух списков через zip
keys = ["name", "age", "city"]
values = ["John", 25, "Moscow"]
d = dict(zip(keys, values))

# Со значениями по умолчанию
d = dict.fromkeys(["a", "b", "c"], 0)   # → {"a": 0, "b": 0, "c": 0}
d = dict.fromkeys(["x", "y", "z"])      # → {"x": None, "y": None, "z": None}

# Dict comprehension
squares = {x: x**2 for x in range(5)}
# → {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

evens = {x: x*2 for x in range(10) if x % 2 == 0}
# → {0: 0, 2: 4, 4: 8, 6: 12, 8: 16}
```

---

## 🔵 Доступ к элементам

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# Получить значение по ключу
person["name"]                   # → "John"

# ❌ Ошибка при отсутствии ключа
person["salary"]                 # KeyError!

# Безопасный доступ через get()
person.get("name")               # → "John"
person.get("salary")             # → None (нет ошибки)
person.get("salary", 0)          # → 0 (значение по умолчанию)

# Проверка наличия ключа
"name" in person                 # → True
"salary" in person               # → False
"salary" not in person           # → True

# Длина и проверка на пустоту
len(person)                      # → 3
bool(person)                     # → True (не пустой)
```

---

## 🔴 Добавление и изменение

```python
person = {"name": "John", "age": 25}

# Добавить новую пару
person["city"] = "Moscow"        # → {"name": "John", "age": 25, "city": "Moscow"}

# Изменить существующее значение
person["age"] = 26

# setdefault — добавить если ключа нет, не перезаписывает
person.setdefault("name", "Bob")     # → "John" (не изменилось)
person.setdefault("salary", 50000)   # → добавлено "salary": 50000

# update — обновить несколько значений
person.update({"age": 27, "city": "SPb"})
person.update(job="Developer", salary=60000)

# Объединение словарей (Python 3.9+)
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}
combined = dict1 | dict2         # → {"a": 1, "b": 3, "c": 4}  (b перезаписан)
dict1 |= dict2                   # изменить на месте
```

---

## 🟡 Удаление элементов

```python
person = {"name": "John", "age": 25, "city": "Moscow", "job": "Developer"}

# del — удалить по ключу (KeyError если нет)
del person["job"]

# pop() — удалить и вернуть значение
age = person.pop("age")              # → 25
salary = person.pop("salary", 0)     # → 0 (нет ошибки, вернули default)

# popitem() — удалить последнюю пару (Python 3.7+)
item = person.popitem()              # → ("city", "Moscow")

# clear() — очистить весь словарь
person.clear()                       # → {}
```

---

## 🟣 Итерация по словарю

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# По ключам (по умолчанию)
for key in person:
    print(key)                    # → "name", "age", "city"

# По значениям
for value in person.values():
    print(value)                  # → "John", 25, "Moscow"

# По парам ключ-значение
for key, value in person.items():
    print(f"{key}: {value}")

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

---

## ⚙️ Методы получения данных

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# Представления (живые — отражают изменения словаря)
person.keys()    # → dict_keys(["name", "age", "city"])
person.values()  # → dict_values(["John", 25, "Moscow"])
person.items()   # → dict_items([("name", "John"), ("age", 25), ("city", "Moscow")])

# Преобразовать в список
list(person.keys())    # → ["name", "age", "city"]
list(person.values())  # → ["John", 25, "Moscow"]
list(person.items())   # → [("name", "John"), ("age", 25), ("city", "Moscow")]

# Обратно в словарь
dict(person.items())   # → {"name": "John", "age": 25, "city": "Moscow"}
```

---

## 📋 Копирование словарей

```python
original = {"a": 1, "b": 2, "c": 3}

# Поверхностная копия — три способа
copy1 = original.copy()
copy2 = dict(original)
copy3 = {**original}

# ❌ Присваивание НЕ создаёт копию
reference = original
reference["d"] = 4               # изменит и original!

# Для вложенных словарей — глубокая копия
import copy
nested = {"a": {"b": 1}}
deep = copy.deepcopy(nested)
deep["a"]["b"] = 2               # не изменит nested ✅
```

---

## 🔧 defaultdict и OrderedDict

**defaultdict — словарь со значением по умолчанию:**

```python
from collections import defaultdict

# С int (по умолчанию 0) — удобно для подсчёта
counter = defaultdict(int)
counter["a"] += 1                # не нужна проверка наличия ключа
counter["a"] += 1
# → {"a": 2}

# Со списком (по умолчанию []) — удобно для группировки
groups = defaultdict(list)
groups["fruits"].append("apple")
groups["fruits"].append("banana")
groups["vegetables"].append("carrot")
# → {"fruits": ["apple", "banana"], "vegetables": ["carrot"]}

# С пользовательским значением
counter = defaultdict(lambda: 100)
counter["a"]                     # → 100
```

**OrderedDict — нужен для особых случаев (в Python 3.7+ обычный dict уже упорядочен):**

```python
from collections import OrderedDict

d = OrderedDict([("a", 1), ("b", 2), ("c", 3)])

d.move_to_end("a")               # → {"b": 2, "c": 3, "a": 1}
d.move_to_end("c", last=False)   # → {"c": 3, "b": 2, "a": 1}
d.popitem(last=True)             # → ("a", 1)
d.popitem(last=False)            # → ("c", 3)
```

---

## ⚡ Быстрые примеры

```python
# Подсчёт слов
from collections import Counter
text = "hello world hello python"
Counter(text.split())            # → Counter({"hello": 2, "world": 1, "python": 1})

# Группировка данных
from collections import defaultdict
students = [{"name": "Alice", "grade": "A"}, {"name": "Bob", "grade": "B"},
            {"name": "Charlie", "grade": "A"}]
by_grade = defaultdict(list)
for s in students:
    by_grade[s["grade"]].append(s["name"])
# → {"A": ["Alice", "Charlie"], "B": ["Bob"]}

# Инверсия словаря
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
# → {1: "a", 2: "b", 3: "c"}

# Фильтрация
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}
high = {k: v for k, v in scores.items() if v >= 90}
# → {"Bob": 92, "David": 95}

# Сортировка по значению
sorted_scores = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True))
# → {"David": 95, "Bob": 92, "Alice": 85, "Charlie": 78}

# Объединение словарей
dict1, dict2, dict3 = {"a": 1}, {"b": 2}, {"c": 3}
combined = {**dict1, **dict2, **dict3}    # Python 3.5+
combined = dict1 | dict2 | dict3          # Python 3.9+

# Кэширование
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive(x):
    return x ** 2

# Безопасный доступ к вложенному словарю
users = {"user1": {"name": "Alice", "age": 25}}
users.get("user99", {}).get("name", "Unknown")   # → "Unknown"
```

---

## 💡 Практические замечания

- Используй `.get()` вместо `[]` когда ключ может отсутствовать
- `dict.fromkeys()` — все ключи получат одно и то же значение (осторожно с изменяемыми!)
- `defaultdict` удобнее `setdefault` для группировки и подсчёта
- Для кэширования функций — `@lru_cache`, не ручной `dict`
- Изменение словаря во время итерации вызовет `RuntimeError` — итерируйся по `list(d.keys())`

---

## ⚠️ Частые ошибки

**❌ Ключ — изменяемый объект:**
```python
d = {[1, 2]: "value"}           # ❌ TypeError — список не хешируемый
d = {(1, 2): "value"}           # ✅ кортеж можно
```

**❌ Изменение словаря во время итерации:**
```python
for key in d:
    if key == "b":
        del d[key]               # ❌ RuntimeError!

for key in list(d.keys()):
    if key == "b":
        del d[key]               # ✅
```

**❌ Присваивание вместо копирования:**
```python
copy = original                  # ❌ та же ссылка
copy = original.copy()           # ✅ независимая копия
```

**❌ KeyError при доступе по несуществующему ключу:**
```python
d["salary"]                      # ❌ KeyError если нет ключа
d.get("salary", 0)               # ✅ безопасно
```

**❌ fromkeys с изменяемым значением:**
```python
d = dict.fromkeys(["a", "b"], [])
d["a"].append(1)
# → {"a": [1], "b": [1]}        # ❌ оба ключа указывают на один список!

d = {k: [] for k in ["a", "b"]} # ✅ каждый получает свой список
```

---

## ✅ Главные правила

✅ Ключи — только неизменяемые объекты (`str`, `int`, `tuple`, `frozenset`)  
✅ Ключи уникальны — дублирующийся ключ перезапишет старое значение  
✅ Порядок вставки сохраняется (Python 3.7+)  
✅ `.get(key, default)` — безопасный доступ без `KeyError`  
✅ `defaultdict` — когда нужно значение по умолчанию для новых ключей  
✅ Итерируйся по `list(d.keys())` если изменяешь словарь в процессе  
✅ `.copy()` — поверхностная копия, `deepcopy()` — для вложенных структур  

---

## 🔗 Связанные темы

- [[04.1  ⤷ Структура словаря Python]]
- [[04.1 .0 ⤷ Структура словаря Python📖 Устройство словаря (dict) под капотом]]
- [[04.2  ⤷ Упорядоченность и хранение словаря]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[03 — ⚡ Множества (set) 🔹]]
- [[00 — 🗂️ Памятка Коллекции Python]]

---

#python/типы_данных #dict #коллекции
