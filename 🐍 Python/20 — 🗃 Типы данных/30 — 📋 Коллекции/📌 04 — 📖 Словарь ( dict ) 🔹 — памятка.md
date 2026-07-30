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

# 📖 Словарь (`dict`) — памятка

> Изменяемая коллекция пар **ключ → значение** на хэш-таблице. С Python 3.7+ сохраняет порядок вставки. Доступ по ключу — **O(1)** в среднем.

**Подробная версия:** [[04 — 📖 Словарь ( dict ) 🔹]]

## Содержание

- [[#⚡ Шпаргалка за 30 секунд|Шпаргалка за 30 секунд]]
- [[#📊 Характеристики и Big O|Характеристики и Big O]]
- [[#🟢 Создание|Создание]]
- [[#🔵 Чтение и проверка|Чтение и проверка]]
- [[#🔴 Изменение и объединение|Изменение и объединение]]
- [[#🟡 Удаление|Удаление]]
- [[#🟣 Итерация и представления|Итерация и представления]]
- [[#📋 Копирование|Копирование]]
- [[#🎯 Паттерны — когда и как|Паттерны]]
- [[#🔧 collections|defaultdict, Counter, OrderedDict]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#🧭 Когда выбирать dict|Когда выбирать dict]]

---

## ⚡ Шпаргалка за 30 секунд

```python
d = {"name": "John", "age": 25}     # создать
d["city"] = "Moscow"                # добавить / изменить
d.get("salary", 0)                    # безопасно прочитать
"name" in d                           # проверить ключ
d.update({"age": 26})                 # обновить несколько пар
d | other                             # объединить (3.9+)
del d["age"]                          # удалить
d.pop("name", None)                   # удалить и вернуть
for k, v in d.items(): ...            # итерироваться
```

| Задача | Как | O |
|---|---|---|
| Создать | `{}`, `dict()`, comprehension | O(n) |
| Прочитать | `d[key]`, `.get(key, default)` | O(1) |
| Записать | `d[key] = value` | O(1) |
| Проверить ключ | `key in d` | O(1) |
| Удалить | `del`, `.pop()`, `.popitem()` | O(1) |
| Объединить | `.update()`, `\|`, `{**a, **b}` | O(k) |
| Длина | `len(d)` | O(1) |

---

## 📊 Характеристики и Big O

```
{ ключ: значение }
   │       └── любой объект
   └── str, int, tuple, frozenset… (только неизменяемые)

Внутри: (hash, key, value) + указатели порядка
```

| Свойство | Значение |
|---|---|
| Изменяемый | ✅ |
| Упорядоченный | ✅ (3.7+) |
| Ключи уникальны | ✅ (дубликат перезаписывает) |
| Ключи хешируемые | ✅ обязательно |
| Индексация | по ключу, не по числу |

| Операция | Сложность |
|---|---|
| `d[key]`, `d[key]=v`, `del d[key]`, `in` | O(1)* |
| `.get()`, `.pop()`, `.setdefault()` | O(1)* |
| `.keys()`, `.values()`, `.items()` | O(1) — view |
| `list(d.keys())` | O(n) |
| `.update()`, `\|` | O(k) |
| Итерация по n элементам | O(n) |

\* в среднем; worst case O(n) при коллизиях хэшей

---

## 🟢 Создание

```python
{}                                  # пустой
{"a": 1, "b": 2}                    # литерал
dict(a=1, b=2)                      # именованные аргументы
dict([("a", 1), ("b", 2)])          # из пар
dict(zip(keys, values))             # из двух последовательностей
dict.fromkeys(["a", "b"], 0)        # ключи + одно значение по умолчанию

# comprehension
{x: x**2 for x in range(5)}
{k: v for k, v in pairs if v > 0}
```

**Важно:** `fromkeys(["a", "b"], [])` — **один** список на все ключи. Для отдельных списков: `{k: [] for k in keys}`.

---

## 🔵 Чтение и проверка

```python
d["key"]              # KeyError, если ключа нет
d.get("key")          # None, если нет
d.get("key", 0)       # default, если нет

"key" in d            # True / False
len(d)                # количество пар
bool(d)               # False для {}
```

| Ситуация | Используй |
|---|---|
| Ключ точно есть | `d[key]` |
| Ключ может отсутствовать | `.get(key, default)` |
| Вложенный dict | `d.get("outer", {}).get("inner", default)` |

---

## 🔴 Изменение и объединение

```python
d["new"] = 42                       # добавить / перезаписать

d.setdefault("k", [])               # создать k=[], если нет; вернуть текущее
d.setdefault("k", []).append(1)    # частый паттерн

d.update({"a": 1, "b": 2})
d.update(c=3, d=4)

# объединение
d1 | d2                             # новый dict (3.9+), правый побеждает при конфликте
d1 |= d2                            # in-place
{**d1, **d2}                        # распаковка (3.5+)
```

---

## 🟡 Удаление

```python
del d["key"]            # KeyError, если нет
d.pop("key")            # удалить и вернуть значение
d.pop("key", None)      # без ошибки — вернуть default
d.popitem()             # последняя вставленная пара (3.7+)
d.clear()               # очистить
```

---

## 🟣 Итерация и представления

```python
for k in d: ...                    # по ключам
for v in d.values(): ...
for k, v in d.items(): ...

sorted(d)                          # ключи по алфавиту
sorted(d, key=d.get)               # ключи по значению
sorted(d.items(), key=lambda x: x[1], reverse=True)

d.keys()    # dict_keys  — «живое» представление
d.values()  # dict_values
d.items()   # dict_items
```

> Представления отражают изменения исходного словаря. Нужен снимок — `list(d.keys())`.

**Сортировка в новый dict:**

```python
top = dict(sorted(scores.items(), key=lambda x: x[1], reverse=True))
```

---

## 📋 Копирование

```python
shallow = d.copy()      # или dict(d), или {**d}
ref = d                 # ❌ та же ссылка, не копия

import copy
deep = copy.deepcopy(nested_dict)   # для вложенных структур
```

---

## 🎯 Паттерны — когда и как

### Подсчёт

```python
from collections import Counter
Counter("hello world".split())

# без Counter
counts = {}
for word in words:
    counts[word] = counts.get(word, 0) + 1
```

### Группировка

```python
from collections import defaultdict

by_grade = defaultdict(list)
for s in students:
    by_grade[s["grade"]].append(s["name"])
```

### Инверсия ключ ↔ значение

```python
{v: k for k, v in d.items()}                    # значения уникальны
inv = {}
for k, v in d.items():
    inv.setdefault(v, []).append(k)             # значения могут повторяться
```

### Фильтрация

```python
high = {k: v for k, v in scores.items() if v >= 90}
picked = {k: scores[k] for k in ("Alice", "Bob")}
```

### Конфиг / вложенность

```python
config = {
    "db": {"host": "localhost", "port": 5432},
    "debug": True,
}
port = config.get("db", {}).get("port", 5432)
```

### Кэш

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fib(n): ...
```

> Ручной `cache = {}` — для простых случаев; для функций предпочтительнее `@lru_cache`.

---

## 🔧 collections

| Класс | Зачем |
|---|---|
| `defaultdict(factory)` | авто-значение для новых ключей (счётчик, группировка) |
| `Counter` | подсчёт элементов, `.most_common(n)` |
| `OrderedDict` | `move_to_end()` — редко нужен с 3.7+ |
| `ChainMap` | «объединённый» вид нескольких dict без копии |

```python
from collections import defaultdict, Counter, OrderedDict, ChainMap

dd = defaultdict(int)
dd["a"] += 1

Counter(["a", "b", "a"]).most_common(1)   # [('a', 2)]

od = OrderedDict(a=1, b=2)
od.move_to_end("a", last=False)

cm = ChainMap(env, defaults)              # env перекрывает defaults
```

---

## ⚠️ Частые ошибки

**❌ Изменяемый ключ**

```python
d = {[1, 2]: "x"}       # TypeError
d = {(1, 2): "x"}       # ✅
```

**❌ Изменение во время итерации**

```python
for k in d:
    del d[k]            # RuntimeError

for k in list(d):
    del d[k]            # ✅
```

**❌ Ссылка вместо копии**

```python
b = a                   # изменения в b затронут a
b = a.copy()            # ✅
```

**❌ `fromkeys` + изменяемое значение**

```python
d = dict.fromkeys("ab", [])
d["a"].append(1)        # {"a": [1], "b": [1]} — один список!
d = {k: [] for k in "ab"}   # ✅
```

**❌ `KeyError` vs `.get()`**

```python
d["missing"]            # KeyError
d.get("missing", 0)     # ✅
```

---

## 🧭 Когда выбирать dict

| Нужно | Коллекция |
|---|---|
| Быстрый доступ по имени / id | **dict** |
| Упорядоченный список однотипных элементов | `list` |
| Только уникальные элементы, без пар | `set` |
| Неизменяемая запись с индексом | `tuple` |

**dict подходит для:** конфигов, кэша, подсчёта, группировки, JSON-подобных данных, атрибутов «ключ → значение».

**dict не подходит для:** упорядоченного списка с частым доступом по индексу, множества уникальных значений без связи с ключом.

---

## ✅ Главные правила

- Ключи — **неизменяемые** и **уникальные**
- Для «может не быть» — **`.get()`**, не `[]`
- Группировка / счётчик — **`defaultdict`** или **`Counter`**
- Меняешь dict в цикле — итерируй **`list(d)`**
- Вложенность — **`deepcopy`**, не `.copy()`
- Устройство под капотом — в отдельных заметках, не здесь

---

## 🔗 Связанные темы

- [[04 — 📖 Словарь ( dict ) 🔹]] — полная версия с расширенными примерами
- [[04.1  ⤷ Структура словаря Python]]
- [[04.1 .0 ⤷ Структура словаря Python📖 Устройство словаря (dict) под капотом]]
- [[04.2  ⤷ Упорядоченность и хранение словаря]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[03 — ⚡ Множества (set) 🔹]]
- [[00 — 🗂️ Памятка Коллекции Python]]
- [[Counter]]
- [[defaultdict]]
- [[OrderedDict]]
- [[Все про Хеш-функции]]
