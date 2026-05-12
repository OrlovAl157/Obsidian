---
tags:
  - python
  - тема/функции
  - тема/встроенные_функции
  - статус/завершён
уровень: начальный
возвращает_итератор: false
ленивые_вычисления: false
принимает_key: true
встроенная: true
---

## 💡 Полезные советы

- **min()** и **max()** находят минимальный и максимальный элементы
- **Работают с любыми итерируемыми**: списки, кортежи, строки, множества
- **Несколько аргументов**: можно передать элементы отдельно или итерируемый объект
- **key** - функция для определения критерия сравнения
- **default** - значение по умолчанию для пустых последовательностей
- **Для строк**: сравнение лексикографическое (по алфавиту)
- **lambda**: удобно для key при сложных критериях
- **Вызовет ошибку**: на пустой последовательности без default

## 🚀 Быстрая справка

|Задача|Синтаксис|Пример|
|---|---|---|
|Минимум из чисел|`min(a, b, c, ...)`|`min(1, 5, 3)` → `1`|
|Максимум из чисел|`max(a, b, c, ...)`|`max(1, 5, 3)` → `5`|
|Минимум списка|`min(iterable)`|`min([1, 5, 3])` → `1`|
|Максимум списка|`max(iterable)`|`max([1, 5, 3])` → `5`|
|По ключу|`min(iterable, key=func)`|`min(['a', 'bbb'], key=len)` → `'a'`|
|Значение по умолчанию|`min(iterable, default=value)`|`min([], default=0)` → `0`|
|Минимум по длине|`min(words, key=len)`|`min(['hi', 'hello'], key=len)` → `'hi'`|
|Максимум по модулю|`max(nums, key=abs)`|`max([-5, 2, -3], key=abs)` → `-5`|
|Минимум по атрибуту|`min(objects, key=lambda x: x.attr)`|`min(users, key=lambda u: u.age)`|

## 📝 Базовое использование

```python
# ========== НЕСКОЛЬКО АРГУМЕНТОВ ==========

# Минимум из нескольких чисел
min(5, 2, 8, 1, 9)              # → 1
max(5, 2, 8, 1, 9)              # → 9

# С двумя аргументами
min(10, 20)                     # → 10
max(10, 20)                     # → 20

# Со строками (лексикографическое сравнение)
min('apple', 'banana', 'cherry') # → 'apple'
max('apple', 'banana', 'cherry') # → 'cherry'

# ========== ИТЕРИРУЕМЫЕ ОБЪЕКТЫ ==========

# Из списка
numbers = [5, 2, 8, 1, 9]
min(numbers)                    # → 1
max(numbers)                    # → 9

# Из кортежа
numbers = (5, 2, 8, 1, 9)
min(numbers)                    # → 1
max(numbers)                    # → 9

# Из множества
numbers = {5, 2, 8, 1, 9}
min(numbers)                    # → 1
max(numbers)                    # → 9

# Из строки (по символам)
text = "python"
min(text)                       # → 'h' (минимальный ASCII)
max(text)                       # → 'y' (максимальный ASCII)

# Из range
min(range(10))                  # → 0
max(range(10))                  # → 9

# ❌ Ошибка на пустой последовательности
# min([])                       # ValueError: min() arg is an empty sequence
# max([])                       # ValueError: max() arg is an empty sequence
```

## 🔑 Использование с ключом (key)

```python
# ========== СРАВНЕНИЕ ПО ДЛИНЕ ==========

words = ['python', 'is', 'awesome', 'programming']

# Самое короткое слово
min(words, key=len)             # → 'is'

# Самое длинное слово
max(words, key=len)             # → 'programming'

# ========== СРАВНЕНИЕ ПО МОДУЛЮ ==========

numbers = [-5, 2, -8, 1, -3]

# Минимальное по модулю
min(numbers, key=abs)           # → 1
# Максимальное по модулю
max(numbers, key=abs)           # → -8

# ========== БЕЗ УЧЁТА РЕГИСТРА ==========

words = ['banana', 'Apple', 'cherry', 'Date']

# По алфавиту без учёта регистра
min(words, key=str.lower)       # → 'Apple'
max(words, key=str.lower)       # → 'Date'

# ========== ПО ВТОРОМУ ЭЛЕМЕНТУ ==========

pairs = [(1, 5), (3, 2), (2, 8), (4, 1)]

# Пара с минимальным вторым элементом
min(pairs, key=lambda x: x[1])  # → (4, 1)

# Пара с максимальным вторым элементом
max(pairs, key=lambda x: x[1])  # → (2, 8)

# ========== ПО СУММЕ ==========

lists = [[1, 2], [5, 1], [2, 2, 2]]

# Список с минимальной суммой
min(lists, key=sum)             # → [1, 2]

# Список с максимальной суммой
max(lists, key=sum)             # → [5, 1]
```

## 📊 Работа со словарями

```python
# ========== СОРТИРОВКА ПО КЛЮЧАМ ==========

data = {'z': 1, 'a': 5, 'c': 3}

# Минимальный ключ
min(data)                       # → 'a'
max(data)                       # → 'z'

# ========== СОРТИРОВКА ПО ЗНАЧЕНИЯМ ==========

# Ключ с минимальным значением
min(data, key=data.get)         # → 'z' (значение 1)

# Ключ с максимальным значением
max(data, key=data.get)         # → 'a' (значение 5)

# Минимальное значение
min(data.values())              # → 1
max(data.values())              # → 5

# Пара (ключ, значение) с минимальным значением
min(data.items(), key=lambda x: x[1])  # → ('z', 1)
max(data.items(), key=lambda x: x[1])  # → ('a', 5)

# ========== СПИСОК СЛОВАРЕЙ ==========

users = [
    {'name': 'Alice', 'age': 30, 'score': 85},
    {'name': 'Bob', 'age': 25, 'score': 90},
    {'name': 'Charlie', 'age': 35, 'score': 75}
]

# Самый молодой
youngest = min(users, key=lambda u: u['age'])
# → {'name': 'Bob', 'age': 25, 'score': 90}

# Самый старший
oldest = max(users, key=lambda u: u['age'])
# → {'name': 'Charlie', 'age': 35, 'score': 75}

# Лучший балл
best_score = max(users, key=lambda u: u['score'])
# → {'name': 'Bob', 'age': 25, 'score': 90}

# Худший балл
worst_score = min(users, key=lambda u: u['score'])
# → {'name': 'Charlie', 'age': 35, 'score': 75}
```

## 🎯 Работа с объектами классов

```python
class Person:
    def __init__(self, name, age, salary):
        self.name = name
        self.age = age
        self.salary = salary
    
    def __repr__(self):
        return f"Person('{self.name}', {self.age}, {self.salary})"

people = [
    Person('Alice', 30, 50000),
    Person('Bob', 25, 60000),
    Person('Charlie', 35, 45000)
]

# По возрасту
youngest = min(people, key=lambda p: p.age)
oldest = max(people, key=lambda p: p.age)

# По зарплате
lowest_paid = min(people, key=lambda p: p.salary)
highest_paid = max(people, key=lambda p: p.salary)

# По имени
first_name = min(people, key=lambda p: p.name)  # → Alice

# Альтернатива через attrgetter
from operator import attrgetter

youngest = min(people, key=attrgetter('age'))
highest_paid = max(people, key=attrgetter('salary'))
```

## 🛡️ Значение по умолчанию (default)

```python
# ========== БЕЗ DEFAULT ==========

numbers = []
# min(numbers)                  # ❌ ValueError!
# max(numbers)                  # ❌ ValueError!

# ========== С DEFAULT ==========

numbers = []
min(numbers, default=0)         # → 0 (вместо ошибки)
max(numbers, default=0)         # → 0 (вместо ошибки)

# Практический пример
def get_min_score(scores):
    """Получить минимальный балл или 0 если список пустой"""
    return min(scores, default=0)

get_min_score([85, 90, 75])     # → 75
get_min_score([])               # → 0 (вместо ошибки)

# С фильтрацией
numbers = [1, 2, 3, 4, 5]
evens = [x for x in numbers if x % 2 == 0]
min_even = min(evens, default=None)  # → 2

numbers = [1, 3, 5, 7]
evens = [x for x in numbers if x % 2 == 0]
min_even = min(evens, default=None)  # → None (нет чётных)

# ⚠️ default работает только с итерируемым, не с аргументами
# min(default=0)                # ❌ TypeError!
min([], default=0)              # ✅ 0
```

## 🎯 Практические примеры

### Поиск экстремальных значений

```python
# Температуры за неделю
temps = [22, 25, 19, 28, 24, 26, 23]

min_temp = min(temps)           # → 19
max_temp = max(temps)           # → 28
temp_range = max_temp - min_temp  # → 9

print(f"Диапазон температур: {temp_range}°C")

# Цены на товары
prices = {'apple': 50, 'banana': 30, 'cherry': 80}

cheapest = min(prices, key=prices.get)
most_expensive = max(prices, key=prices.get)

print(f"Самый дешёвый: {cheapest} = {prices[cheapest]}₽")
print(f"Самый дорогой: {most_expensive} = {prices[most_expensive]}₽")
```

### Оценки студентов

```python
students = [
    {'name': 'Alice', 'score': 85},
    {'name': 'Bob', 'score': 90},
    {'name': 'Charlie', 'score': 78},
    {'name': 'David', 'score': 92}
]

# Лучший и худший результат
best = max(students, key=lambda s: s['score'])
worst = min(students, key=lambda s: s['score'])

print(f"Лучший результат: {best['name']} - {best['score']}")
print(f"Худший результат: {worst['name']} - {worst['score']}")

# Средний балл
avg_score = sum(s['score'] for s in students) / len(students)
print(f"Средний балл: {avg_score:.1f}")
```

### Поиск ближайшего значения

```python
def find_closest(target, numbers):
    """Найти ближайшее число к target"""
    return min(numbers, key=lambda x: abs(x - target))

numbers = [10, 20, 35, 50, 80]
target = 30

closest = find_closest(target, numbers)
print(f"Ближайшее к {target}: {closest}")  # → 35

# Альтернатива для максимально близкого
def find_furthest(target, numbers):
    """Найти самое далёкое число от target"""
    return max(numbers, key=lambda x: abs(x - target))

furthest = find_furthest(target, numbers)
print(f"Самое далёкое от {target}: {furthest}")  # → 80
```

### Сравнение дат

```python
from datetime import datetime

dates = [
    datetime(2024, 3, 15),
    datetime(2023, 12, 1),
    datetime(2024, 1, 20),
    datetime(2023, 6, 30)
]

earliest = min(dates)
latest = max(dates)

print(f"Самая ранняя: {earliest.strftime('%Y-%m-%d')}")  # → 2023-06-30
print(f"Самая поздняя: {latest.strftime('%Y-%m-%d')}")   # → 2024-03-15
```

### Файлы по размеру

```python
from pathlib import Path

# Получить все файлы
files = list(Path('.').glob('*.txt'))

if files:
    # Самый маленький файл
    smallest = min(files, key=lambda f: f.stat().st_size)
    print(f"Самый маленький: {smallest.name} - {smallest.stat().st_size} bytes")
    
    # Самый большой файл
    largest = max(files, key=lambda f: f.stat().st_size)
    print(f"Самый большой: {largest.name} - {largest.stat().st_size} bytes")
```

### Координаты - ближайшая точка

```python
import math

def distance(p1, p2):
    """Расстояние между двумя точками"""
    return math.sqrt((p1[0] - p2[0])**2 + (p1[1] - p2[1])**2)

points = [(1, 2), (5, 7), (3, 4), (8, 1)]
origin = (0, 0)

# Ближайшая точка к началу координат
closest = min(points, key=lambda p: distance(p, origin))
print(f"Ближайшая к (0,0): {closest}")  # → (1, 2)

# Самая дальняя точка
furthest = max(points, key=lambda p: distance(p, origin))
print(f"Самая дальняя от (0,0): {furthest}")  # → (8, 1) или (5, 7)
```

## 🔧 Комбинации с другими функциями

```python
# ========== С FILTER ==========

numbers = [-5, -3, -1, 0, 2, 4, 6]

# Максимальное положительное
max_positive = max(filter(lambda x: x > 0, numbers))  # → 6

# Минимальное отрицательное
min_negative = min(filter(lambda x: x < 0, numbers))  # → -5

# С default при пустом фильтре
numbers = [1, 2, 3]
max_negative = max(filter(lambda x: x < 0, numbers), default=None)  # → None

# ========== С MAP ==========

words = ['python', 'is', 'awesome']

# Максимальная длина
max_length = max(map(len, words))  # → 7

# Минимальная длина
min_length = min(map(len, words))  # → 2

# ========== ВЛОЖЕННЫЕ MIN/MAX ==========

matrix = [
    [1, 5, 3],
    [4, 2, 8],
    [7, 6, 9]
]

# Минимум в каждой строке
row_mins = [min(row) for row in matrix]  # → [1, 2, 6]

# Максимум в каждой строке
row_maxs = [max(row) for row in matrix]  # → [5, 8, 9]

# Глобальный минимум
global_min = min(min(row) for row in matrix)  # → 1

# Глобальный максимум
global_max = max(max(row) for row in matrix)  # → 9

# ========== С ENUMERATE ==========

scores = [85, 92, 78, 95, 88]

# Индекс максимального элемента
max_index = max(range(len(scores)), key=lambda i: scores[i])  # → 3

# Индекс минимального элемента
min_index = min(range(len(scores)), key=lambda i: scores[i])  # → 2

# Альтернатива
max_index = scores.index(max(scores))
min_index = scores.index(min(scores))
```

## ⚠️ Частые ошибки

```python
# ❌ Пустая последовательность без default
# min([])                       # ValueError!
# max([])                       # ValueError!

# ✅ Используйте default
min([], default=0)              # → 0

# ❌ Смешанные типы (Python 3)
# min(1, 'a')                   # TypeError!
# min([1, 2, 'a'])              # TypeError!

# ✅ Преобразуйте к одному типу
min([1, 2, 3], key=str)         # → 1

# ❌ Забыли key= для lambda
numbers = [1, -5, 3, -2]
# min(numbers, lambda x: abs(x))  # TypeError!

# ✅ Используйте key=
min(numbers, key=lambda x: abs(x))  # → 1

# ❌ Путаница с несколькими аргументами
numbers = [1, 5, 3]
# min(numbers, 10)              # TypeError! (сравнивает список и число)

# ✅ Используйте * для распаковки или передайте список
min(*numbers, 10)               # → 1
min(numbers + [10])             # → 1

# ⚠️ min/max не изменяют оригинал
numbers = [5, 2, 8, 1, 9]
min_value = min(numbers)        # → 1
print(numbers)                  # → [5, 2, 8, 1, 9] (не изменился)

# ⚠️ key возвращает критерий, но результат - оригинальный элемент
words = ['python', 'is', 'cool']
shortest = min(words, key=len)
print(shortest)                 # → 'is' (слово, не его длина!)
print(len(shortest))            # → 2 (длина)
```

## 🎯 Когда использовать min/max

**✅ Используйте min() и max() когда:**

- Нужно найти экстремальное значение
- Работаете с числами, строками, датами
- Сравниваете объекты по критерию (key)
- Ищете лучший/худший результат
- Нужно простое и читабельное решение

**🔄 Альтернативы:**

- `sorted()[0]` и `sorted()[-1]` - если нужна вся отсортированная последовательность
- Ручной цикл - если нужна дополнительная логика при поиске
- `numpy.min()` и `numpy.max()` - для больших числовых массивов (быстрее)

```python
numbers = [5, 2, 8, 1, 9]

# ✅ min/max - когда нужно только одно значение
min_value = min(numbers)        # → 1 (быстро)
max_value = max(numbers)        # → 9

# ❌ sorted - избыточно если нужно только мин/макс
sorted_nums = sorted(numbers)
min_value = sorted_nums[0]      # → медленнее
max_value = sorted_nums[-1]

# ✅ sorted - когда нужны несколько значений
top3 = sorted(numbers, reverse=True)[:3]  # → [9, 8, 5]
```

## 🔗 Связанные темы

- [[02 — 🎯 sorted()]]
- [[🌊 Lambda функции]]
- [[00 — 🎯 Встроенные функции Python]]
