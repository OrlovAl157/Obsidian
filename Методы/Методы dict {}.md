## 💡 Полезные советы

- **Словари изменяемы**: можно добавлять, изменять и удалять элементы
- **Ключи уникальны**: дублирующиеся ключи перезаписываются
- **Быстрый доступ**: поиск по ключу работает за O(1)
- **Порядок сохраняется**: с Python 3.7+ словари упорядочены
- **Используйте .get()**: безопасный доступ без KeyError
- **.setdefault()**: удобно для счётчиков и накопления
- **.update()**: обновить несколько значений сразу
- **Ключи неизменяемы**: строки, числа, кортежи (не списки!)

## 🚀 Быстрая справка - ВСЕ методы словаря

|Задача|Метод/Оператор|Пример|Результат|
|---|---|---|---|
|**СОЗДАНИЕ**||||
|Создать пустой словарь|`{}` или `dict()`|`d = {}`|`{}`|
|Создать с элементами|`{key: value}`|`{"a": 1, "b": 2}`|`{'a': 1, 'b': 2}`|
|Из пар|`dict(pairs)`|`dict([("a", 1)])`|`{'a': 1}`|
|Из kwargs|`dict(key=value)`|`dict(a=1, b=2)`|`{'a': 1, 'b': 2}`|
|С default значениями|`.fromkeys()`|`dict.fromkeys(["a"], 0)`|`{'a': 0}`|
|**ДОСТУП**||||
|Получить значение|`[key]`|`d["a"]`|`1` (KeyError если нет)|
|Безопасно получить|`.get(key)`|`d.get("a")`|`1` (None если нет)|
|С default|`.get(key, default)`|`d.get("z", 0)`|`0`|
|Все ключи|`.keys()`|`d.keys()`|`dict_keys(['a', 'b'])`|
|Все значения|`.values()`|`d.values()`|`dict_values([1, 2])`|
|Пары|`.items()`|`d.items()`|`dict_items([('a', 1)])`|
|**ИЗМЕНЕНИЕ**||||
|Добавить/изменить|`[key] = value`|`d["c"] = 3`|`{'a': 1, 'c': 3}`|
|Добавить если нет|`.setdefault(key, val)`|`d.setdefault("z", 0)`|`0` (добавит)|
|Обновить|`.update(dict)`|`d.update({"c": 3})`|обновляет d|
|Объединить 3.9+|`\|`|`d1 \| d2`|новый словарь|
|**УДАЛЕНИЕ**||||
|Удалить|`del dict[key]`|`del d["a"]`|KeyError если нет|
|Удалить и вернуть|`.pop(key)`|`d.pop("a")`|`1` (KeyError если нет)|
|С default|`.pop(key, default)`|`d.pop("z", 0)`|`0` (без ошибки)|
|Удалить последний|`.popitem()`|`d.popitem()`|`('b', 2)`|
|Очистить|`.clear()`|`d.clear()`|`{}`|
|**ПРОВЕРКИ**||||
|Есть ли ключ|`in`|`"a" in d`|`True/False`|
|Длина|`len()`|`len(d)`|`2`|
|**КОПИРОВАНИЕ**||||
|Копия|`.copy()`|`d2 = d.copy()`|новый словарь|

## 📝 Доступ к элементам (самое частое!)

### Получение значений

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# ========== [key] - прямой доступ ==========
user["name"]                    # → "Alice"
user["age"]                     # → 25
# user["salary"]                # ❌ KeyError!

# ========== .get() - безопасный доступ ==========
user.get("name")                # → "Alice"
user.get("salary")              # → None (без ошибки!)
user.get("salary", 0)           # → 0 (default значение)

# Практическое применение
salary = user.get("salary", 50000)  # Если нет, то 50000
print(f"Salary: {salary}")

# С условием
if user.get("admin"):
    print("Administrator")
else:
    print("Regular user")
```

### Получение всех ключей, значений и пар

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# ========== .keys() - все ключи ==========
keys = user.keys()              # → dict_keys(['name', 'age', 'city'])
print(list(keys))               # → ['name', 'age', 'city']

for key in user.keys():
    print(key)                  # → name, age, city

# ========== .values() - все значения ==========
values = user.values()          # → dict_values(['Alice', 25, 'Moscow'])
print(list(values))             # → ['Alice', 25, 'Moscow']

for value in user.values():
    print(value)                # → Alice, 25, Moscow

# ========== .items() - пары ключ-значение ==========
items = user.items()            # → dict_items([('name', 'Alice'), ...])
print(list(items))              # → [('name', 'Alice'), ('age', 25), ...]

for key, value in user.items():
    print(f"{key}: {value}")    # → name: Alice, age: 25, ...

# Практические примеры
# Все значения больше 20
large_values = [v for v in user.values() if isinstance(v, int) and v > 20]

# Ключи, содержащие 'a'
keys_with_a = [k for k in user.keys() if 'a' in k]
```

## ➕ Добавление и изменение (очень частое!)

### Прямое присваивание

python

```python
user = {"name": "Alice"}

# Добавить новый ключ
user["age"] = 25                # → {"name": "Alice", "age": 25}
user["city"] = "Moscow"         # → добавлено "city": "Moscow"

# Изменить существующий
user["age"] = 26                # → возраст изменён на 26

# Множественное добавление
user["job"] = "Developer"
user["salary"] = 50000
user["active"] = True
```

### .setdefault() - добавить если нет

python

```python
user = {"name": "Alice", "age": 25}

# Если ключ есть - вернёт значение, не изменит
user.setdefault("name", "Bob")  # → "Alice" (не изменилось)

# Если ключа нет - добавит с указанным значением
user.setdefault("city", "Moscow")  # → "Moscow" (добавлено)

# Практическое применение - счётчики
words = ["apple", "banana", "apple", "cherry", "banana"]
word_count = {}

for word in words:
    word_count.setdefault(word, 0)
    word_count[word] += 1
# → {"apple": 2, "banana": 2, "cherry": 1}

# Группировка
students = [
    {"name": "Alice", "class": "A"},
    {"name": "Bob", "class": "B"},
    {"name": "Charlie", "class": "A"}
]

by_class = {}
for student in students:
    class_name = student["class"]
    by_class.setdefault(class_name, [])
    by_class[class_name].append(student["name"])
# → {"A": ["Alice", "Charlie"], "B": ["Bob"]}
```

### .update() - обновить несколько значений

python

```python
user = {"name": "Alice", "age": 25}

# Из словаря
user.update({"age": 26, "city": "Moscow"})
# → {"name": "Alice", "age": 26, "city": "Moscow"}

# Из списка пар
user.update([("job", "Developer"), ("salary", 50000)])

# Из ключевых аргументов
user.update(active=True, verified=True)

# Комбинирование
user.update({"age": 27}, job="Manager", city="SPb")

# Практическое применение - слияние настроек
default_config = {"timeout": 30, "retries": 3, "debug": False}
user_config = {"timeout": 60, "debug": True}

config = default_config.copy()
config.update(user_config)
# → {"timeout": 60, "retries": 3, "debug": True}
```

## ➖ Удаление элементов (частое)

### del - удалить по ключу

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# Удалить элемент
del user["city"]                # → {"name": "Alice", "age": 25}
# del user["salary"]            # ❌ KeyError!

# С проверкой
if "city" in user:
    del user["city"]
```

### .pop() - удалить и вернуть значение

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# Удалить и получить значение
age = user.pop("age")           # → 25
print(user)                     # → {"name": "Alice", "city": "Moscow"}

# Если ключа нет - ошибка
# salary = user.pop("salary")   # ❌ KeyError!

# С default значением (безопасно)
salary = user.pop("salary", 0)  # → 0 (без ошибки)

# Практическое применение
def process_user(data):
    # Извлечь и удалить временные поля
    temp_id = data.pop("temp_id", None)
    debug = data.pop("debug", False)
    
    # Работать с основными данными
    save_to_database(data)
```

### .popitem() - удалить последний элемент

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# Удалить и вернуть последнюю пару
item = user.popitem()           # → ("city", "Moscow")
print(user)                     # → {"name": "Alice", "age": 25}

# На пустом словаре - ошибка
# {}.popitem()                  # ❌ KeyError!

# Практическое применение - LIFO стек
stack = {"first": 1, "second": 2, "third": 3}
while stack:
    key, value = stack.popitem()
    print(f"{key}: {value}")
```

### .clear() - удалить всё

python

```python
user = {"name": "Alice", "age": 25}

user.clear()                    # → {}
print(user)                     # → {}

# Практическое применение
cache = {"key1": "value1", "key2": "value2"}
# Очистить кэш
cache.clear()
```

## 🔍 Проверки (очень частое!)

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# ========== in - проверка наличия ключа ==========
"name" in user                  # → True
"salary" in user                # → False
"salary" not in user            # → True

# Практическое использование
if "email" in user:
    print(f"Email: {user['email']}")
else:
    print("Email not provided")

# С условным выражением
email = user["email"] if "email" in user else "No email"

# ========== len() - количество элементов ==========
len(user)                       # → 3

# Проверка на пустоту
if user:
    print("User has data")      # → True
else:
    print("User is empty")

empty = {}
if not empty:
    print("Dictionary is empty")  # → True
```

## 📋 Копирование (важное!)

python

```python
original = {"name": "Alice", "age": 25}

# ========== .copy() - поверхностная копия ==========
copy1 = original.copy()
copy1["age"] = 30
print(original["age"])          # → 25 (не изменилось)

# Альтернативы
copy2 = dict(original)
copy3 = {**original}

# ⚠️ Присваивание НЕ создаёт копию!
reference = original
reference["age"] = 30
print(original["age"])          # → 30 (изменилось!)

# ========== Глубокая копия для вложенных словарей ==========
import copy

nested = {
    "name": "Alice",
    "address": {"city": "Moscow", "street": "Main"}
}

# Поверхностная копия - НЕ копирует вложенные
shallow = nested.copy()
shallow["address"]["city"] = "SPb"
print(nested["address"]["city"])  # → "SPb" (изменилось!)

# Глубокая копия - копирует всё
deep = copy.deepcopy(nested)
deep["address"]["city"] = "Kazan"
print(nested["address"]["city"])  # → "SPb" (не изменилось!)
```

## 🎯 Создание словарей (частое!)

python

```python
# ========== Пустой словарь ==========
d = {}
d = dict()

# ========== С элементами ==========
user = {"name": "Alice", "age": 25}

# ========== dict() из пар ==========
pairs = [("name", "Alice"), ("age", 25)]
user = dict(pairs)

# Из zip
keys = ["name", "age", "city"]
values = ["Alice", 25, "Moscow"]
user = dict(zip(keys, values))

# ========== dict() из kwargs ==========
user = dict(name="Alice", age=25, city="Moscow")

# ========== .fromkeys() - создать с одинаковыми значениями ==========
# Все значения 0
counters = dict.fromkeys(["a", "b", "c"], 0)
# → {"a": 0, "b": 0, "c": 0}

# Все значения None
fields = dict.fromkeys(["name", "age", "city"])
# → {"name": None, "age": None, "city": None}

# Практическое применение
# Инициализация счётчиков
letters = ["a", "b", "c", "a", "b"]
counter = dict.fromkeys(set(letters), 0)
# → {"a": 0, "b": 0, "c": 0}

# ⚠️ Осторожно с изменяемыми значениями!
d = dict.fromkeys(["a", "b"], [])
d["a"].append(1)
print(d)                        # → {"a": [1], "b": [1]} (обе изменились!)

# ✅ Правильно для изменяемых значений
d = {key: [] for key in ["a", "b"]}
d["a"].append(1)
print(d)                        # → {"a": [1], "b": []}
```

## 🔄 Итерация (очень частое!)

python

```python
user = {"name": "Alice", "age": 25, "city": "Moscow"}

# ========== По ключам (по умолчанию) ==========
for key in user:
    print(key)                  # → name, age, city

# Явно через .keys()
for key in user.keys():
    print(key)

# ========== По значениям ==========
for value in user.values():
    print(value)                # → Alice, 25, Moscow

# ========== По парам ключ-значение ==========
for key, value in user.items():
    print(f"{key}: {value}")    # → name: Alice, age: 25, ...

# ========== С enumerate ==========
for i, (key, value) in enumerate(user.items()):
    print(f"{i}: {key} = {value}")

# ========== Отфильтрованная итерация ==========
# Только числовые значения
for key, value in user.items():
    if isinstance(value, int):
        print(f"{key}: {value}")

# ========== Отсортированная итерация ==========
# По ключам
for key in sorted(user.keys()):
    print(f"{key}: {user[key]}")

# По значениям
for key in sorted(user, key=user.get):
    print(f"{key}: {user[key]}")
```

## 🔧 Объединение словарей (Python 3.9+)

python

```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
dict3 = {"b": 20, "e": 5}

# ========== Оператор | - объединение ==========
combined = dict1 | dict2
# → {"a": 1, "b": 2, "c": 3, "d": 4}

# При совпадении ключей - берётся из правого
combined = dict1 | dict3
# → {"a": 1, "b": 20, "e": 5}

# Цепочка объединений
all_combined = dict1 | dict2 | dict3
# → {"a": 1, "b": 20, "c": 3, "d": 4, "e": 5}

# ========== Оператор |= - объединение на месте ==========
dict1 |= dict2                  # Изменяет dict1
print(dict1)                    # → {"a": 1, "b": 2, "c": 3, "d": 4}

# ========== Для Python < 3.9 используйте .update() ==========
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
result = {**dict1, **dict2}     # Распаковка словарей
```

## 🎯 Практические примеры

### Подсчёт элементов

python

```python
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]

# Через .setdefault()
count = {}
for word in words:
    count.setdefault(word, 0)
    count[word] += 1
# → {"apple": 3, "banana": 2, "cherry": 1}

# Через .get()
count = {}
for word in words:
    count[word] = count.get(word, 0) + 1

# Лучше используйте Counter
from collections import Counter
count = Counter(words)
```

### Группировка данных

python

```python
students = [
    {"name": "Alice", "grade": "A", "age": 20},
    {"name": "Bob", "grade": "B", "age": 21},
    {"name": "Charlie", "grade": "A", "age": 20}
]

# По оценке
by_grade = {}
for student in students:
    grade = student["grade"]
    by_grade.setdefault(grade, [])
    by_grade[grade].append(student["name"])
# → {"A": ["Alice", "Charlie"], "B": ["Bob"]}

# По возрасту
by_age = {}
for student in students:
    age = student["age"]
    by_age.setdefault(age, [])
    by_age[age].append(student)
```

### Инверсия словаря

python

```python
original = {"a": 1, "b": 2, "c": 3}

# Поменять ключи и значения
inverted = {v: k for k, v in original.items()}
# → {1: "a", 2: "b", 3: "c"}

# Если значения могут повторяться
original = {"a": 1, "b": 2, "c": 1}
inverted = {}
for k, v in original.items():
    inverted.setdefault(v, []).append(k)
# → {1: ["a", "c"], 2: ["b"]}
```

### Слияние с приоритетом

python

```python
defaults = {"timeout": 30, "retries": 3, "debug": False}
user_settings = {"timeout": 60, "debug": True}

# Пользовательские настройки перезаписывают defaults
config = defaults | user_settings  # Python 3.9+
# или
config = {**defaults, **user_settings}
# → {"timeout": 60, "retries": 3, "debug": True}
```

## ⚠️ Частые ошибки

python

```python
# ❌ Прямой доступ к несуществующему ключу
d = {"a": 1}
# value = d["b"]                # KeyError!

# ✅ Используйте .get()
value = d.get("b", 0)           # → 0

# ❌ Изменение словаря во время итерации
d = {"a": 1, "b": 2, "c": 3}
# for key in d:
#     if key == "b":
#         del d[key]            # RuntimeError!

# ✅ Итерируйте по копии ключей
for key in list(d.keys()):
    if key == "b":
        del d[key]              # ✅ Работает

# ❌ Присваивание вместо копирования
d1 = {"a": 1}
d2 = d1                         # Не копия!
d2["a"] = 2
print(d1["a"])                  # → 2 (изменился!)

# ✅ Используйте .copy()
d2 = d1.copy()

# ❌ .fromkeys() с изменяемым значением
d = dict.fromkeys(["a", "b"], [])
d["a"].append(1)
print(d)                        # → {"a": [1], "b": [1]} (оба!)

# ✅ Используйте dict comprehension
d = {key: [] for key in ["a", "b"]}
```

## 🎯 Когда использовать какой метод

**Используйте `.get()` когда:**

- Ключ может отсутствовать
- Нужно значение по умолчанию
- Хотите избежать try-except

**Используйте `.setdefault()` когда:**

- Строите счётчики
- Группируете данные
- Нужно добавить если нет, иначе использовать существующее

**Используйте `.update()` когда:**

- Обновляете несколько значений
- Сливаете настройки
- Добавляете данные из другого источника

**Используйте `.pop()` когда:**

- Нужно удалить и использовать значение
- Извлекаете временные поля
- Обрабатываете данные с удалением

**Используйте `.items()` когда:**

- Нужны и ключи, и значения
- Итерируетесь по словарю
- Фильтруете или преобразуете пары