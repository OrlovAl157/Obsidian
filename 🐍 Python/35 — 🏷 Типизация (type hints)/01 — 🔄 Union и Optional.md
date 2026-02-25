---
tags:
  - python
  - тема/типизация
  - статус/в-разработке
---

# 🔄 Union и Optional

## 📌 Коротко

> **Union** — когда значение может быть одним из нескольких типов. **Optional** — это Union с None, т.е. значение или отсутствие значения.

---

## 💡 Полезные советы

- **Optional[T]** = **Union[T, None]** — это одно и то же
- **Используйте Optional чаще** — он более читаемый чем Union с None
- **Union для альтернатив** — `Union[int, str]` когда может быть несколько типов
- **Python 3.10+** — можно использовать `T | None` вместо Optional
- **Проверяйте тип перед использованием** — защита от None ошибок

---

## 🚀 Быстрая справка

| Что | Синтаксис | Пример |
|---|---|---|
| **Union** | `Union[Type1, Type2]` | `Union[int, str]` |
| **Optional** | `Optional[Type]` | `Optional[str]` |
| **Union с None** | `Union[Type, None]` | `Union[int, None]` |
| **Python 3.10+** | `Type1 \| Type2` | `int \| str` |
| **Python 3.10+ с None** | `Type \| None` | `str \| None` |

---

## 📖 Теория

### Union — несколько типов

```python
from typing import Union

# Функция может принять int или str
def process(value: Union[int, str]) -> str:
    if isinstance(value, int):
        return f"Number: {value}"
    else:
        return f"Text: {value}"

# Использование
result1 = process(42)        # ✅ OK
result2 = process("hello")   # ✅ OK
result3 = process(3.14)      # ❌ Error: float not allowed
```

### Optional — значение или None

```python
from typing import Optional

# Может вернуть строку или None
def get_greeting(name: Optional[str]) -> str:
    if name is None:
        return "Hello, stranger!"
    return f"Hello, {name}!"

# Использование
msg1 = get_greeting("Alice")  # "Hello, Alice!"
msg2 = get_greeting(None)     # "Hello, stranger!"
```

### Union и None эквивалентны

```python
from typing import Union, Optional

# Эти два эквивалентны:
type1: Union[str, None]
type2: Optional[str]

# И это одно и то же
def func1(x: Union[int, str, None]) -> str:
    pass

def func2(x: Union[int, str] | None) -> str:
    pass
```

### Python 3.10+ синтаксис

```python
# Старый синтаксис (Python 3.9 и ниже)
from typing import Union, Optional

def old_style(x: Union[int, str]) -> Optional[str]:
    pass

# Новый синтаксис (Python 3.10+)
def new_style(x: int | str) -> str | None:
    pass
```

---

## 💻 Примеры

### Обработка Union

```python
from typing import Union

def describe(value: Union[int, str, float]) -> str:
    if isinstance(value, int):
        return f"Integer: {value}"
    elif isinstance(value, str):
        return f"String: {value}"
    else:
        return f"Float: {value}"

# Правильное использование
print(describe(42))          # "Integer: 42"
print(describe("hello"))     # "String: hello"
print(describe(3.14))        # "Float: 3.14"
```

### Функции с Optional

```python
from typing import Optional

def find_max(values: list) -> Optional[int]:
    """Возвращает макс или None если пусто"""
    if not values:
        return None
    return max(values)

# Использование
max1 = find_max([1, 2, 3])   # 3
max2 = find_max([])          # None
```

### Функция возвращает Union

```python
from typing import Union

def parse_number(text: str) -> Union[int, float, None]:
    """Парсит число или возвращает None"""
    try:
        if '.' in text:
            return float(text)
        else:
            return int(text)
    except ValueError:
        return None

# Использование
val1 = parse_number("42")     # 42 (int)
val2 = parse_number("3.14")   # 3.14 (float)
val3 = parse_number("abc")    # None
```

---

## ⚠️ Частые ошибки

```python
from typing import Optional

# ❌ Забыли про None проверку
def greet(name: Optional[str]) -> str:
    return f"Hello, {name}!"  # Может быть None!

# ✅ Правильно
def greet(name: Optional[str]) -> str:
    if name is None:
        return "Hello, stranger!"
    return f"Hello, {name}!"

# ❌ Union без нужды
def add(x: Union[int, float], y: Union[int, float]) -> Union[int, float]:
    return x + y
# Число + число всегда = число

# ✅ Проще
def add(x: int | float, y: int | float) -> int | float:
    return x + y
```

---

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[Generics и контейнеры]]
- [[Type checking с mypy]]

## ❓ Вопросы / Непонятное

- ...
