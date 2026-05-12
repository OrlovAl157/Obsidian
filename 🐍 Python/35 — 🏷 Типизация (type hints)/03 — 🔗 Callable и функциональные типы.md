---
tags:
  - python
  - тема/типизация
  - тема/функции
  - статус/завершён
уровень: средний
---

# 🔗 Callable и функциональные типы

## 📌 Коротко

> **Callable** — тип для функций и любых вызываемых объектов. Указывает типы аргументов и возвращаемого значения.

---

## 💡 Полезные советы

- **Callable[[Arg1, Arg2], ReturnType]** — типы аргументов в списке, результат после
- **Callable[[], Type]** — функция без аргументов
- **Callable[..., Type]** — функция с любыми аргументами
- **Используйте для callback функций** — когда функция передаётся как параметр
- **Protocol лучше для сложных случаев** — более гибкий чем Callable

---

## 🚀 Быстрая справка

| Что | Синтаксис | Пример |
|---|---|---|
| **Функция int → str** | `Callable[[int], str]` | `lambda x: str(x)` |
| **Два аргумента** | `Callable[[int, int], int]` | `lambda x, y: x + y` |
| **Без аргументов** | `Callable[[], str]` | `lambda: "hello"` |
| **Любые аргументы** | `Callable[..., int]` | `lambda *args: sum(args)` |

---

## 📖 Теория

### Базовый Callable

```python
from typing import Callable

# Функция которая принимает функцию
def apply_operation(a: int, b: int, op: Callable[[int, int], int]) -> int:
    """Применить операцию к двум числам"""
    return op(a, b)

# Использование
result1 = apply_operation(5, 3, lambda x, y: x + y)      # 8
result2 = apply_operation(5, 3, lambda x, y: x * y)      # 15

def multiply(x: int, y: int) -> int:
    return x * y

result3 = apply_operation(5, 3, multiply)                 # 15
```

### Возврат функции

```python
from typing import Callable

# Функция которая возвращает функцию
def make_multiplier(n: int) -> Callable[[int], int]:
    """Создаёт функцию умножения на n"""
    def multiplier(x: int) -> int:
        return x * n
    return multiplier

# Использование
times_3 = make_multiplier(3)
result = times_3(10)  # 30
```

### Callback функции

```python
from typing import Callable

# Класс с callback
class Button:
    def __init__(self, label: str):
        self.label = label
        self.on_click: Callable[[], None] = lambda: None
    
    def set_onclick(self, callback: Callable[[], None]) -> None:
        """Установить callback на клик"""
        self.on_click = callback
    
    def click(self) -> None:
        """Эмулировать клик"""
        self.on_click()

# Использование
def on_button_click() -> None:
    print("Button clicked!")

button = Button("Click me")
button.set_onclick(on_button_click)
button.click()  # "Button clicked!"
```

### Без аргументов

```python
from typing import Callable

# Функция которая возвращает значение без аргументов
def execute_later(func: Callable[[], str]) -> str:
    """Выполнить функцию позже"""
    return func()

# Использование
result = execute_later(lambda: "Done!")  # "Done!"

def get_message() -> str:
    return "Hello!"

result = execute_later(get_message)  # "Hello!"
```

### С любыми аргументами

```python
from typing import Callable

# Функция с переменным числом аргументов
def call_with_args(func: Callable[..., str], *args) -> str:
    """Вызвать функцию с аргументами"""
    return func(*args)

# Использование
def format_numbers(*nums: int) -> str:
    return ", ".join(str(n) for n in nums)

result = call_with_args(format_numbers, 1, 2, 3)  # "1, 2, 3"
```

---

## 💻 Примеры

### Фильтрация с Callable

```python
from typing import Callable, List

def filter_items(
    items: List[int], 
    predicate: Callable[[int], bool]
) -> List[int]:
    """Фильтровать элементы по условию"""
    return [item for item in items if predicate(item)]

# Использование
numbers = [1, 2, 3, 4, 5, 6]

# Фильтр четных
evens = filter_items(numbers, lambda x: x % 2 == 0)
# [2, 4, 6]

# Фильтр больше 3
greater_than_3 = filter_items(numbers, lambda x: x > 3)
# [4, 5, 6]
```

### Map и Callable

```python
from typing import Callable, List, TypeVar

T = TypeVar('T')
U = TypeVar('U')

def map_items(items: List[T], transform: Callable[[T], U]) -> List[U]:
    """Применить трансформацию"""
    return [transform(item) for item in items]

# Использование
numbers = [1, 2, 3, 4, 5]

# Преобразование в строки
strings = map_items(numbers, str)
# ['1', '2', '3', '4', '5']

# Умножение на 2
doubled = map_items(numbers, lambda x: x * 2)
# [2, 4, 6, 8, 10]
```

---

## ⚠️ Частые ошибки

```python
from typing import Callable

# ❌ Забыли Return Type
def apply(f: Callable[[int, int]]) -> int:  # Error!
    pass

# ✅ Правильно
def apply(f: Callable[[int, int], int]) -> int:
    pass

# ❌ Передали функцию вместо результата
def process(callback: Callable[[], int]) -> int:
    return callback  # Error! Нужно вызвать

# ✅ Правильно
def process(callback: Callable[[], int]) -> int:
    return callback()  # Вызываем функцию

# ❌ Неправильный порядок в Callable
def execute(f: Callable[int, [int]]) -> int:  # Error!
    pass

# ✅ Правильно
def execute(f: Callable[[int], int]) -> int:
    pass
```

---

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union и Optional]]
- [[00 — Декоратор]]
- [[🌊 Lambda функции]]

## ❓ Вопросы / Непонятное

- ...
