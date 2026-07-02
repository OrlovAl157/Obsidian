-----

## tags: [python, типизация, generics, list, dict, tuple, set]
difficulty: beginner

# 📦 Generics и контейнеры

> **Generics (обобщённые типы)** позволяют указать, какие элементы хранятся в контейнерах. Например, `list[int]` — список целых чисел, `dict[str, int]` — словарь со строками-ключами и целыми-значениями. Это делает код намного понятнее и помогает ловить ошибки. В Python 3.9+ можно использовать встроенные типы прямо: `list[int]`. В более старых версиях — `typing.List[int]`.

## Содержание

- [[#Справка|Справка]]
- [[#📋 List — список элементов|List — список элементов]]
- [[#🔑 Dict — словарь ключ-значение|Dict — словарь ключ-значение]]
- [[#🔗 Tuple — кортеж|Tuple — кортеж]]
- [[#⭕ Set — множество|Set — множество]]
- [[#🎁 Вложенные типы|Вложенные типы]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Контейнер              |Python 3.9+         |Старый стиль        |Пример                                          |
|-----------------------|--------------------|--------------------|------------------------------------------------|
|**List**               |`list[T]`           |`List[T]`           |`list[int]` — список целых чисел                |
|**Dict**               |`dict[K, V]`        |`Dict[K, V]`        |`dict[str, int]` — ключи строки, значения числа |
|**Set**                |`set[T]`            |`Set[T]`            |`set[str]` — множество уникальных строк         |
|**Tuple фиксированная**|`tuple[T1, T2, ...]`|`Tuple[T1, T2, ...]`|`tuple[int, str, float]` — точно 3 элемента     |
|**Tuple переменная**   |`tuple[T, ...]`     |`Tuple[T, ...]`     |`tuple[int, ...]` — любое количество целых чисел|

-----

## 📋 List — список элементов

Список может содержать элементы одного типа:

```python
# ✅ Список целых чисел
numbers: list[int] = [1, 2, 3, 4, 5]

# ✅ Список строк
names: list[str] = ["Alice", "Bob", "Charlie"]

# ❌ Не типизируем (неинформативно)
data: list = [1, "two", 3.0]

# ✅ Если правда несколько типов
mixed: list[int | str] = [1, "two", 3, "four"]
```

**Функции со списками:**

```python
def sum_numbers(numbers: list[int]) -> int:
    """Сумма списка целых чисел"""
    return sum(numbers)

def reverse_list(items: list[str]) -> list[str]:
    """Реверс списка строк"""
    return items[::-1]

def filter_positive(numbers: list[int]) -> list[int]:
    """Оставляет только положительные числа"""
    return [n for n in numbers if n > 0]

# Использование
total = sum_numbers([1, 2, 3])
reversed_names = reverse_list(["A", "B"])
positives = filter_positive([-1, 2, -3, 4])
```

-----

## 🔑 Dict — словарь ключ-значение

Словарь требует два типа: для ключей и для значений:

```python
# ✅ Строки-ключи, целые-значения
ages: dict[str, int] = {
    "Alice": 25,
    "Bob": 30,
    "Charlie": 35
}

# ✅ Целые-ключи, строки-значения
id_to_name: dict[int, str] = {
    1: "Alice",
    2: "Bob",
    3: "Charlie"
}

# ❌ Неинформативно
data: dict = {"key": "value"}

# ✅ Если значения сложные
config: dict[str, int | str | bool] = {
    "port": 8000,
    "host": "localhost",
    "debug": True
}
```

**Функции со словарями:**

```python
def get_age(people: dict[str, int], name: str) -> int:
    """Получить возраст человека"""
    return people.get(name, 0)

def merge_dicts(dict1: dict[str, int], dict2: dict[str, int]) -> dict[str, int]:
    """Объединить два словаря"""
    return {**dict1, **dict2}

def count_occurrences(items: list[str]) -> dict[str, int]:
    """Подсчитать количество каждого элемента"""
    result: dict[str, int] = {}
    for item in items:
        result[item] = result.get(item, 0) + 1
    return result

# Использование
ages = {"Alice": 25, "Bob": 30}
bob_age = get_age(ages, "Bob")

counts = count_occurrences(["a", "b", "a", "c", "a"])
# {"a": 3, "b": 1, "c": 1}
```

-----

## 🔗 Tuple — кортеж (неизменяемый список)

Кортеж может быть:

- **Фиксированной длины** с конкретными типами каждого элемента
- **Переменной длины** с элементами одного типа

```python
# ✅ Фиксированная длина — точно 3 элемента
point: tuple[int, int, int] = (10, 20, 30)

# ✅ Фиксированная длина — разные типы
person: tuple[str, int, bool] = ("Alice", 25, True)

# ✅ Переменная длина — любое количество целых чисел
numbers: tuple[int, ...] = (1, 2, 3, 4, 5)

# ✅ Переменная длина — любое количество строк
words: tuple[str, ...] = ("hello", "world", "python")

# ❌ Неинформативно
data: tuple = (1, "two", 3.0)
```

**Функции с кортежами:**

```python
def get_coordinates() -> tuple[float, float]:
    """Получить координаты точки"""
    return (3.14, 2.71)

def split_string(text: str, sep: str = " ") -> tuple[str, ...]:
    """Разделить строку на части"""
    return tuple(text.split(sep))

def swap(a: int, b: int) -> tuple[int, int]:
    """Поменять два значения местами"""
    return b, a

# Использование
x, y = get_coordinates()
words = split_string("hello world python")
a, b = swap(5, 10)
```

-----

## ⭕ Set — множество (уникальные элементы)

Множество содержит только уникальные элементы:

```python
# ✅ Множество целых чисел
unique_ids: set[int] = {1, 2, 3, 4, 5}

# ✅ Множество строк
tags: set[str] = {"python", "typing", "code"}

# ❌ Неинформативно
data: set = {1, "two", 3.0}

# ✅ Пустое множество (важно!)
empty_set: set[int] = set()  # НЕ {} — это пустой словарь!
```

**Функции со множествами:**

```python
def find_unique(items: list[int]) -> set[int]:
    """Найти уникальные элементы"""
    return set(items)

def common_elements(set1: set[str], set2: set[str]) -> set[str]:
    """Найти общие элементы двух множеств"""
    return set1 & set2

def remove_duplicates(items: list[str]) -> list[str]:
    """Удалить дубликаты, сохраняя порядок"""
    seen: set[str] = set()
    result: list[str] = []
    for item in items:
        if item not in seen:
            seen.add(item)
            result.append(item)
    return result

# Использование
unique = find_unique([1, 2, 2, 3, 3, 3])
common = common_elements({"a", "b", "c"}, {"b", "c", "d"})
```

-----

## 🎁 Вложенные типы

Контейнеры могут содержать другие контейнеры:

```python
# ✅ Список словарей
users: list[dict[str, str]] = [
    {"name": "Alice", "email": "alice@example.com"},
    {"name": "Bob", "email": "bob@example.com"}
]

# ✅ Словарь со списками
groups: dict[str, list[str]] = {
    "admins": ["Alice", "Charlie"],
    "users": ["Bob", "David"]
}

# ✅ Список кортежей
points: list[tuple[int, int]] = [(0, 0), (1, 1), (2, 2)]

# ✅ Словарь со множествами
tags_by_post: dict[int, set[str]] = {
    1: {"python", "typing"},
    2: {"python", "decorators"},
    3: {"django"}
}

# Сложные вложенные типы
config: dict[str, list[tuple[str, int]]] = {
    "servers": [("localhost", 8000), ("api.example.com", 443)],
    "databases": [("postgres", 5432), ("redis", 6379)]
}
```

**Функции с вложенными типами:**

```python
def get_user_names(users: list[dict[str, str]]) -> list[str]:
    """Получить имена всех пользователей"""
    return [user["name"] for user in users]

def group_by_length(words: list[str]) -> dict[int, list[str]]:
    """Сгруппировать слова по длине"""
    result: dict[int, list[str]] = {}
    for word in words:
        length = len(word)
        if length not in result:
            result[length] = []
        result[length].append(word)
    return result

# Использование
users = [{"name": "Alice"}, {"name": "Bob"}]
names = get_user_names(users)

grouped = group_by_length(["a", "bb", "ccc", "dd"])
# {1: ["a"], 2: ["bb", "dd"], 3: ["ccc"]}
```

-----

## 💻 Примеры

### Пример 1: Типизация функции обработки данных

```python
def process_data(
    values: list[int],
    multiplier: int,
    config: dict[str, int | str]
) -> dict[str, list[int] | str]:
    """Обработать данные с конфигурацией"""
    processed = [v * multiplier for v in values]
    
    return {
        "results": processed,
        "count": len(processed),
        "status": "success"
    }

# Использование
config = {"threshold": 10, "name": "process"}
result = process_data([1, 2, 3], 5, config)
# {"results": [5, 10, 15], "count": 3, "status": "success"}
```

### Пример 2: Работа с типизированными контейнерами

```python
class DataAnalyzer:
    def __init__(self, data: list[dict[str, int]]):
        self.data = data
    
    def get_averages(self) -> dict[str, float]:
        """Средние значения для каждого ключа"""
        if not self.data:
            return {}
        
        totals: dict[str, int] = {}
        for record in self.data:
            for key, value in record.items():
                totals[key] = totals.get(key, 0) + value
        
        count = len(self.data)
        return {key: total / count for key, total in totals.items()}

# Использование
data = [
    {"apple": 10, "banana": 5},
    {"apple": 20, "banana": 15},
    {"apple": 30, "banana": 10}
]
analyzer = DataAnalyzer(data)
averages = analyzer.get_averages()
# {"apple": 20.0, "banana": 10.0}
```

-----

## ⚠️ Частые ошибки

```python
# ❌ Забыли тип элементов
def process(data: list) -> None:  # Неинформативно!
    pass

# ✅ Указали тип
def process(data: list[int]) -> None:
    pass

# ❌ Забыли второй тип для Dict
items: dict[str] = {}  # Error!

# ✅ Правильно
items: dict[str, int] = {}

# ❌ Пустой словарь как пустое множество
empty: set = {}  # Это словарь, не множество!

# ✅ Правильно
empty: set[int] = set()

# ❌ Tuple без типов
coordinates: tuple = (1, 2, 3)  # Неинформативно

# ✅ С типами
coordinates: tuple[int, int, int] = (1, 2, 3)

# ❌ Смешивание типов без Union
data: list[int] = [1, "two", 3]  # ❌ "two" не int!

# ✅ С Union
data: list[int | str] = [1, "two", 3]
```

-----

## ✅ Главные правила

✅ **Всегда указывайте тип элементов** — `list[int]`, не просто `list`  
✅ **Dict требует два типа** — ключ и значение  
✅ **Tuple может быть фиксированным или переменным** — `tuple[int, str]` или `tuple[int, ...]`  
✅ **Set для уникальных элементов** — `set[T]`  
✅ **Python 3.9+** — используйте встроенные типы, не typing.List  
✅ **Вложенные типы допустимы** — `list[dict[str, int]]`  
✅ **Пустой словарь `{}`** — это не пустое множество, используйте `set()`  
✅ **Union для смешанных типов** — `list[int | str]`

-----

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union, Optional и типы из typing]]
- [[05 — 🎨 TypeVar и Generic]]
- [[04 — ✅ Type Checking с mypy и pyright]]