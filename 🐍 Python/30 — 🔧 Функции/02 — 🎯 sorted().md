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

# Памятка: Функция sorted() в Python

## 💡 Полезные советы

- **sorted()** создаёт новый отсортированный список, не изменяет оригинал
- **list.sort()** сортирует на месте (изменяет оригинал), возвращает None
- **key** - функция для определения ключа сортировки
- **reverse=True** - сортировка в обратном порядке
- **Стабильная сортировка**: элементы с одинаковыми ключами сохраняют порядок
- **Работает с любыми итерируемыми**: списки, кортежи, строки, словари
- **lambda** - удобно для key при сортировке по кастомным критериям
- **Сложные критерии**: можно возвращать кортеж из key

## 🚀 Быстрая справка

|Задача                |Синтаксис                                   |Пример                                          |
|----------------------|--------------------------------------------|------------------------------------------------|
|Базовая сортировка    |`sorted(iterable)`                          |`sorted([3, 1, 2])` → `[1, 2, 3]`               |
|Обратный порядок      |`sorted(iterable, reverse=True)`            |`sorted([1, 2, 3], reverse=True)` → `[3, 2, 1]` |
|По ключу              |`sorted(iterable, key=func)`                |`sorted(['a', 'bb'], key=len)` → `['a', 'bb']`  |
|По длине              |`sorted(iterable, key=len)`                 |`sorted(['aaa', 'b', 'cc'], key=len)`           |
|По модулю             |`sorted(iterable, key=abs)`                 |`sorted([-2, 1, -3], key=abs)` → `[1, -2, -3]`  |
|Регистронезависимо    |`sorted(iterable, key=str.lower)`           |`sorted(['B', 'a'], key=str.lower)`             |
|По атрибуту           |`sorted(iterable, key=lambda x: x.attr)`    |`sorted(users, key=lambda u: u.age)`            |
|По элементу кортежа   |`sorted(iterable, key=lambda x: x[1])`      |`sorted([(1,'b'), (2,'a')], key=lambda x: x[1])`|
|Множественные критерии|`sorted(iterable, key=lambda x: (x.a, x.b))`|`sorted(data, key=lambda x: (x.age, x.name))`   |

## 📝 Базовая сортировка

```python
# Сортировка списка чисел
numbers = [3, 1, 4, 1, 5, 9, 2, 6]
result = sorted(numbers)
print(result)                   # → [1, 1, 2, 3, 4, 5, 6, 9]
print(numbers)                  # → [3, 1, 4, 1, 5, 9, 2, 6] (не изменился!)

# Сортировка строк (по алфавиту)
words = ['banana', 'apple', 'cherry', 'date']
result = sorted(words)
print(result)                   # → ['apple', 'banana', 'cherry', 'date']

# Сортировка в обратном порядке
numbers = [3, 1, 4, 1, 5, 9, 2]
result = sorted(numbers, reverse=True)
print(result)                   # → [9, 5, 4, 3, 2, 1, 1]

# Сортировка кортежа (возвращает список!)
tuple_nums = (3, 1, 4, 1, 5)
result = sorted(tuple_nums)
print(result)                   # → [1, 1, 3, 4, 5] (список!)
print(type(result))             # → <class 'list'>

# Сортировка строки (возвращает список символов!)
text = "python"
result = sorted(text)
print(result)                   # → ['h', 'n', 'o', 'p', 't', 'y']
print(''.join(result))          # → "hnopty"

# Сортировка множества
numbers_set = {3, 1, 4, 1, 5, 9}
result = sorted(numbers_set)
print(result)                   # → [1, 3, 4, 5, 9] (уникальные, отсортированные)
```

## 🔄 sorted() vs list.sort()

```python
numbers = [3, 1, 4, 1, 5, 9, 2]

# ========== sorted() - создаёт НОВЫЙ список ==========
result = sorted(numbers)
print(result)                   # → [1, 1, 2, 3, 4, 5, 9]
print(numbers)                  # → [3, 1, 4, 1, 5, 9, 2] (не изменился!)

# ✅ Возвращает новый список
# ✅ Работает с любыми итерируемыми
# ✅ Не изменяет оригинал

# ========== list.sort() - сортирует НА МЕСТЕ ==========
numbers = [3, 1, 4, 1, 5, 9, 2]
result = numbers.sort()         # → None
print(result)                   # → None (!)
print(numbers)                  # → [1, 1, 2, 3, 4, 5, 9] (изменился!)

# ✅ Изменяет оригинальный список
# ✅ Экономит память (не создаёт копию)
# ❌ Работает только со списками
# ⚠️ Возвращает None!

# Когда что использовать:
# sorted() - когда нужно сохранить оригинал
original = [3, 1, 2]
sorted_copy = sorted(original)  # Оригинал не изменился

# sort() - когда оригинал не нужен и важна память
numbers = [3, 1, 2]
numbers.sort()                  # Изменили на месте
```

## 🔑 Сортировка с ключом (key)

### Базовые примеры с key

```python
# По длине строк
words = ['python', 'is', 'awesome', 'programming']
result = sorted(words, key=len)
print(result)                   # → ['is', 'python', 'awesome', 'programming']

# По абсолютному значению
numbers = [-5, 2, -3, 1, -4]
result = sorted(numbers, key=abs)
print(result)                   # → [1, 2, -3, -4, -5]

# Без учёта регистра
words = ['banana', 'Apple', 'cherry', 'Date']
result = sorted(words, key=str.lower)
print(result)                   # → ['Apple', 'banana', 'cherry', 'Date']

# По второму символу
words = ['ab', 'ca', 'bc']
result = sorted(words, key=lambda x: x[1])
print(result)                   # → ['ca', 'ab', 'bc']

# По остатку от деления
numbers = [10, 21, 32, 43, 54]
result = sorted(numbers, key=lambda x: x % 10)
print(result)                   # → [10, 21, 32, 43, 54]
```

### Сортировка словарей

```python
# По ключам словаря
data = {'z': 1, 'a': 2, 'c': 3}
result = sorted(data)           # Сортирует ключи!
print(result)                   # → ['a', 'c', 'z']

# По значениям словаря
data = {'a': 3, 'b': 1, 'c': 2}
result = sorted(data.items(), key=lambda x: x[1])
print(result)                   # → [('b', 1), ('c', 2), ('a', 3)]

# Получить только ключи, отсортированные по значениям
result = sorted(data, key=data.get)
print(result)                   # → ['b', 'c', 'a']
```

### Сортировка списка словарей

```python
users = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35}
]

# По возрасту
result = sorted(users, key=lambda x: x['age'])
# → [{'name': 'Bob', 'age': 25}, {'name': 'Alice', 'age': 30}, ...]

# По имени
result = sorted(users, key=lambda x: x['name'])
# → [{'name': 'Alice', ...}, {'name': 'Bob', ...}, ...]

# В обратном порядке по возрасту
result = sorted(users, key=lambda x: x['age'], reverse=True)
# → [{'name': 'Charlie', 'age': 35}, {'name': 'Alice', 'age': 30}, ...]
```

### Сортировка кортежей

```python
# Список кортежей
data = [('Alice', 30), ('Bob', 25), ('Charlie', 35)]

# По первому элементу (по умолчанию)
result = sorted(data)
print(result)                   # → [('Alice', 30), ('Bob', 25), ('Charlie', 35)]

# По второму элементу (возраст)
result = sorted(data, key=lambda x: x[1])
print(result)                   # → [('Bob', 25), ('Alice', 30), ('Charlie', 35)]

# Альтернатива через itemgetter
from operator import itemgetter
result = sorted(data, key=itemgetter(1))
print(result)                   # → [('Bob', 25), ('Alice', 30), ('Charlie', 35)]
```

## 📊 Множественные критерии сортировки

```python
# Сортировка по нескольким полям
students = [
    ('Alice', 25, 85),
    ('Bob', 25, 90),
    ('Charlie', 23, 85),
    ('David', 23, 80)
]

# По возрасту, потом по баллам
result = sorted(students, key=lambda x: (x[1], x[2]))
# → [('David', 23, 80), ('Charlie', 23, 85), ('Alice', 25, 85), ('Bob', 25, 90)]

# По возрасту (возр.), по баллам (убыв.)
result = sorted(students, key=lambda x: (x[1], -x[2]))
# → [('Charlie', 23, 85), ('David', 23, 80), ('Bob', 25, 90), ('Alice', 25, 85)]

# Список словарей с несколькими критериями
users = [
    {'name': 'Alice', 'age': 30, 'score': 85},
    {'name': 'Bob', 'age': 25, 'score': 90},
    {'name': 'Charlie', 'age': 30, 'score': 80},
    {'name': 'David', 'age': 25, 'score': 85}
]

# По возрасту, потом по баллам
result = sorted(users, key=lambda x: (x['age'], x['score']))

# По возрасту (возр.), по баллам (убыв.)
result = sorted(users, key=lambda x: (x['age'], -x['score']))

# По имени (без учёта регистра), потом по возрасту
result = sorted(users, key=lambda x: (x['name'].lower(), x['age']))
```

## 🎯 Сортировка объектов класса

```python
# Класс с атрибутами
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __repr__(self):
        return f"Person('{self.name}', {self.age})"

people = [
    Person('Alice', 30),
    Person('Bob', 25),
    Person('Charlie', 35)
]

# По возрасту
result = sorted(people, key=lambda p: p.age)
print(result)                   # → [Person('Bob', 25), Person('Alice', 30), ...]

# По имени
result = sorted(people, key=lambda p: p.name)
print(result)                   # → [Person('Alice', 30), Person('Bob', 25), ...]

# Альтернатива через attrgetter
from operator import attrgetter
result = sorted(people, key=attrgetter('age'))
result = sorted(people, key=attrgetter('name'))

# По нескольким атрибутам
result = sorted(people, key=lambda p: (p.age, p.name))
result = sorted(people, key=attrgetter('age', 'name'))
```

## 🔧 Специальные случаи

### Сортировка строк с числами

```python
# Проблема: лексикографическая сортировка
files = ['file10.txt', 'file2.txt', 'file1.txt', 'file20.txt']
result = sorted(files)
print(result)                   # → ['file1.txt', 'file10.txt', 'file2.txt', 'file20.txt']
# Неправильный порядок!

# Решение: естественная сортировка
import re

def natural_sort_key(text):
    """Ключ для естественной сортировки"""
    return [int(c) if c.isdigit() else c.lower() 
            for c in re.split(r'(\d+)', text)]

result = sorted(files, key=natural_sort_key)
print(result)                   # → ['file1.txt', 'file2.txt', 'file10.txt', 'file20.txt']
# Правильный порядок!
```

### Сортировка с None значениями

```python
# Список с None
data = [3, None, 1, None, 5, 2]

# ❌ Не работает напрямую
# sorted(data)                  # TypeError!

# ✅ None в конец
result = sorted(data, key=lambda x: (x is None, x))
print(result)                   # → [1, 2, 3, 5, None, None]

# ✅ None в начало
result = sorted(data, key=lambda x: (x is not None, x))
print(result)                   # → [None, None, 1, 2, 3, 5]
```

### Сортировка по нескольким ключам с разным порядком

```python
# По возрасту возрастание, по имени убывание
data = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 30},
    {'name': 'David', 'age': 25}
]

# Для чисел: используйте минус для убывания
result = sorted(data, key=lambda x: (x['age'], -ord(x['name'][0])))

# Для строк: сложнее, используйте multiple sorted
result = sorted(data, key=lambda x: x['name'], reverse=True)
result = sorted(result, key=lambda x: x['age'])
# Стабильная сортировка сохранит порядок имён внутри возрастов
```

## 🔍 Модуль operator

```python
from operator import itemgetter, attrgetter, methodcaller

# ========== itemgetter - для индексов/ключей ==========

# Для кортежей/списков
data = [('Alice', 30), ('Bob', 25), ('Charlie', 35)]
result = sorted(data, key=itemgetter(1))  # По индексу 1
# → [('Bob', 25), ('Alice', 30), ('Charlie', 35)]

# Для словарей
users = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25}
]
result = sorted(users, key=itemgetter('age'))

# Множественные ключи
data = [('Alice', 30, 85), ('Bob', 25, 90)]
result = sorted(data, key=itemgetter(1, 2))  # По индексам 1 и 2

# ========== attrgetter - для атрибутов объектов ==========

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

people = [Person('Alice', 30), Person('Bob', 25)]
result = sorted(people, key=attrgetter('age'))

# Множественные атрибуты
result = sorted(people, key=attrgetter('age', 'name'))

# ========== methodcaller - для вызова методов ==========

words = ['  hello  ', '  world  ', '  python  ']
result = sorted(words, key=methodcaller('strip'))
# → ['  hello  ', '  python  ', '  world  ']

# С аргументами метода
words = ['Apple', 'banana', 'Cherry']
result = sorted(words, key=methodcaller('lower'))
# → ['Apple', 'banana', 'Cherry']

# ========== Сравнение производительности ==========

# lambda - медленнее
sorted(data, key=lambda x: x[1])

# itemgetter - быстрее (на ~25-30%)
sorted(data, key=itemgetter(1))

# ✅ Используйте operator для производительности
```

## 🎯 Практические примеры

### Топ-N элементов

```python
# Топ-3 самых больших числа
numbers = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
top3 = sorted(numbers, reverse=True)[:3]
print(top3)                     # → [9, 6, 5]

# Топ-3 самых длинных слова
words = ['python', 'is', 'awesome', 'programming', 'language']
top3 = sorted(words, key=len, reverse=True)[:3]
print(top3)                     # → ['programming', 'language', 'awesome']

# Топ-3 пользователей по возрасту
users = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35},
    {'name': 'David', 'age': 28}
]
top3 = sorted(users, key=lambda x: x['age'], reverse=True)[:3]
```

### Группировка после сортировки

```python
from itertools import groupby

# Группировка по первой букве
words = ['apple', 'banana', 'apricot', 'cherry', 'avocado', 'blueberry']

# ВАЖНО: сначала сортируем!
sorted_words = sorted(words, key=lambda x: x[0])

# Затем группируем
for letter, group in groupby(sorted_words, key=lambda x: x[0]):
    print(f"{letter}: {list(group)}")
# a: ['apple', 'apricot', 'avocado']
# b: ['banana', 'blueberry']
# c: ['cherry']
```

### Сортировка файлов по размеру

```python
from pathlib import Path

# Получить все файлы
files = list(Path('.').glob('*.txt'))

# Сортировать по размеру
sorted_files = sorted(files, key=lambda f: f.stat().st_size)

for file in sorted_files:
    size = file.stat().st_size
    print(f"{file.name}: {size} bytes")
```

### Сортировка дат

```python
from datetime import datetime

dates_str = ['2024-03-15', '2023-12-01', '2024-01-20', '2023-06-30']

# Преобразовать и отсортировать
dates = [datetime.strptime(d, '%Y-%m-%d') for d in dates_str]
sorted_dates = sorted(dates)

# Обратно в строки
result = [d.strftime('%Y-%m-%d') for d in sorted_dates]
print(result)                   # → ['2023-06-30', '2023-12-01', '2024-01-20', '2024-03-15']

# Или сортировать строки напрямую (работает для ISO формата)
result = sorted(dates_str)
```

### Сортировка IP-адресов

```python
# IP адреса
ips = ['192.168.1.10', '192.168.1.2', '10.0.0.1', '192.168.1.100']

# Правильная сортировка
def ip_key(ip):
    return tuple(int(part) for part in ip.split('.'))

sorted_ips = sorted(ips, key=ip_key)
print(sorted_ips)               # → ['10.0.0.1', '192.168.1.2', '192.168.1.10', '192.168.1.100']
```

## ⚡ Производительность и оптимизация

```python
# Для больших данных важна производительность

# ❌ Медленно: сортировка со сложной lambda
data = [...]  # миллионы элементов
result = sorted(data, key=lambda x: complex_function(x))

# ✅ Быстрее: предвычислить ключи
keys = [complex_function(x) for x in data]
result = [x for _, x in sorted(zip(keys, data))]

# ✅ Или используйте декоратор
def memoize(func):
    cache = {}
    def wrapper(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]
    return wrapper

@memoize
def complex_function(x):
    # сложные вычисления
    return result

# Теперь кэшируется
result = sorted(data, key=complex_function)
```

## ⚠️ Частые ошибки

```python
# ❌ sort() возвращает None
numbers = [3, 1, 2]
result = numbers.sort()
print(result)                   # → None (!)

# ✅ sort() изменяет на месте
numbers = [3, 1, 2]
numbers.sort()
print(numbers)                  # → [1, 2, 3]

# ❌ Забыли key= для lambda
words = ['aaa', 'b', 'cc']
result = sorted(words, lambda x: len(x))  # SyntaxError!

# ✅ Используйте key=
result = sorted(words, key=lambda x: len(x))

# ❌ Сортировка смешанных типов (Python 3)
mixed = [1, 'a', 2, 'b']
# sorted(mixed)                 # TypeError!

# ✅ Преобразуйте к одному типу
result = sorted(mixed, key=str)

# ⚠️ Изменение оригинала при использовании sorted
numbers = [3, 1, 2]
result = sorted(numbers)        # НЕ изменяет numbers
numbers = sorted(numbers)       # Перезаписывает numbers
```

## 🎯 Когда использовать sorted()

**✅ Используйте sorted() когда:**

- Нужно сохранить оригинальный порядок
- Работаете с кортежами, строками, множествами
- Нужно отсортированное представление словаря
- Создаёте новую структуру данных
- Функциональный стиль программирования

**✅ Используйте list.sort() когда:**

- Работаете со списками
- Оригинальный порядок не нужен
- Важна экономия памяти
- Оптимизируете производительность
- Список уже существует и нужно изменить его

```python
# ✅ sorted() - для сохранения оригинала
original = [3, 1, 2]
sorted_copy = sorted(original)
print(original)                 # → [3, 1, 2] (не изменился)
print(sorted_copy)              # → [1, 2, 3]

# ✅ sort() - когда оригинал не нужен
numbers = [3, 1, 2]
numbers.sort()
print(numbers)                  # → [1, 2, 3] (изменился)
```

## 🔗 Связанные темы

- [[00 — 🎯 map()]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[🌊 Lambda функции]]
- [[Сортировка - приоритет группы через кортеж в key]]
- [[00 — Встроенные функции Python]]
