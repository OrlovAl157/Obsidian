---
tags:
  - python
  - тема/функции
  - тема/встроенные_функции
  - тема/functools
  - статус/завершён
уровень: средний
возвращает_итератор: false
ленивые_вычисления: false
принимает_key: false
встроенная: false
стандартная_библиотека: true
---

## Пример для быстрой справки

```python
from functools import reduce

# Суммирование списка чисел
numbers = [1, 2, 3, 4, 5]
result = reduce(lambda x, y: x + y, numbers)

print(result)  # 15
# Процесс: ((((1+2)+3)+4)+5) = 15
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from functools import reduce`|Подключение функции|
|**Базовое использование**|`reduce(func, iterable)`|Свертка без начального значения|
|**С начальным значением**|`reduce(func, iterable, initial)`|Свертка с инициализатором|
|**Сумма**|`reduce(lambda x, y: x + y, nums)`|Сложение всех элементов|
|**Произведение**|`reduce(lambda x, y: x * y, nums)`|Умножение всех элементов|
|**Минимум**|`reduce(lambda x, y: x if x < y else y, nums)`|Поиск минимального значения|
|**Максимум**|`reduce(lambda x, y: x if x > y else y, nums)`|Поиск максимального значения|
|**С operator**|`reduce(operator.add, nums)`|Использование готовых операций|
|**Конкатенация**|`reduce(lambda x, y: x + y, strings)`|Объединение строк|

## Что такое reduce?

**reduce()** — это функция из модуля `functools`, которая применяет двухаргументную функцию последовательно ко всем элементам итерируемого объекта слева направо, сводя его к одному значению.

### Синтаксис

```python
from functools import reduce

reduce(function, iterable, initializer=None)
```

**⚙️ Параметры:**

- `function` — функция, принимающая 2 аргумента
- `iterable` — итерируемый объект (список, кортеж, строка и т.д.)
- `initializer` — начальное значение (необязательно)

**Возвращает:** Одно агрегированное значение

### Как работает reduce?

reduce применяет функцию к первым двум элементам, затем использует результат со следующим элементом и так далее:

```python
from functools import reduce

numbers = [1, 2, 3, 4]
result = reduce(lambda x, y: x + y, numbers)

# Пошаговое выполнение:
# Шаг 1: 1 + 2 = 3
# Шаг 2: 3 + 3 = 6
# Шаг 3: 6 + 4 = 10
# Результат: 10
```

### Упрощенная реализация

Вот как примерно работает reduce изнутри:

```python
def reduce(function, iterable, initial=None):
    """Упрощенная версия functools.reduce"""
    it = iter(iterable)
    
    if initial is None:
        value = next(it)  # Берем первый элемент
    else:
        value = initial
    
    for element in it:
        value = function(value, element)
    
    return value
```

## Базовые примеры

### Пример 1: Сумма чисел

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# С lambda
result = reduce(lambda x, y: x + y, numbers)
print(result)  # 15

# С обычной функцией
def add(x, y):
    return x + y

result = reduce(add, numbers)
print(result)  # 15

# ✅ Лучше использовать встроенную sum()
result = sum(numbers)
print(result)  # 15
```

### Пример 2: Произведение чисел (факториал)

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# С lambda
result = reduce(lambda x, y: x * y, numbers)
print(result)  # 120

# ✅ Лучше использовать math.prod() (Python 3.8+)
from math import prod
result = prod(numbers)
print(result)  # 120
```

### Пример 3: Поиск максимума

```python
from functools import reduce

numbers = [3, 7, 2, 9, 1, 5]

result = reduce(lambda x, y: x if x > y else y, numbers)
print(result)  # 9

# ✅ Лучше использовать встроенную max()
result = max(numbers)
print(result)  # 9
```

### Пример 4: Конкатенация строк

```python
from functools import reduce

words = ["Python", "is", "awesome"]

result = reduce(lambda x, y: x + " " + y, words)
print(result)  # "Python is awesome"

# ✅ Лучше использовать join()
result = " ".join(words)
print(result)  # "Python is awesome"
```

## Использование initializer (начального значения)

Третий аргумент initializer является необязательным. Если он указан, reduce использует его как первое значение:

```python
from functools import reduce

numbers = [1, 2, 3, 4]

# Без initializer
result = reduce(lambda x, y: x + y, numbers)
print(result)  # 10 (1+2+3+4)

# С initializer = 100
result = reduce(lambda x, y: x + y, numbers, 100)
print(result)  # 110 (100+1+2+3+4)
```

### Пошаговое выполнение с initializer

```python
from functools import reduce

def add_verbose(x, y):
    result = x + y
    print(f"{x} + {y} = {result}")
    return result

numbers = [1, 2, 3, 4]
result = reduce(add_verbose, numbers, 100)

# Вывод:
# 100 + 1 = 101
# 101 + 2 = 103
# 103 + 3 = 106
# 106 + 4 = 110
```

### Зачем нужен initializer?

Если итерируемый объект пустой, reduce вернет значение initializer. Без initializer будет выброшена ошибка TypeError:

```python
from functools import reduce

# ❌ Ошибка с пустым списком
try:
    result = reduce(lambda x, y: x + y, [])
except TypeError as e:
    print(e)  # reduce() of empty sequence with no initial value

# ✅ С initializer работает
result = reduce(lambda x, y: x + y, [], 0)
print(result)  # 0
```

**Правило:** Всегда указывайте `initializer`, если список может быть пустым.

### Выбор правильного initializer

Initializer должен быть **нейтральным элементом** (identity element) для операции:

|Операция|Правильный initializer|Почему|
|---|---|---|
|Сложение|`0`|`0 + x = x`|
|Умножение|`1`|`1 * x = x`|
|Конкатенация строк|`''`|`'' + s = s`|
|Объединение списков|`[]`|`[] + lst = lst`|
|Логическое И (AND)|`True`|`True and x = x`|
|Логическое ИЛИ (OR)|`False`|`False or x = x`|

```python
from functools import reduce

# Сложение
reduce(lambda x, y: x + y, [1, 2, 3], 0)  # 6

# Умножение
reduce(lambda x, y: x * y, [2, 3, 4], 1)  # 24

# Конкатенация строк
reduce(lambda x, y: x + y, ['a', 'b', 'c'], '')  # 'abc'

# Объединение списков
reduce(lambda x, y: x + y, [[1], [2], [3]], [])  # [1, 2, 3]
```

## Использование с модулем operator

Модуль operator предоставляет функции для стандартных операций Python:

```python
from functools import reduce
import operator

numbers = [1, 2, 3, 4, 5]

# Сложение
result = reduce(operator.add, numbers)
print(result)  # 15

# Умножение
result = reduce(operator.mul, numbers)
print(result)  # 120

# Конкатенация
words = ["Hello", " ", "World"]
result = reduce(operator.add, words)
print(result)  # "Hello World"

# Сравнение (gt = greater than)
numbers = [5, 3, 8, 1]
result = reduce(lambda x, y: x if x > y else y, numbers)
print(result)  # 8
```

**Популярные функции из operator:**

- `operator.add` — сложение (`+`)
- `operator.sub` — вычитание (`-`)
- `operator.mul` — умножение (`*`)
- `operator.truediv` — деление (`/`)
- `operator.and_` — логическое И
- `operator.or_` — логическое ИЛИ
- `operator.concat` — конкатенация

## Практические примеры

### Пример 1: Подсчет вложенных элементов

```python
from functools import reduce

# Подсчет общего количества элементов в списке списков
nested_lists = [[1, 2], [3, 4, 5], [6]]

total_count = reduce(lambda x, y: x + len(y), nested_lists, 0)
print(total_count)  # 6

# ✅ Более читаемый вариант
total_count = sum(len(lst) for lst in nested_lists)
print(total_count)  # 6
```

### Пример 2: Объединение словарей

```python
from functools import reduce

dicts = [
    {'a': 1, 'b': 2},
    {'c': 3, 'd': 4},
    {'e': 5}
]

# Объединение всех словарей
result = reduce(lambda x, y: {**x, **y}, dicts)
print(result)  # {'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}

# ✅ Более современный вариант (Python 3.9+)
result = {}
for d in dicts:
    result |= d
print(result)
```

### Пример 3: Глубокий доступ к вложенным словарям

```python
from functools import reduce
import operator

data = {
    'user': {
        'profile': {
            'address': {
                'city': 'Moscow'
            }
        }
    }
}

# Получение значения по цепочке ключей
keys = ['user', 'profile', 'address', 'city']
result = reduce(operator.getitem, keys, data)
print(result)  # 'Moscow'

# Или с lambda
result = reduce(lambda d, key: d[key], keys, data)
print(result)  # 'Moscow'
```

### Пример 4: Композиция функций

```python
from functools import reduce

def compose(*functions):
    """Создает композицию функций"""
    return lambda x: reduce(lambda acc, f: f(acc), functions, x)

# Определяем функции
def add_10(x):
    return x + 10

def multiply_2(x):
    return x * 2

def subtract_5(x):
    return x - 5

# Создаем композицию: subtract_5(multiply_2(add_10(x)))
composed = compose(add_10, multiply_2, subtract_5)

result = composed(5)
print(result)  # ((5 + 10) * 2) - 5 = 25
```

### Пример 5: Объединение списков

```python
from functools import reduce

# Объединение списков в один
lists = [[1, 2], [3, 4], [5, 6]]

result = reduce(lambda x, y: x + y, lists)
print(result)  # [1, 2, 3, 4, 5, 6]

# ✅ Более эффективный вариант
from itertools import chain
result = list(chain.from_iterable(lists))
print(result)  # [1, 2, 3, 4, 5, 6]
```

### Пример 6: Подсчет символов

```python
from functools import reduce
from collections import defaultdict

words = ['apple', 'banana', 'apricot', 'berry']

# Подсчет первых букв
result = reduce(
    lambda acc, word: {**acc, word[0]: acc.get(word[0], 0) + 1},
    words,
    {}
)
print(result)  # {'a': 2, 'b': 2}

# ✅ Более читаемый вариант
from collections import Counter
result = Counter(word[0] for word in words)
print(result)  # Counter({'a': 2, 'b': 2})
```

### Пример 7: GCD (наибольший общий делитель) нескольких чисел

```python
from functools import reduce
from math import gcd

numbers = [48, 64, 80, 96]

result = reduce(gcd, numbers)
print(result)  # 16 (НОД всех чисел)
```

### Пример 8: Вычисление выражения из списка

```python
from functools import reduce
import operator

# Вычисление: ((10 + 5) * 2) - 3
operations = [
    (operator.add, 5),
    (operator.mul, 2),
    (operator.sub, 3)
]

result = reduce(
    lambda acc, op: op[0](acc, op[1]),
    operations,
    10
)
print(result)  # 27
```

## Сравнение: reduce vs циклы vs встроенные функции

### Задача: Найти сумму квадратов четных чисел

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# 1️⃣ С циклом for (самый читаемый)
total = 0
for num in numbers:
    if num % 2 == 0:
        total += num ** 2
print(total)  # 220

# 2️⃣ С reduce (компактно, но менее читаемо)
total = reduce(
    lambda acc, x: acc + x**2 if x % 2 == 0 else acc,
    numbers,
    0
)
print(total)  # 220

# 3️⃣ С sum и генератором (идеально! ✅)
total = sum(x**2 for x in numbers if x % 2 == 0)
print(total)  # 220
```

## История reduce в Python

В Python 2 reduce была встроенной функцией, но в Python 3 её переместили в модуль functools. Причины:

1. **Появились специализированные функции**: `sum()`, `min()`, `max()`, `any()`, `all()`
2. **Проблемы с читаемостью**: код с reduce часто сложнее понять
3. **Производительность**: встроенные функции работают быстрее

## Когда использовать reduce?

### ✅ Используйте reduce когда:

1. **Композиция функций**
    
    ```python
    compose = lambda *fs: lambda x: reduce(lambda a, f: f(a), fs, x)
    ```
    
2. **Операции, для которых нет встроенной функции**
    
    ```python
    # НОД нескольких чисел
    reduce(gcd, numbers)
    ```
    
3. **Сложные агрегации с кастомной логикой**
    
    ```python
    # Объединение словарей с конфликтующими ключами
    reduce(lambda a, b: {**a, **b, 'total': a.get('total', 0) + b.get('total', 0)}, dicts)
    ```
    
4. **Функциональное программирование**
    
    - Когда пишете в функциональном стиле
    - Работаете с функциями высшего порядка

### ❌ Не используйте reduce когда:

1. **Есть встроенная функция**
    
    ```python
    # ❌ Плохо
    reduce(lambda x, y: x + y, numbers)
    
    # ✅ Хорошо
    sum(numbers)
    ```
    
2. **Есть специализированный инструмент**
    
    ```python
    # ❌ Плохо
    reduce(lambda x, y: x * y, numbers)
    
    # ✅ Хорошо (Python 3.8+)
    from math import prod
    prod(numbers)
    ```
    
3. **Цикл более читаем**
    
    ```python
    # ❌ Плохо
    reduce(lambda x, y: x + [y] if condition(y) else x, data, [])
    
    # ✅ Хорошо
    result = []
    for item in data:
        if condition(item):
            result.append(item)
    ```
    
4. **Нужны промежуточные результаты**
    
    ```python
    # ❌ Плохо с reduce
    
    # ✅ Хорошо с itertools.accumulate
    from itertools import accumulate
    list(accumulate(numbers, operator.add))  # [1, 3, 6, 10, 15]
    ```
    

## reduce vs accumulate

reduce возвращает одно финальное значение, а accumulate возвращает итератор со всеми промежуточными результатами:

```python
from functools import reduce
from itertools import accumulate
import operator

numbers = [1, 2, 3, 4, 5]

# reduce - только финальный результат
result = reduce(operator.add, numbers)
print(result)  # 15

# accumulate - все промежуточные результаты
result = list(accumulate(numbers, operator.add))
print(result)  # [1, 3, 6, 10, 15]
```

## Реализация других функций через reduce

reduce настолько мощная функция, что через неё можно реализовать многие встроенные функции:

```python
from functools import reduce
import operator

# sum()
_sum = lambda data: reduce(operator.add, data, 0)

# max()
_max = lambda data: reduce(lambda x, y: x if x > y else y, data)

# min()
_min = lambda data: reduce(lambda x, y: x if x < y else y, data)

# map()
f = str
_map = lambda data: reduce(lambda x, y: x + [f(y)], data, [])

# filter()
is_even = lambda n: n % 2 == 0
_filter = lambda data: reduce(
    lambda x, y: x + [y] if is_even(y) else x,
    data,
    []
)

# reversed()
_reversed = lambda data: reduce(lambda x, y: [y] + x, data, [])

# Примеры
print(_sum([1, 2, 3, 4, 5]))           # 15
print(_max([3, 7, 2, 9, 1]))           # 9
print(_map([1, 2, 3]))                 # ['1', '2', '3']
print(_filter([1, 2, 3, 4, 5]))        # [2, 4]
print(_reversed([1, 2, 3, 4]))         # [4, 3, 2, 1]
```

**Примечание:** Это академические примеры. В реальном коде используйте встроенные функции!

## Распространенные ошибки

### ❌ Ошибка 1: Забыли initializer для пустого списка

```python
from functools import reduce

# Неправильно
try:
    result = reduce(lambda x, y: x + y, [])
except TypeError:
    print("Ошибка!")

# Правильно
result = reduce(lambda x, y: x + y, [], 0)
print(result)  # 0
```

### ❌ Ошибка 2: Неправильный initializer

```python
from functools import reduce

numbers = [2, 3, 4]

# Неправильно - initializer 0 для умножения
result = reduce(lambda x, y: x * y, numbers, 0)
print(result)  # 0 (все умножилось на 0!)

# Правильно - initializer 1 для умножения
result = reduce(lambda x, y: x * y, numbers, 1)
print(result)  # 24
```

### ❌ Ошибка 3: Использование reduce вместо специализированной функции

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Плохо
result = reduce(lambda x, y: x + y, numbers)

# Хорошо
result = sum(numbers)
```

### ❌ Ошибка 4: Слишком сложная lambda

```python
from functools import reduce

# Плохо - нечитаемо
result = reduce(
    lambda acc, x: {**acc, x[0]: acc.get(x[0], []) + [x[1]]} if x[1] > 0 else acc,
    data,
    {}
)

# Хорошо - обычный цикл
result = {}
for key, value in data:
    if value > 0:
        if key not in result:
            result[key] = []
        result[key].append(value)
```

## Производительность

**Сравнение скорости** (приблизительно):

|Метод|Относительная скорость|
|---|---|
|`sum()`|1.0x (базовая линия)|
|`math.prod()`|1.0x|
|`reduce(operator.add)`|2-3x медленнее|
|`reduce(lambda)`|3-5x медленнее|
|Цикл for|1-2x медленнее|

**Вывод:** Встроенные функции написаны на C и работают быстрее reduce.

## Полезные ссылки

- [Официальная документация functools.reduce](https://docs.python.org/3/library/functools.html#functools.reduce)
- [PEP 20 — The Zen of Python](https://www.python.org/dev/peps/pep-0020/) (простота важнее)
- [Guido van Rossum о reduce](https://www.artima.com/weblogs/viewpost.jsp?thread=98196)

## 🔗 Связанные темы

- [[04 — 🧰 functools]]
- [[00 — 🎯 map()]]
- [[01 — 🎯 filter()]]
- [[00 — 🎯 Встроенные функции Python]]
