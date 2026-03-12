---
tags:
  - python
  - тема/модули
  - тема/functools
  - тема/функции
  - статус/завершён
уровень: средний
стандартная_библиотека: true
сторонний_пакет: false
---

# 🔧 Модуль functools - Полная памятка

## 💡 Что это?

**functools** — модуль Python для работы с функциями высшего порядка (функции, работающие с другими функциями).

```python
import functools
```

**Основные возможности:**

- 🎯 Частичное применение функций (`partial`)
- 💾 Кэширование результатов (`lru_cache`, `cache`)
- 🎨 Создание декораторов (`wraps`)
- 🔄 Функциональное программирование (`reduce`)
- 📊 Сравнение и упорядочивание (`total_ordering`)

---

## 🎯 1. partial - Частичное применение

### Что это?

**Частичное применение** — создание новой функции путём "замораживания" части аргументов.

### Синтаксис

```python
partial(func, *args, **kwargs)
```

### Базовый пример

```python
from functools import partial

def multiply(a, b):
    return a * b

# Фиксируем первый аргумент
double = partial(multiply, 2)
triple = partial(multiply, 3)

print(double(5))   # 10 (2 * 5)
print(triple(10))  # 30 (3 * 10)
```

### Именованные аргументы

```python
from functools import partial

# Системы счисления
binary = partial(int, base=2)
octal = partial(int, base=8)
hexadecimal = partial(int, base=16)

print(binary('1010'))      # 10
print(octal('17'))         # 15
print(hexadecimal('FF'))   # 255
```

### Практические примеры

**Логгеры:**

```python
def log_message(message, level='INFO'):
    print(f'[{level}] {message}')

log_error = partial(log_message, level='ERROR')
log_warning = partial(log_message, level='WARNING')

log_error('Ошибка!')     # [ERROR] Ошибка!
log_warning('Внимание!') # [WARNING] Внимание!
```

**GUI callbacks:**

```python
def on_click(button_id, event):
    print(f'Нажата кнопка {button_id}')

btn_ok = partial(on_click, 'OK')
btn_cancel = partial(on_click, 'Cancel')

btn_ok(None)      # Нажата кнопка OK
btn_cancel(None)  # Нажата кнопка Cancel
```

### Атрибуты partial-объекта

```python
double = partial(multiply, 2)

print(double.func)      # <function multiply>
print(double.args)      # (2,)
print(double.keywords)  # {}

# Вызов оригинальной функции
double.func(3, 4)  # 12
```

---

## 🎨 2. wraps - Сохранение метаданных

### Проблема

Декораторы теряют метаданные функции:

```python
def decorator(func):
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@decorator
def greet(name):
    """Приветствует пользователя"""
    return f'Hello {name}'

print(greet.__name__)  # 'wrapper' ❌
print(greet.__doc__)   # None ❌
```

### Решение: @wraps

```python
from functools import wraps

def decorator(func):
    @wraps(func)  # ← Сохраняет метаданные!
    def wrapper(*args, **kwargs):
        return func(*args, **kwargs)
    return wrapper

@decorator
def greet(name):
    """Приветствует пользователя"""
    return f'Hello {name}'

print(greet.__name__)  # 'greet' ✅
print(greet.__doc__)   # 'Приветствует пользователя' ✅
```

### Что копирует?

**WRAPPER_ASSIGNMENTS** (заменяет):

- `__module__` — модуль функции
- `__name__` — имя функции
- `__qualname__` — полное имя
- `__annotations__` — аннотации типов
- `__doc__` — документация

**WRAPPER_UPDATES** (обновляет):

- `__dict__` — словарь атрибутов

### Атрибут **wrapped**

```python
from functools import wraps

def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print("Декоратор работает")
        return func(*args, **kwargs)
    return wrapper

@decorator
def greet(name):
    return f'Hello {name}'

# Доступ к оригинальной функции
greet.__wrapped__('Alice')  # Hello Alice (БЕЗ "Декоратор работает")
```

---

## 💾 3. lru_cache - Кэширование (LRU)

### Что такое LRU?

**LRU (Least Recently Used)** — удаляет элементы, которые дольше всего не использовались.

### Синтаксис

```python
@lru_cache(maxsize=128, typed=False)
```

**Параметры:**

- `maxsize` — размер кэша (None = безлимит)
- `typed` — различать типы аргументов

### Базовый пример

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n - 1) + fibonacci(n - 2)

print(fibonacci(100))  # Мгновенно!
```

### Параметр maxsize

```python
@lru_cache(maxsize=128)  # Кэш на 128 элементов
@lru_cache(maxsize=1)    # Только последний
@lru_cache(maxsize=None) # Безлимитный
@lru_cache               # Python 3.8+: то же что None
```

### Параметр typed

```python
@lru_cache(typed=False)  # По умолчанию
def func(x):
    return x * 2

func(4)    # Вычисляем
func(4.0)  # Из кэша (4 == 4.0)

@lru_cache(typed=True)  # Различать типы
def func2(x):
    return x * 2

func2(4)    # Вычисляем
func2(4.0)  # Вычисляем снова (разные типы!)
```

### Методы cache_info() и cache_clear()

```python
@lru_cache(maxsize=128)
def factorial(n):
    return 1 if n <= 1 else n * factorial(n - 1)

factorial(5)
factorial(10)
factorial(5)  # Из кэша

info = factorial.cache_info()
print(info)
# CacheInfo(hits=1, misses=2, maxsize=128, currsize=2)

print(f"Hits: {info.hits}")        # 1 (из кэша)
print(f"Misses: {info.misses}")    # 2 (вычислено)
print(f"Currsize: {info.currsize}")# 2 (текущий размер)

factorial.cache_clear()  # Очистить кэш
```

### Сравнение скорости

```python
import time

# БЕЗ кэша - медленно (~3-5 сек)
def fib_slow(n):
    if n < 2:
        return n
    return fib_slow(n-1) + fib_slow(n-2)

# С кэшем - мгновенно (~0.0001 сек)
@lru_cache(maxsize=None)
def fib_fast(n):
    if n < 2:
        return n
    return fib_fast(n-1) + fib_fast(n-2)

start = time.time()
print(fib_slow(35))
print(f"Время: {time.time() - start:.2f}s")  # ~3-5s

start = time.time()
print(fib_fast(35))
print(f"Время: {time.time() - start:.4f}s")  # ~0.0001s
```

### ⚠️ Ограничения

```python
# ❌ НЕ РАБОТАЕТ - list не хешируемый
@lru_cache
def sum_list(numbers):
    return sum(numbers)

# ✅ РАБОТАЕТ - tuple хешируемый
@lru_cache
def sum_tuple(numbers):
    return sum(numbers)

sum_tuple((1, 2, 3))  # OK
```

**Можно:** int, str, tuple, frozenset  
**Нельзя:** list, dict, set

---

## 🚀 4. cache - Безлимитный кэш (Python 3.9+)

### Синтаксис

```python
from functools import cache

@cache  # = @lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

### Отличия от lru_cache

|Характеристика|@cache|@lru_cache|
|---|---|---|
|Синтаксис|Без скобок|Со скобками|
|Скорость|Быстрее|Чуть медленнее|
|Размер кэша|Безлимитный|Настраиваемый|
|cache_info()|✅ Есть|✅ Есть|

### Когда использовать?

- **@cache** — небольшой набор входных значений
- **@lru_cache(maxsize=128)** — большой диапазон значений

---

## 🔄 5. reduce - Свёртка последовательности

### Что делает?

Применяет функцию кумулятивно к элементам, сводя к одному значению.

### Синтаксис

```python
reduce(function, iterable[, initializer])
```

### Базовый пример

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]
total = reduce(lambda x, y: x + y, numbers)
print(total)  # 15

# Пошагово:
# 1. x=1, y=2 → 3
# 2. x=3, y=3 → 6
# 3. x=6, y=4 → 10
# 4. x=10, y=5 → 15
```

### Примеры

**Произведение:**

```python
numbers = [1, 2, 3, 4, 5]
product = reduce(lambda x, y: x * y, numbers)
print(product)  # 120
```

**С начальным значением:**

```python
numbers = [1, 2, 3]
result = reduce(lambda x, y: x + y, numbers, 10)
print(result)  # 16 (10 + 1 + 2 + 3)
```

**Объединение словарей:**

```python
dicts = [{'a': 1}, {'b': 2}, {'c': 3}]
merged = reduce(lambda x, y: {**x, **y}, dicts)
print(merged)  # {'a': 1, 'b': 2, 'c': 3}
```

**Плоский список:**

```python
nested = [[1, 2], [3, 4], [5, 6]]
flat = reduce(lambda x, y: x + y, nested)
print(flat)  # [1, 2, 3, 4, 5, 6]
```

### ⚠️ Когда НЕ использовать

```python
# ❌ Плохо - неочевидно
total = reduce(lambda x, y: x + y, numbers)

# ✅ Хорошо - очевидно
total = sum(numbers)

# ❌ Плохо
maximum = reduce(lambda x, y: x if x > y else y, numbers)

# ✅ Хорошо
maximum = max(numbers)
```

**Правило:** Используй `reduce` только если нет встроенной функции!

---

## 📊 6. total_ordering - Автогенерация сравнений

### Проблема

```python
class Person:
    def __eq__(self, other): ...
    def __lt__(self, other): ...
    def __le__(self, other): ...  # Повтор
    def __gt__(self, other): ...  # Повтор
    def __ge__(self, other): ...  # Повтор
```

### Решение

```python
from functools import total_ordering

@total_ordering
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __eq__(self, other):
        return self.age == other.age
    
    def __lt__(self, other):
        return self.age < other.age
    
    def __repr__(self):
        return f'Person({self.name}, {self.age})'

# Всё работает автоматически!
alice = Person('Alice', 25)
bob = Person('Bob', 30)

print(alice < bob)   # True
print(alice > bob)   # False (автоматически!)
print(alice <= bob)  # True (автоматически!)

people = [bob, alice]
people.sort()  # Сортировка работает!
```

**Требуется:** `__eq__` + один из (`__lt__`, `__le__`, `__gt__`, `__ge__`)  
**Создаётся:** все остальные методы

---

## 🏷️ 7. cached_property - Кэшируемое свойство (3.8+)

### Что делает?

Вычисляет свойство один раз и кэширует результат.

```python
from functools import cached_property

class Circle:
    def __init__(self, radius):
        self.radius = radius
    
    @cached_property
    def area(self):
        print("Вычисляем...")
        return 3.14159 * self.radius ** 2

circle = Circle(5)
print(circle.area)  # Вычисляем... 78.54
print(circle.area)  # 78.54 (из кэша!)
```

### vs @property

```python
@property  # Вычисляется КАЖДЫЙ раз
def area(self):
    return 3.14 * self.radius ** 2

@cached_property  # Вычисляется ОДИН раз
def area(self):
    return 3.14 * self.radius ** 2
```

### Очистка кэша

```python
calc = Calculator(5)
print(calc.result)  # Вычисляем

calc.value = 10
print(calc.result)  # Старое значение (кэш!)

del calc.result     # Очистить
print(calc.result)  # Пересчитать
```

---

## 🎭 8. singledispatch - Перегрузка (3.4+)

### Что это?

Разные версии функции для разных типов аргументов.

```python
from functools import singledispatch

@singledispatch
def process(data):
    raise NotImplementedError(f"Тип {type(data)} не поддерживается")

@process.register(int)
def _(data):
    return f"Число: {data}"

@process.register(str)
def _(data):
    return f"Строка: {data}"

@process.register(list)
def _(data):
    return f"Список из {len(data)} элементов"

print(process(42))        # Число: 42
print(process("hello"))   # Строка: hello
print(process([1,2,3]))   # Список из 3 элементов
```

### Множественная регистрация

```python
@process.register(list)
@process.register(tuple)
def _(data):
    return f"Последовательность: {len(data)} элементов"
```

---

## 🔧 9. update_wrapper - Копирование метаданных

### Для чего?

Копирует метаданные из одной функции в другую.

```python
from functools import partial, update_wrapper

def multiply(a, b):
    '''Перемножает два числа'''
    return a * b

double = partial(multiply, 2)

# Без update_wrapper
# print(double.__name__)  # AttributeError

# С update_wrapper
update_wrapper(double, multiply)
print(double.__name__)  # multiply
print(double.__doc__)   # Перемножает два числа
```

**@wraps использует update_wrapper() внутри!**

---

## 📝 Краткая справка

### Импорт

```python
from functools import (
    partial, wraps, lru_cache, cache,
    reduce, total_ordering, cached_property,
    singledispatch, update_wrapper
)
```

### Таблица функций

|Функция/Декоратор|Для чего|Python|
|---|---|---|
|`partial()`|Зафиксировать аргументы|2.5+|
|`@wraps`|Сохранить метаданные в декораторе|2.5+|
|`@lru_cache`|Кэширование с LRU|3.2+|
|`@cache`|Безлимитное кэширование|3.9+|
|`reduce()`|Свернуть последовательность|2.3+|
|`@total_ordering`|Автогенерация сравнений|2.7+|
|`@cached_property`|Кэшируемое свойство|3.8+|
|`@singledispatch`|Перегрузка по типу|3.4+|
|`update_wrapper()`|Копировать метаданные|2.5+|

### Когда использовать?

|Задача|Решение|
|---|---|
|GUI callbacks|`partial()`|
|Создание декоратора|`@wraps`|
|Рекурсия, дорогие вычисления|`@lru_cache`|
|Небольшой набор значений|`@cache`|
|Нет встроенной функции|`reduce()`|
|Классы с сравнением|`@total_ordering`|
|Дорогие свойства|`@cached_property`|
|Обработка разных типов|`@singledispatch`|

---

## 🎯 Практические примеры

### Пример 1: Конвейер обработки

```python
from functools import reduce

sentence = 'In the face of ambiguity refuse the temptation to guess'

# Фильтруем → Преобразовываем → Нумеруем
words = sentence.split()
long_words = filter(lambda w: len(w) > 4, words)
upper_words = map(str.upper, long_words)
numbered = enumerate(upper_words, 1)

for index, word in numbered:
    print(f'{index}. {word}')
# 1. AMBIGUITY
# 2. REFUSE
# 3. TEMPTATION
# 4. GUESS
```

### Пример 2: Декоратор с параметрами

```python
from functools import wraps

def repeat(times):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def greet(name):
    """Приветствует пользователя"""
    print(f'Hello, {name}!')

greet('Alice')
# Hello, Alice!
# Hello, Alice!
# Hello, Alice!

print(greet.__name__)  # greet ✅
```

### Пример 3: Кэширование HTTP запросов

```python
from functools import lru_cache
import requests

@lru_cache(maxsize=32)
def fetch_url(url):
    print(f"Загружаем {url}...")
    response = requests.get(url)
    return response.text

# Первый вызов - реальный запрос
data = fetch_url('https://api.github.com/users/python')
# Загружаем https://api.github.com/users/python...

# Второй вызов - из кэша (мгновенно!)
data = fetch_url('https://api.github.com/users/python')
# (ничего не выводит)
```

### Пример 4: Умная сортировка

```python
from functools import total_ordering

@total_ordering
class Version:
    def __init__(self, major, minor, patch):
        self.major = major
        self.minor = minor
        self.patch = patch
    
    def __eq__(self, other):
        return (self.major, self.minor, self.patch) == \
               (other.major, other.minor, other.patch)
    
    def __lt__(self, other):
        return (self.major, self.minor, self.patch) < \
               (other.major, other.minor, other.patch)
    
    def __repr__(self):
        return f'{self.major}.{self.minor}.{self.patch}'

versions = [Version(2, 0, 0), Version(1, 2, 3), Version(1, 0, 0)]
versions.sort()
print(versions)  # [1.0.0, 1.2.3, 2.0.0]
```

---

## 💡 Советы и хитрости

### 1. Комбинирование декораторов

```python
from functools import lru_cache, wraps

def debug(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        print(f'Вызов {func.__name__}({args}, {kwargs})')
        return func(*args, **kwargs)
    return wrapper

@debug
@lru_cache(maxsize=None)
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

fibonacci(5)
# Вызов fibonacci((5,), {})
# Вызов fibonacci((4,), {})
# ...
```

### 2. Очистка всех кэшей

```python
functions_with_cache = [func1, func2, func3]

for func in functions_with_cache:
    if hasattr(func, 'cache_clear'):
        func.cache_clear()
```

### 3. Статистика кэширования

```python
@lru_cache(maxsize=128)
def expensive_operation(n):
    return n ** 2

# После использования
info = expensive_operation.cache_info()
hit_rate = info.hits / (info.hits + info.misses) if info.hits + info.misses > 0 else 0
print(f'Hit rate: {hit_rate:.2%}')
```

---

## ⚠️ Частые ошибки

### 1. Кэширование с изменяемыми аргументами

```python
# ❌ ОШИБКА
@lru_cache
def process(data):  # data - список
    return sum(data)

# ✅ РЕШЕНИЕ
@lru_cache
def process(data):  # data - tuple
    return sum(data)

process((1, 2, 3))  # OK
```

### 2. Забыли @wraps

```python
# ❌ ПЛОХО
def decorator(func):
    def wrapper(*args):
        return func(*args)
    return wrapper

# ✅ ХОРОШО
def decorator(func):
    @wraps(func)
    def wrapper(*args):
        return func(*args)
    return wrapper
```

### 3. reduce вместо встроенных функций

```python
# ❌ ПЛОХО - неочевидно
from functools import reduce
total = reduce(lambda x, y: x + y, numbers)

# ✅ ХОРОШО - понятно
total = sum(numbers)
```

---

## 🎓 Что запомнить

✅ **partial** — GUI, callbacks, специализация функций  
✅ **@wraps** — ВСЕГДА в декораторах  
✅ **@lru_cache** — рекурсия, дорогие вычисления  
✅ **@cache** — простые случаи, малый набор значений  
✅ **reduce** — только если нет встроенной функции  
✅ **@total_ordering** — классы с сравнением  
✅ **@cached_property** — дорогие свойства классов  
✅ **@singledispatch** — обработка разных типов

## 🔗 Связанные темы

- [[06 — 🎯 reduce()]]
- [[00 — Декоратор]]
- [[01 — Как работают декораторы]]
- [[07 — 🔀 Перегрузка методов и @singledispatchmethod]]
- [[🔄 Рекурсия]]
