-----

## tags: [python, типизация, typevar, generics, generic]
difficulty: intermediate

# 🎨 TypeVar и обобщённые типы

> **TypeVar** — переменная для типов, которая позволяет писать **generic функции**. Функция работает с любым типом, но все места, отмеченные одной переменной TypeVar, ожидают один и тот же тип. Например, функция `identity(x: T) -> T:` принимает любой тип и возвращает тот же тип. TypeVar решает проблему: как написать функцию, которая работает с любыми типами, но сохраняет информацию о типе?

## Содержание

- [[#Справка|Справка]]
- [[#⚡ Проблема Any теряет информацию|Проблема: Any теряет информацию]]
- [[#💡 Решение TypeVar|Решение: TypeVar]]
- [[#🎯 TypeVar с bound|TypeVar с bound]]
- [[#📋 TypeVar с constraints|TypeVar с constraints]]
- [[#🔀 Несколько TypeVar|Несколько TypeVar]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Что                |Синтаксис                          |Пример                    |Когда                 |
|-------------------|-----------------------------------|--------------------------|----------------------|
|**TypeVar базовый**|`T = TypeVar('T')`                 |`def identity(x: T) -> T:`|Любой тип             |
|**С bound**        |`T = TypeVar('T', bound=int)`      |Числовые типы             |Ограничивает типы     |
|**С constraints**  |`T = TypeVar('T', int, str)`       |Только эти типы           |Конкретные типы       |
|**Несколько**      |`T, U = TypeVar('T'), TypeVar('U')`|Разные типы               |Разные переменные типа|

-----

## ⚡ Проблема: Any теряет информацию

```python
from typing import Any

def identity(x: Any) -> Any:
    """Может быть что угодно"""
    return x

result = identity(42)  # Что вернулось? int? Any?
```

mypy не знает, что вернулось. Ошибка типов не будет видна:

```python
result = identity(42)
print(result.upper())  # ❌ mypy не ловит! Может быть Any...
```

-----

## 💡 Решение: TypeVar

```python
from typing import TypeVar

T = TypeVar('T')

def identity(x: T) -> T:
    """Возвращает то же, что получил"""
    return x

result1 = identity(42)      # int
result2 = identity("hello") # str
```

Теперь mypy **знает типы!**

```python
result = identity(42)
print(result.upper())  # ❌ Error: int has no method upper()
```

-----

## 🎯 TypeVar с bound (ограничения)

Когда функция нужна только для определённых типов:

```python
from typing import TypeVar

# T может быть только int, float или их подклассы
T = TypeVar('T', bound=int)

def double(x: T) -> T:
    return x * 2

result1 = double(5)    # ✅ int
result2 = double(5.5)  # ✅ float (наследует от числовых)
result3 = double("5")  # ❌ Error: str не числовой!
```

-----

## 📋 TypeVar с constraints (список типов)

Когда функция работает только с конкретными типами:

```python
from typing import TypeVar

# T может быть ТОЛЬКО int, str или float
T = TypeVar('T', int, str, float)

def process(value: T) -> T:
    if isinstance(value, str):
        return value.upper()  # type: ignore
    return value

result1 = process(42)      # ✅ int
result2 = process("hello") # ✅ str
result3 = process([1, 2])  # ❌ Error: list не в constraints!
```

**Важно:** при constraints все аргументы с типом T должны быть **одного типа**!

```python
T = TypeVar('T', int, str)

def to_tuple(a: T, b: T) -> tuple[T, T]:
    return a, b

to_tuple(1, 2)          # ✅ Оба int
to_tuple("a", "b")      # ✅ Оба str
to_tuple(1, "a")        # ❌ Error: разные типы!
```

-----

## 🔀 Несколько TypeVar в одной функции

Когда функция работает с несколькими независимыми типами:

```python
from typing import TypeVar

T = TypeVar('T')
U = TypeVar('U')

def zip_types(a: T, b: U) -> tuple[T, U]:
    """Создаёт кортеж из двух разных типов"""
    return a, b

result = zip_types(42, "hello")  # tuple[int, str]
```

Или когда у функции несколько параметров одного типа:

```python
from typing import TypeVar

T = TypeVar('T')

def combine(items: list[T]) -> T | None:
    """Возвращает первый элемент или None"""
    return items[0] if items else None

result1 = combine([1, 2, 3])        # int
result2 = combine(["a", "b"])       # str
```

-----

## 💻 Примеры

### Пример 1: Реверс списка сохраняет тип

```python
from typing import TypeVar

T = TypeVar('T')

def reverse_list(items: list[T]) -> list[T]:
    """Реверсить список, сохраняя тип элементов"""
    return items[::-1]

# Типы сохраняются!
ints = reverse_list([1, 2, 3])        # list[int]
strs = reverse_list(["a", "b", "c"])  # list[str]
```

### Пример 2: Работа с контейнерами

```python
from typing import TypeVar

T = TypeVar('T')

def first_element(seq: list[T]) -> T:
    """Получить первый элемент"""
    if not seq:
        raise ValueError("List is empty")
    return seq[0]

def last_element(seq: list[T]) -> T:
    """Получить последний элемент"""
    if not seq:
        raise ValueError("List is empty")
    return seq[-1]

# Типы выводятся правильно
first = first_element([1, 2, 3])      # int
last = last_element(["a", "b", "c"])  # str
```

### Пример 3: Функция с bound

```python
from typing import TypeVar

# T может быть только числовым (int, float, bool и их подклассы)
NumT = TypeVar('NumT', bound=int)

def triple(x: NumT) -> NumT:
    """Умножить на 3"""
    return x * 3  # type: ignore

result1 = triple(5)    # ✅ int
result2 = triple(5.5)  # ✅ float
# result3 = triple("5")  # ❌ Error!
```

### Пример 4: Функция фильтрации

```python
from typing import TypeVar, Callable

T = TypeVar('T')

def filter_items(
    items: list[T],
    predicate: Callable[[T], bool]
) -> list[T]:
    """Фильтровать элементы по условию"""
    return [item for item in items if predicate(item)]

# Типы работают корректно
numbers = [1, 2, 3, 4, 5]
evens = filter_items(numbers, lambda x: x % 2 == 0)
# evens: list[int]

names = ["Alice", "Bob", "Charlie"]
long_names = filter_items(names, lambda x: len(x) > 3)
# long_names: list[str]
```

### Пример 5: Функция трансформации

```python
from typing import TypeVar, Callable

T = TypeVar('T')
U = TypeVar('U')

def transform(
    items: list[T],
    transformer: Callable[[T], U]
) -> list[U]:
    """Преобразовать список из одного типа в другой"""
    return [transformer(item) for item in items]

# Типы выводятся правильно
numbers = [1, 2, 3]
strings = transform(numbers, str)
# strings: list[str]

squares = transform(numbers, lambda x: x ** 2)
# squares: list[int]

words = ["hello", "world"]
lengths = transform(words, len)
# lengths: list[int]

upper = transform(words, str.upper)
# upper: list[str]
```

-----

## ⚠️ Частые ошибки

```python
from typing import TypeVar

# ❌ TypeVar с одним constraint (не имеет смысла)
T = TypeVar('T', str)  # Error: need at least 2 constraints

# ✅ Используй bound вместо этого
T = TypeVar('T', bound=str)

# ❌ Забыли типизировать функцию, использующую T
T = TypeVar('T')

def mystery(x: T) -> T:  # ❌ T где-то не указан?
    result: list = [x]
    return result[0]  # ❌ Type mismatch!

# ✅ Правильно
def mystery(x: T) -> T:
    result: list[T] = [x]
    return result[0]

# ❌ Использование Any вместо TypeVar
from typing import Any

def identity(x: Any) -> Any:
    return x

# ✅ Используй TypeVar
T = TypeVar('T')

def identity(x: T) -> T:
    return x

# ❌ Думать, что constraints = множество типов (как Union)
T = TypeVar('T', int, str)
value: T  # Это не Union[int, str]!

# ✅ Это одна переменная, которая ОДНА из этих типов
T = TypeVar('T', int, str)

def process(a: T, b: T) -> T:
    # a и b ДО ЛЖ НЫ быть ОДНОГО типа!
    return a if a else b
```

-----

## ✅ Главные правила

✅ **TypeVar для generic функций** — когда работаешь с любыми типами  
✅ **T запоминает тип аргумента** — везде используется один и тот же  
✅ **bound для ограничений** — только определённые типы  
✅ **constraints для конкретных типов** — только эти, не другие  
✅ **Несколько TypeVar OK** — T, U, V в одной функции  
✅ **TypeVar лучше Any** — сохраняет информацию о типе  
✅ **Функции с TypeVar универсальны** — работают со всеми подходящими типами

-----

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[02 — 📦 Generics и контейнеры]]
- [[03 — 🔗 Callable и функциональные типы]]