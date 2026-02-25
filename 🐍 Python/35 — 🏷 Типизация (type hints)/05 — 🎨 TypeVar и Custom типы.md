---
tags:
  - python
  - тема/типизация
  - статус/в-разработке
---

# 🎨 TypeVar и Custom типы

## 📌 Коротко

> **TypeVar** — переменная для типов, позволяет писать generic функции. **Custom типы** — свои классы и типы для более специфичной типизации.

---

## 💡 Полезные советы

- **TypeVar для generics** — когда функция работает с любым типом
- **Bound ограничивает типы** — `TypeVar('T', bound=BaseClass)`
- **Constraints список разрешённых типов** — `TypeVar('T', int, str, float)`
- **Protocol лучше чем наследование** — для структурной типизации
- **Используйте @runtime_checkable** — для проверки типов в runtime

---

## 🚀 Быстрая справка

| Что | Синтаксис | Пример |
|---|---|---|
| **TypeVar** | `T = TypeVar('T')` | `def func(x: T) -> T:` |
| **С bound** | `T = TypeVar('T', bound=Type)` | `T = TypeVar('T', bound=int)` |
| **С constraints** | `T = TypeVar('T', Type1, Type2)` | `T = TypeVar('T', int, str)` |
| **Protocol** | `class Proto(Protocol):` | Структурная типизация |
| **TypeAlias** | `MyType = Union[int, str]` | Псевдоним для типа |

---

## 📖 Теория

### TypeVar базовый

```python
from typing import TypeVar

T = TypeVar('T')

def identity(x: T) -> T:
    """Возвращает аргумент без изменений"""
    return x

# Работает с любым типом
result1 = identity(42)          # int
result2 = identity("hello")     # str
result3 = identity([1, 2, 3])   # list
```

### TypeVar с bound

```python
from typing import TypeVar

# T может быть только числовым типом или его подклассом
T = TypeVar('T', bound=int)

def increment(x: T) -> T:
    """Увеличить на 1"""
    return x + 1  # OK, потому что T наследует от int

increment(5)        # ✅ OK
increment(5.5)      # ✅ OK (float наследует от числового)
increment("5")      # ❌ Error: str не числовой
```

### TypeVar с constraints

```python
from typing import TypeVar

# T может быть только int, str или float
T = TypeVar('T', int, str, float)

def process(value: T) -> T:
    if isinstance(value, str):
        return value.upper()  # type: ignore
    return value

process(42)         # ✅ int
process("hello")    # ✅ str
process([1, 2])     # ❌ Error: list не в constraints
```

### Protocol — структурная типизация

```python
from typing import Protocol

class Drawable(Protocol):
    """Всё что можно нарисовать"""
    def draw(self) -> str:
        ...

class Circle:
    def draw(self) -> str:
        return "●"

class Square:
    def draw(self) -> str:
        return "■"

def render(obj: Drawable) -> None:
    """Рендер любого рисуемого объекта"""
    print(obj.draw())

render(Circle())   # ✅ OK, есть метод draw
render(Square())   # ✅ OK, есть метод draw
```

### TypeAlias для сложных типов

```python
from typing import TypeAlias, Union, List, Dict

# Сложный тип
JsonValue: TypeAlias = Union[int, str, float, bool, None]
JsonDict: TypeAlias = Dict[str, JsonValue]

def parse_json(data: JsonDict) -> str:
    """Парсить JSON словарь"""
    return str(data)

# Более читаемо чем Union[...]
```

---

## 💻 Примеры

### Generic функция с TypeVar

```python
from typing import TypeVar, List

T = TypeVar('T')

def reverse_list(items: List[T]) -> List[T]:
    """Реверсить список сохраняя тип"""
    return items[::-1]

# Типы сохраняются
ints = reverse_list([1, 2, 3])      # List[int]
strs = reverse_list(["a", "b"])     # List[str]
```

### Generic класс

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []
    
    def push(self, item: T) -> None:
        self.items.append(item)
    
    def pop(self) -> T:
        return self.items.pop()

# Использование
int_stack: Stack[int] = Stack()
int_stack.push(1)
int_stack.push(2)
val = int_stack.pop()  # int
```

### Protocol практический

```python
from typing import Protocol, List

class Logger(Protocol):
    """Всё что может логировать"""
    def log(self, message: str) -> None:
        ...

class ConsoleLogger:
    def log(self, message: str) -> None:
        print(f"LOG: {message}")

class FileLogger:
    def log(self, message: str) -> None:
        with open("app.log", "a") as f:
            f.write(message + "\n")

def setup_logging(loggers: List[Logger]) -> None:
    """Установить логгеры"""
    for logger in loggers:
        logger.log("System initialized")

# Работает с обоими
setup_logging([ConsoleLogger(), FileLogger()])
```

---

## ⚠️ Частые ошибки

```python
from typing import TypeVar

T = TypeVar('T')

# ❌ Забыли что T может быть любым типом
def double(x: T) -> T:
    return x * 2  # Error! Не все типы поддерживают *

# ✅ Исправить с bound
T = TypeVar('T', bound=int)

def double(x: T) -> T:
    return x * 2  # OK

# ❌ Забыли Generic при наследовании
class Stack:
    def __init__(self) -> None:
        self.items: List[???] = []  # Какой тип?

# ✅ Правильно
from typing import Generic

T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: List[T] = []
```

---

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[03 — 🔗 Callable и функциональные типы]]
- [[02 — 📦 Generics и контейнеры]]

## ❓ Вопросы / Непонятное

- ...
