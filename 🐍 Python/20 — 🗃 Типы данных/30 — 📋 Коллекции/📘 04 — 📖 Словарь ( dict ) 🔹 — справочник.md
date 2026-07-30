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

# 📘 Словарь (`dict`) — справочник

> Альтернативная структура той же темы. Полная версия в другом формате: [[04 — 📖 Словарь ( dict ) 🔹]]

---

## Навигация

| Часть | О чём |
|---|---|
| [[#Часть I — Что такое dict и как он устроен\|I. Теория и модель]] | хэш-таблица, память, ключи, порядок, Big O |
| [[#Часть II — API по задачам\|II. API по задачам]] | создание, чтение, запись, удаление, обход |
| [[#Часть III — Рецепты из практики\|III. Рецепты]] | подсчёт, группировка, фильтрация, конфиг, кэш… |
| [[#Часть IV — Специализированные словари\|IV. collections]] | defaultdict, Counter, OrderedDict, ChainMap |
| [[#Часть V — Ошибки и выбор коллекции\|V. Ошибки и выбор]] | подводные камни, dict vs list vs set |
| [[#Приложение — Таблица методов\|Приложение]] | все методы в одной таблице |

---

# Часть I — Что такое dict и как он устроен

## 1.1 Определение

`dict` — изменяемая коллекция пар **«ключ : значение»**. Внутри — **хэш-таблица**: ключ превращается в хэш, по нему находится ячейка со значением.

```python
person = {
    "name": "John",    # str → str
    "age": 25,         # str → int
    "tags": ["dev"],   # str → list  (значение может быть любым)
}
```

> До Python 3.7 порядок элементов **не гарантировался** официально. С **3.7+** порядок вставки — часть спецификации языка.

---

## 1.2 Модель в памяти

```
┌─────────────────────────────────────────────────────────┐
│  dict                                                    │
│                                                          │
│  Хэш-таблица:  (hash, key, value)  × N ячеек            │
│  + двусвязный список для порядка вставки (CPython 3.7+) │
└─────────────────────────────────────────────────────────┘

  ключ  ──hash()──►  индекс ячейки  ──►  value
```

| Аспект | Как устроено |
|---|---|
| **Хранение** | Уникальные ключи + связанные значения (полноценные объекты) |
| **Структура ячейки** | `(hash, key, value)` |
| **Порядок** | Двусвязный список указателей (с 3.7+) |
| **Память** | Больше, чем у `list` / `tuple` — хэши, ключи, значения, указатели |
| **Поиск / вставка / удаление** | O(1) в среднем; O(n) в худшем случае при массовых коллизиях |

> Подробнее о внутреннем устройстве: [[04.1  ⤷ Структура словаря Python]], [[04.1 .0 ⤷ Структура словаря Python📖 Устройство словаря (dict) под капотом]], [[04.2  ⤷ Упорядоченность и хранение словаря]]

---

## 1.3 Правила ключей и значений

### Ключи

| Правило | Пояснение |
|---|---|
| **Неизменяемые** | `str`, `int`, `float`, `bool`, `tuple`, `frozenset`, `None` |
| **Хешируемые** | Должны поддерживать `hash()` и `==` |
| **Уникальны** | Повторный ключ **перезаписывает** старое значение |
| **Не подходят** | `list`, `dict`, `set` — `TypeError` |

```python
d = {"key": "value"}            # str ✅
d = {1: "one", (1, 2): "pair"}  # int, tuple ✅
d = {frozenset([1, 2]): "x"}    # frozenset ✅

# d = {[1, 2]: "x"}             # TypeError — list
# d = {{1, 2}: "x"}             # TypeError — set
# d = {{"a": 1}: "x"}           # TypeError — dict
```

### Значения

- Могут быть **любыми** объектами, в том числе изменяемыми (`list`, `dict`, `set`).
- Изменение значения **не меняет** ключ и не нарушает структуру dict.

```python
d = {"a": [1, 2, 3]}
d["a"].append(4)                # {"a": [1, 2, 3, 4]} — ок

d = {"a": 1, "b": 2, "a": 3}    # {"a": 3, "b": 2} — дубликат ключа
```

---

## 1.4 Сложность операций (Big O)

| Операция | Средний случай | Комментарий |
|---|:-:|---|
| `d[key]`, `d[key] = v` | O(1) | через хэш |
| `del d[key]`, `key in d` | O(1) | |
| `.get()`, `.pop()`, `.setdefault()` | O(1) | |
| `.keys()`, `.values()`, `.items()` | O(1) | возвращают **view**, не копию |
| `list(d.keys())` | O(n) | материализация |
| `.update()`, `\|`, `{**a, **b}` | O(k) | k — число добавляемых пар |
| Итерация по n элементам | O(n) | |
| `len(d)` | O(1) | |

---

## 1.5 Ключевые свойства — сводка

- **Изменяемый** — добавление, изменение, удаление пар в любой момент
- **Упорядоченный** (3.7+) — порядок = порядок вставки
- **Не хешируемый** — нельзя использовать dict как ключ другого dict
- **Индексация** — только по ключу, не по числовому индексу как у list
- **Итерируемый** — по умолчанию обход идёт по **ключам**

---

# Часть II — API по задачам

## 2.1 Создать словарь

```python
# Пустой
empty = {}
empty = dict()

# Литерал
person = {"name": "John", "age": 25, "city": "Moscow"}
numbers = {1: "one", 2: "two", 3: "three"}
mixed = {"a": 1, "b": [1, 2], "c": {"nested": "dict"}}

# Конструктор с именованными аргументами
person = dict(name="John", age=25, city="Moscow")
# → {"name": "John", "age": 25, "city": "Moscow"}

# Из списка кортежей
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = dict(pairs)                 # → {"a": 1, "b": 2, "c": 3}

# Из двух списков (zip)
keys = ["name", "age", "city"]
values = ["John", 25, "Moscow"]
d = dict(zip(keys, values))

# Ключи с одним значением по умолчанию
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

> ⚠️ `dict.fromkeys(keys, [])` — **один** список на все ключи. Для отдельных списков: `{k: [] for k in keys}`.

---

## 2.2 Прочитать значение

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# Строгий доступ — KeyError, если ключа нет
person["name"]                  # → "John"
person["age"]                   # → 25
# person["salary"]              # ❌ KeyError!

# Безопасный доступ
person.get("name")              # → "John"
person.get("salary")            # → None
person.get("salary", 0)         # → 0

# Проверка наличия
"name" in person                # → True
"salary" in person              # → False
"salary" not in person          # → True

# Размер и «пустота»
len(person)                     # → 3
if person:                      # True, если не пустой
    print("Словарь не пустой")
```

| Ситуация | Что использовать |
|---|---|
| Ключ гарантированно есть | `d[key]` |
| Ключ может отсутствовать | `d.get(key, default)` |
| Вложенный dict | `d.get("outer", {}).get("inner", default)` |

---

## 2.3 Изменить или добавить

```python
person = {"name": "John", "age": 25}

# Новая пара / перезапись
person["city"] = "Moscow"
person["country"] = "Russia"
person["age"] = 26

# setdefault — создать только если ключа нет
person.setdefault("name", "Bob")      # → "John" (не изменилось)
person.setdefault("salary", 50000)    # добавлено

# Массовое обновление
person.update({"age": 27, "city": "SPb"})
person.update(job="Developer", salary=60000)

# Объединение (Python 3.9+) — правый побеждает при конфликте
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
combined = dict1 | dict2        # → {"a": 1, "b": 2, "c": 3, "d": 4}

dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}
combined = dict1 | dict2        # → {"a": 1, "b": 3, "c": 4}

dict1 |= dict2                  # in-place
```

**Способы объединения:**

| Способ | Версия | Создаёт копию? |
|---|---|---|
| `.update()` | любая | нет (меняет текущий) |
| `{**a, **b}` | 3.5+ | да |
| `a \| b` | 3.9+ | да |
| `ChainMap(a, b)` | любая | да (логическое объединение без копии данных) |

---

## 2.4 Удалить

```python
person = {"name": "John", "age": 25, "city": "Moscow", "job": "Developer"}

del person["job"]               # KeyError, если ключа нет
# del person["salary"]          # ❌ KeyError!

age = person.pop("age")         # → 25, ключ удалён
salary = person.pop("salary", 0)  # → 0 без ошибки

item = person.popitem()         # последняя вставленная пара (3.7+)
# → ("city", "Moscow")

person.clear()                  # → {}
```

---

## 2.5 Обойти и получить представления

```python
person = {"name": "John", "age": 25, "city": "Moscow"}

# По ключам (поведение for по умолчанию)
for key in person:
    print(key)

for key in person.keys():       # то же самое явно

# По значениям
for value in person.values():
    print(value)

# По парам
for key, value in person.items():
    print(f"{key}: {value}")

# С индексом
for i, (key, value) in enumerate(person.items()):
    print(f"{i}: {key} = {value}")

# Сортировка
for key in sorted(person.keys()):
    print(f"{key}: {person[key]}")

for key in sorted(person, key=lambda k: person[k]):
    print(f"{key}: {person[key]}")
```

**Представления (views)** — «живые», отражают изменения исходного dict:

```python
person.keys()     # dict_keys
person.values()   # dict_values
person.items()    # dict_items

list(person.keys())
list(person.values())
list(person.items())

dict(person.items())            # обратно в словарь
```

---

## 2.6 Скопировать

```python
original = {"a": 1, "b": 2, "c": 3}

# Поверхностная копия — три эквивалентных способа
copy1 = original.copy()
copy2 = dict(original)
copy3 = {**original}

# Присваивание — НЕ копия
reference = original
reference["d"] = 4              # изменит и original!

# Глубокая копия для вложенности
import copy
nested = {"a": {"b": 1}}
deep = copy.deepcopy(nested)
deep["a"]["b"] = 2              # nested не изменится
```

| Тип копии | Когда |
|---|---|
| Поверхностная (`.copy()`) | Плоский dict или копируешь только верхний уровень |
| Глубокая (`deepcopy`) | Вложенные dict / list внутри значений |

---

# Часть III — Рецепты из практики

## 3.1 Подсчёт элементов

```python
text = "hello world hello python world"
words = text.split()

# Вручную через get
word_count = {}
for word in words:
    word_count[word] = word_count.get(word, 0) + 1
# → {"hello": 2, "world": 2, "python": 1}

# Через setdefault
word_count = {}
for word in words:
    word_count.setdefault(word, 0)
    word_count[word] += 1

# Counter — предпочтительно для подсчёта
from collections import Counter
word_count = Counter(words)
# → Counter({"hello": 2, "world": 2, "python": 1})

# Подсчёт символов
text = "hello"
char_count = {char: text.count(char) for char in set(text)}
# → {"h": 1, "e": 1, "l": 2, "o": 1}
```

---

## 3.2 Группировка данных

```python
students = [
    {"name": "Alice", "grade": "A"},
    {"name": "Bob", "grade": "B"},
    {"name": "Charlie", "grade": "A"},
    {"name": "David", "grade": "B"},
]

by_grade = {}
for student in students:
    grade = student["grade"]
    by_grade.setdefault(grade, [])
    by_grade[grade].append(student["name"])
# → {"A": ["Alice", "Charlie"], "B": ["Bob", "David"]}

from collections import defaultdict
by_grade = defaultdict(list)
for student in students:
    by_grade[student["grade"]].append(student["name"])
```

---

## 3.3 Инверсия словаря

```python
# Значения уникальны
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
# → {1: "a", 2: "b", 3: "c"}

# Значения могут повторяться
original = {"a": 1, "b": 2, "c": 1}
inverted = {}
for k, v in original.items():
    inverted.setdefault(v, []).append(k)
# → {1: ["a", "c"], 2: ["b"]}
```

---

## 3.4 Объединение нескольких словарей

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"e": 5, "f": 6}

combined = {}
combined.update(dict1)
combined.update(dict2)
combined.update(dict3)

combined = {**dict1, **dict2, **dict3}       # 3.5+
combined = dict1 | dict2 | dict3             # 3.9+

from collections import ChainMap
combined = ChainMap(dict1, dict2, dict3)     # без копирования; первый dict имеет приоритет
```

---

## 3.5 Фильтрация

```python
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}

high_scores = {k: v for k, v in scores.items() if v >= 90}
# → {"Bob": 92, "David": 95}

keys_to_keep = ["Alice", "Charlie"]
filtered = {k: scores[k] for k in keys_to_keep}
# → {"Alice": 85, "Charlie": 78}

keys_to_remove = ["Bob"]
filtered = {k: v for k, v in scores.items() if k not in keys_to_remove}
```

---

## 3.6 Сортировка

```python
scores = {"Alice": 85, "Bob": 92, "Charlie": 78, "David": 95}

sorted_by_keys = dict(sorted(scores.items()))
sorted_by_values = dict(sorted(scores.items(), key=lambda x: x[1]))
sorted_desc = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True))
# → {"David": 95, "Bob": 92, "Alice": 85, "Charlie": 78}

top3 = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True)[:3])
```

> `sorted(d)` сортирует **ключи**. Чтобы получить новый dict с другим порядком пар — `dict(sorted(d.items(), ...))`.

---

## 3.7 Вложенные словари

```python
users = {
    "user1": {"name": "Alice", "age": 25, "city": "Moscow"},
    "user2": {"name": "Bob", "age": 30, "city": "SPb"},
    "user3": {"name": "Charlie", "age": 35, "city": "Moscow"},
}

users["user1"]["name"]          # → "Alice"
users["user2"]["age"]           # → 30

users.get("user1", {}).get("name", "Unknown")   # → "Alice"
users.get("user99", {}).get("name", "Unknown")  # → "Unknown"

users["user1"]["age"] = 26
users["user4"] = {"name": "David", "age": 28, "city": "Kazan"}

moscow_users = {k: v for k, v in users.items() if v["city"] == "Moscow"}
```

---

## 3.8 Конфигурация приложения

```python
config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "mydb",
    },
    "server": {
        "host": "0.0.0.0",
        "port": 8000,
    },
    "debug": True,
}

db_host = config["database"]["host"]
server_port = config["server"]["port"]
```

---

## 3.9 Кэширование

```python
# Ручной кэш
cache = {}

def expensive_function(x):
    if x in cache:
        print("Из кэша")
        return cache[x]
    print("Вычисляем...")
    result = x ** 2
    cache[x] = result
    return result

expensive_function(5)           # Вычисляем... → 25
expensive_function(5)           # Из кэша → 25

# Рекомендуемый способ для функций
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(x):
    return x ** 2
```

---

## 3.10 Статистика по списку записей

```python
data = [
    {"name": "Alice", "age": 25, "salary": 50000},
    {"name": "Bob", "age": 30, "salary": 60000},
    {"name": "Charlie", "age": 35, "salary": 70000},
]

avg_age = sum(p["age"] for p in data) / len(data)
total_salary = sum(p["salary"] for p in data)
max_salary = max(p["salary"] for p in data)
max_person = max(data, key=lambda x: x["salary"])
```

---

## 3.11 Преобразование форматов

```python
# CSV-подобные данные → list[dict]
csv_data = [
    ["name", "age", "city"],
    ["Alice", 25, "Moscow"],
    ["Bob", 30, "SPb"],
]
headers = csv_data[0]
users = [dict(zip(headers, row)) for row in csv_data[1:]]
# → [{"name": "Alice", "age": 25, "city": "Moscow"}, ...]

# dict → список пар
person = {"name": "John", "age": 25}
pairs = [[k, v] for k, v in person.items()]
# → [["name", "John"], ["age", 25]]
```

---

# Часть IV — Специализированные словари

## 4.1 `defaultdict` — значение по умолчанию для новых ключей

```python
from collections import defaultdict

counter = defaultdict(int)
counter["a"] += 1
counter["b"] += 1
counter["a"] += 1
# → {"a": 2, "b": 1}

groups = defaultdict(list)
groups["fruits"].append("apple")
groups["fruits"].append("banana")
groups["vegetables"].append("carrot")
# → {"fruits": ["apple", "banana"], "vegetables": ["carrot"]}

tags = defaultdict(set)
tags["post1"].add("python")
tags["post1"].add("tutorial")

counter = defaultdict(lambda: 100)
counter["a"]                    # → 100
```

| factory | Типичное применение |
|---|---|
| `int` | подсчёт (`+= 1`) |
| `list` | группировка (`.append`) |
| `set` | уникальные элементы на ключ |
| `lambda: ...` | произвольное начальное значение |

---

## 4.2 `Counter` — словарь-счётчик

```python
from collections import Counter

Counter("hello")                # подсчёт символов
Counter(["a", "b", "a"])        # подсчёт элементов
c = Counter(words)
c.most_common(2)                # топ-2
```

> См. также: [[Counter]]

---

## 4.3 `OrderedDict` — когда обычного dict мало

```python
from collections import OrderedDict

# С Python 3.7+ обычный dict уже упорядочен.
# OrderedDict нужен для move_to_end и семантики «равенства с учётом порядка».

d = OrderedDict()
d["a"] = 1
d["b"] = 2
d["c"] = 3

d.move_to_end("a")              # → {"b": 2, "c": 3, "a": 1}
d.move_to_end("c", last=False)  # → {"c": 3, "b": 2, "a": 1}
d.popitem(last=True)            # → ("a", 1)
d.popitem(last=False)           # → ("c", 3)
```

> См. также: [[OrderedDict]]

---

## 4.4 `ChainMap` — цепочка словарей без слияния

```python
from collections import ChainMap

defaults = {"color": "red", "size": "M"}
user_prefs = {"color": "blue"}
merged = ChainMap(user_prefs, defaults)

merged["color"]                 # → "blue" (первый dict в цепочке)
merged["size"]                  # → "M"
```

Полезно для конфигов: `ChainMap(env, file_config, defaults)`.

---

# Часть V — Ошибки и выбор коллекции

## 5.1 Подводные камни

### Изменение dict во время итерации

```python
d = {"a": 1, "b": 2, "c": 3}

# for key in d:
#     if key == "b":
#         del d[key]            # ❌ RuntimeError

for key in list(d.keys()):
    if key == "b":
        del d[key]              # ✅
```

### fromkeys с изменяемым значением

```python
d = dict.fromkeys(["a", "b"], [])
d["a"].append(1)
# → {"a": [1], "b": [1]}       # ❌ один список на оба ключа

d = {k: [] for k in ["a", "b"]}  # ✅
```

### Присваивание vs копирование

```python
copy = original                 # ❌ ссылка
copy = original.copy()          # ✅
```

### KeyError

```python
d["missing"]                    # ❌ KeyError
d.get("missing", 0)             # ✅
```

---

## 5.2 Когда что использовать

```
                    Нужен доступ по имени/id?
                              │
                    ┌─────────┴─────────┐
                   да                  нет
                    │                    │
                  dict          Нужны уникальные элементы?
                    │                    │
                    │          ┌─────────┴─────────┐
                    │         да                  нет
                    │          │                    │
                    │         set           Нужен числовой индекс?
                    │                              │
                    │                    ┌─────────┴─────────┐
                    │                   да                  нет
                    │                    │                    │
                    │                  list               tuple
                    │                              (неизменяемый)
```

| Задача | Коллекция |
|---|---|
| Ключ → значение, конфиг, кэш, JSON | **dict** |
| Упорядоченный список, индекс `i` | **list** |
| Только уникальные элементы | **set** |
| Неизменяемая запись | **tuple** |

**Dict — когда:**
- нужна связь ключ-значение
- быстрый доступ по ключу O(1)
- настройки, подсчёт, группировка, кэш, атрибуты объекта

**List — когда:**
- важен порядок и индексация `[0]`
- однотипная коллекция, частое добавление в конец

**Set — когда:**
- только уникальность
- операции ∪ ∩ − без пар ключ-значение

---

# Приложение — Таблица методов

| Задача | Метод / оператор | O | Пример |
|---|---|:-:|---|
| Создать | `{}`, `dict()` | O(n) | `{"a": 1}` |
| Добавить / изменить | `[key] = value` | O(1) | `d["c"] = 3` |
| Прочитать | `.get(key, default)` | O(1) | `d.get("a", 0)` |
| Удалить | `del`, `.pop()` | O(1) | `del d["a"]` |
| Проверить ключ | `in` | O(1) | `"a" in d` |
| Ключи | `.keys()` | O(1)* | `d.keys()` |
| Значения | `.values()` | O(1)* | `d.values()` |
| Пары | `.items()` | O(1)* | `d.items()` |
| Объединить | `.update()`, `\|` | O(k) | `d1 \| d2` |
| Длина | `len()` | O(1) | `len(d)` |
| Очистить | `.clear()` | O(1) | `d.clear()` |
| Pop с возвратом | `.pop(key)` | O(1) | `d.pop("a")` |
| Pop последний | `.popitem()` | O(1) | `d.popitem()` |
| Setdefault | `.setdefault(k, v)` | O(1) | `d.setdefault("x", [])` |
| Копия | `.copy()` | O(n) | `d.copy()` |

\* view создаётся за O(1); обход — O(n)

---

## 🔗 Связанные темы

- [[04 — 📖 Словарь ( dict ) 🔹]] — исходная полная версия
- [[📌 04 — 📖 Словарь ( dict ) 🔹 — памятка]] — краткая шпаргалка
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
