-----

## tags: [python, типизация, callable, функции, callback]
difficulty: intermediate

# 🔗 Callable и функциональные типы

> **Callable** — тип для аннотирования функций и любых вызываемых объектов. Указывает типы параметров функции и её возвращаемого значения. Используется когда функция передаётся как параметр или возвращается из другой функции. Callable — это для функций то же, что `list[int]` для списков: указывает, что хранится внутри.

## Содержание

- [[#Справка|Справка]]
- [[#🔧 Базовый Callable|Базовый Callable]]
- [[#🎁 Функция возвращает функцию|Функция возвращает функцию]]
- [[#🔇 Callable без аргументов|Callable без аргументов]]
- [[#🌀 Callable с любыми аргументами|Callable с любыми аргументами]]
- [[#📞 Callback функции|Callback функции]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Сигнатура           |Синтаксис                    |Пример              |Значение                           |
|--------------------|-----------------------------|--------------------|-----------------------------------|
|**int → str**       |`Callable[[int], str]`       |`lambda x: str(x)`  |Принимает int, возвращает str      |
|**int, int → int**  |`Callable[[int, int], int]`  |`lambda x, y: x + y`|Два int → int                      |
|**Без аргументов**  |`Callable[[], str]`          |`lambda: "hello"`   |Ничего не принимает, возвращает str|
|**Любые аргументы** |`Callable[..., int]`         |`lambda *a, **k: 42`|Любые аргументы → int              |
|**str, bool → None**|`Callable[[str, bool], None]`|`print`             |Строка и булево → ничего           |

-----

## 🔧 Базовый Callable

Когда функция принимает другую функцию как параметр:

```python
from typing import Callable

def apply_operation(a: int, b: int, op: Callable[[int, int], int]) -> int:
    """Применить операцию к двум числам"""
    return op(a, b)

# Использование с lambda
result1 = apply_operation(5, 3, lambda x, y: x + y)      # 8
result2 = apply_operation(5, 3, lambda x, y: x * y)      # 15

# Или с обычной функцией
def multiply(x: int, y: int) -> int:
    return x * y

result3 = apply_operation(5, 3, multiply)                 # 15
```

**Синтаксис Callable:**

- `Callable[[int, int], int]` — принимает 2 int’а, возвращает int
- Типы параметров в **квадратных скобках**, возвращаемый тип **после запятой**

-----

## 🎁 Функция возвращает функцию

Когда функция создаёт и возвращает другую функцию (фабрика функций):

```python
from typing import Callable

def make_multiplier(n: int) -> Callable[[int], int]:
    """Создаёт функцию умножения на n"""
    def multiplier(x: int) -> int:
        return x * n
    return multiplier

# Использование
times_3 = make_multiplier(3)
times_5 = make_multiplier(5)

print(times_3(10))  # 30
print(times_5(10))  # 50
```

-----

## 🔇 Callable без аргументов

Функция, которая ничего не принимает:

```python
from typing import Callable

def execute_later(func: Callable[[], str]) -> str:
    """Выполнить функцию и вернуть результат"""
    return func()

# Использование
def get_message() -> str:
    return "Hello, World!"

result = execute_later(get_message)  # "Hello, World!"
result = execute_later(lambda: "Lambda result")  # "Lambda result"
```

-----

## 🌀 Callable с любыми аргументами

Когда точно не знаешь, сколько аргументов и каких типов:

```python
from typing import Callable, Any

def call_with_logging(func: Callable[..., Any], *args, **kwargs) -> Any:
    """Вызвать функцию и залогировать"""
    print(f"Calling {func.__name__} with args={args}, kwargs={kwargs}")
    result = func(*args, **kwargs)
    print(f"Result: {result}")
    return result

# Использование
def add(a: int, b: int) -> int:
    return a + b

def greet(name: str, greeting: str = "Hello") -> str:
    return f"{greeting}, {name}!"

call_with_logging(add, 5, 3)
call_with_logging(greet, "Alice", greeting="Hi")
```

-----

## 📞 Callback функции

Очень частый паттерн — передавать callback, который вызывается когда происходит что-то:

```python
from typing import Callable

class Button:
    def __init__(self, label: str):
        self.label = label
        self.on_click: Callable[[], None] = lambda: None
    
    def set_onclick(self, callback: Callable[[], None]) -> None:
        """Установить функцию, которая вызывается при клике"""
        self.on_click = callback
    
    def click(self) -> None:
        """Эмулировать клик"""
        print(f"Button '{self.label}' clicked!")
        self.on_click()

# Использование
def on_button_click() -> None:
    print("User clicked the button!")

button = Button("Click me")
button.set_onclick(on_button_click)
button.click()
# Output:
# Button 'Click me' clicked!
# User clicked the button!
```

-----

## 💻 Примеры

### Пример 1: Фильтрация с Callable

```python
from typing import Callable

def filter_numbers(
    numbers: list[int],
    predicate: Callable[[int], bool]
) -> list[int]:
    """Фильтровать числа по условию"""
    return [num for num in numbers if predicate(num)]

# Использование
all_numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Фильтр чётных
evens = filter_numbers(all_numbers, lambda x: x % 2 == 0)
print(evens)  # [2, 4, 6, 8, 10]

# Фильтр больше 5
greater_than_5 = filter_numbers(all_numbers, lambda x: x > 5)
print(greater_than_5)  # [6, 7, 8, 9, 10]

# Фильтр с функцией
def is_even(n: int) -> bool:
    return n % 2 == 0

evens2 = filter_numbers(all_numbers, is_even)
```

### Пример 2: Map с трансформацией

```python
from typing import Callable, TypeVar

T = TypeVar('T')
U = TypeVar('U')

def transform(
    items: list[T],
    transformer: Callable[[T], U]
) -> list[U]:
    """Применить трансформацию к каждому элементу"""
    return [transformer(item) for item in items]

# Использование
numbers = [1, 2, 3, 4, 5]

# Преобразовать в строки
strings = transform(numbers, str)
print(strings)  # ['1', '2', '3', '4', '5']

# Возвести в квадрат
squares = transform(numbers, lambda x: x ** 2)
print(squares)  # [1, 4, 9, 16, 25]

# Преобразовать в булевы (чётные ли)
are_even = transform(numbers, lambda x: x % 2 == 0)
print(are_even)  # [False, True, False, True, False]
```

### Пример 3: Декоратор с Callable

```python
from typing import Callable, TypeVar
import functools

T = TypeVar('T')

def log_calls(func: Callable[..., T]) -> Callable[..., T]:
    """Декоратор, логирующий вызовы функции"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> T:
        print(f"Calling {func.__name__}({args}, {kwargs})")
        result = func(*args, **kwargs)
        print(f"Result: {result}")
        return result
    return wrapper  # type: ignore

# Использование
@log_calls
def add(a: int, b: int) -> int:
    return a + b

@log_calls
def greet(name: str) -> str:
    return f"Hello, {name}!"

add(5, 3)
# Calling add((5, 3), {})
# Result: 8

greet("Alice")
# Calling greet(('Alice',), {})
# Result: Hello, Alice!
```

### Пример 4: Сортировка с Callable

```python
from typing import Callable

class Person:
    def __init__(self, name: str, age: int):
        self.name = name
        self.age = age
    
    def __repr__(self) -> str:
        return f"Person({self.name}, {self.age})"

def sort_people(
    people: list[Person],
    key: Callable[[Person], int] | Callable[[Person], str]
) -> list[Person]:
    """Сортировать людей по функции"""
    return sorted(people, key=key)  # type: ignore

# Использование
people = [
    Person("Alice", 30),
    Person("Bob", 25),
    Person("Charlie", 35)
]

# Сортировка по имени
by_name = sort_people(people, lambda p: p.name)
print(by_name)

# Сортировка по возрасту
by_age = sort_people(people, lambda p: p.age)
print(by_age)
```

-----

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
    return callback  # ❌ Должно быть callback()

# ✅ Правильно
def process(callback: Callable[[], int]) -> int:
    return callback()

# ❌ Неправильный порядок (параметры в круглых скобках!)
def execute(f: Callable(int, int) -> int) -> int:  # SyntaxError!
    pass

# ✅ Правильно (квадратные скобки!)
def execute(f: Callable[[int, int], int]) -> int:
    pass

# ❌ Путаница с типами параметров
def transform(data: list[int], func: Callable[[str], int]) -> None:
    # ❌ func ожидает str, но мы передаём int!
    for item in data:
        func(item)

# ✅ Правильно
def transform(data: list[int], func: Callable[[int], int]) -> None:
    for item in data:
        func(item)
```

-----

## ✅ Главные правила

✅ **Callable[[Args], ReturnType]** — порядок: аргументы потом возврат  
✅ **Callable[[], Type]** — функция без аргументов  
✅ **Callable[…, Type]** — любые аргументы (используй редко!)  
✅ **Всегда указывайте Return Type** — это обязательно!  
✅ **Используйте для callback’ов** — когда функция передаётся параметром  
✅ **Квадратные скобки для аргументов** — `Callable[[int], str]`, не круглые!

-----

## 🔗 Связанные темы

- [[00 — 📖 Основы типизации]]
- [[01 — 🔄 Union, Optional и типы из typing]]
- [[05 — 🎨 TypeVar и обобщённые типы]]