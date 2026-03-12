---
tags:
  - python
  - тема/типизация
  - статус/завершён
уровень: средний
---

# 📦 Generics и контейнеры

## 📌 Коротко

> Generics позволяют указать тип элементов внутри контейнеров. Например, `List[int]` — список целых чисел, `Dict[str, int]` — словарь где ключи строки а значения числа.

---

## 💡 Полезные советы

- **Всегда указывайте тип элементов** — `List[int]` вместо `list`
- **Dict требует 2 типа** — ключ и значение: `Dict[str, int]`
- **Используйте встроенные классы** — `list[int]` вместо `List[int]` (Python 3.9+)
- **Tuple может быть фиксированной длины** — `Tuple[int, str, float]`
- **Set для уникальных элементов** — `Set[int]`

---

## 🚀 Быстрая справка

| Тип | Синтаксис | Пример |
|---|---|---|
| **List** | `List[Type]` | `List[int]` |
| **Dict** | `Dict[KeyType, ValueType]` | `Dict[str, int]` |
| **Tuple фиксированная** | `Tuple[Type1, Type2, ...]` | `Tuple[int, str, float]` |
| **Tuple переменная** | `Tuple[Type, ...]` | `Tuple[int, ...]` |
| **Set** | `Set[Type]` | `Set[str]` |
| **Встроенные (3.9+)** | `list[Type]` | `list[int]` |

---

## 📖 Теория

### List — список элементов

```python
from typing import List

# Список целых чисел
numbers: List[int] = [1, 2, 3, 4, 5]

# Список строк
names: List[str] = ["Alice", "Bob", "Charlie"]

# Функция с типизированным списком
def sum_list(items: List[int]) -> int:
    return sum(items)

result = sum_list([1, 2, 3])  # ✅ OK
result = sum_list([1, "2", 3])  # ❌ Error
```

### Dict — словарь ключ-значение

```python
from typing import Dict

# Словарь: имя → возраст
ages: Dict[str, int] = {
    "Alice": 25,
    "Bob": 30,
    "Charlie": 35
}

# Функция с типизированным словарём
def get_age(people: Dict[str, int], name: str) -> int:
    return people.get(name, 0)

age = get_age(ages, "Alice")  # ✅ 25
```

### Tuple — кортеж

```python
from typing import Tuple

# Фиксированная длина и типы
point: Tuple[int, int] = (10, 20)
person: Tuple[str, int, bool] = ("Alice", 25, True)

# Переменная длина одного типа
numbers: Tuple[int, ...] = (1, 2, 3, 4, 5)

# Функция
def get_coordinates() -> Tuple[float, float]:
    return (3.14, 2.71)

x, y = get_coordinates()  # ✅ Распаковка
```

### Set — множество

```python
from typing import Set

# Множество уникальных элементов
unique_ids: Set[int] = {1, 2, 3, 4, 5}
tags: Set[str] = {"python", "typing", "code"}

# Функция
def get_unique(items: List[int]) -> Set[int]:
    return set(items)

result = get_unique([1, 2, 2, 3, 3, 3])  # {1, 2, 3}
```

### Python 3.9+ встроенные типы

```python
# Старый синтаксис (требует импорта typing)
from typing import List, Dict, Set, Tuple

old_list: List[int] = [1, 2, 3]
old_dict: Dict[str, int] = {"a": 1}

# Новый синтаксис (Python 3.9+, встроенные классы)
new_list: list[int] = [1, 2, 3]
new_dict: dict[str, int] = {"a": 1}
new_set: set[str] = {"a", "b"}
new_tuple: tuple[int, ...] = (1, 2, 3)
```

---

## 💻 Примеры

### Функции с контейнерами

```python
from typing import List, Dict, Set

def process_numbers(nums: List[int]) -> Dict[str, int]:
    """Возвращает статистику по числам"""
    return {
        "sum": sum(nums),
        "count": len(nums),
        "max": max(nums) if nums else 0
    }

# Использование
result = process_numbers([1, 2, 3, 4, 5])
# {"sum": 15, "count": 5, "max": 5}

def find_duplicates(items: List[int]) -> Set[int]:
    """Находит дубликаты"""
    seen = set()
    duplicates = set()
    for item in items:
        if item in seen:
            duplicates.add(item)
        seen.add(item)
    return duplicates

dups = find_duplicates([1, 2, 2, 3, 3, 3])  # {2, 3}
```

### Вложенные типы

```python
from typing import List, Dict

# Список словарей
users: List[Dict[str, str]] = [
    {"name": "Alice", "email": "alice@example.com"},
    {"name": "Bob", "email": "bob@example.com"}
]

# Словарь со списками
groups: Dict[str, List[str]] = {
    "admin": ["Alice", "Charlie"],
    "users": ["Bob", "David"]
}

# Функция
def get_user_names(users: List[Dict[str, str]]) -> List[str]:
    return [user["name"] for user in users]

names = get_user_names(users)  # ["Alice", "Bob"]
```

---

## ⚠️ Частые ошибки

```python
from typing import List, Dict

# ❌ Без типа элементов
def process(data: list) -> dict:
    pass

# ✅ С типами
def process(data: List[int]) -> Dict[str, int]:
    pass

# ❌ Забыли второй тип для Dict
data: Dict[str] = {}  # Error!

# ✅ Правильно
data: Dict[str, int] = {}

# ❌ Tuple без типов
coords: Tuple = (1, 2, 3)  # Слишком общо

# ✅ Правильно
coords: Tuple[int, int, int] = (1, 2, 3)
```

---

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union и Optional]]
- [[05 — 🎨 TypeVar и Custom типы]]
- [[01 — Итераторы (Iterator)]]

## ❓ Вопросы / Непонятное

- ...
