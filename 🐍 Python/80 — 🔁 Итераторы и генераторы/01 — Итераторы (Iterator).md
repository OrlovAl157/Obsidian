---
tags:
  - python
  - тема/итераторы_генераторы
  - статус/завершён
уровень: средний
ленивые_вычисления: true
одноразовый: true
бесконечный: false
---

# 🔄 Итераторы (Iterator)

## 📌 Коротко

> Итератор — объект, который выдаёт элементы по одному через `next()` и возбуждает `StopIteration` когда элементы закончились; лежит в основе всех циклов `for` в Python.

---

## 💡 Полезные советы

- **Итераторы одноразовые** — после исчерпания повторно использовать нельзя, нужно создавать новый через `iter()`
- **`next()` со значением по умолчанию** — `next(it, default)` не бросает исключение, возвращает `default` вместо `StopIteration`
- **Осторожно с `in`** — оператор `in` для итератора опустошает его до найденного элемента
- **`max()` и `min()` опустошают итератор** — после них итератор пуст, `sorted()` возвращает список, а не итератор
- **Не изменяй коллекцию во время итерации** — приводит к непредсказуемому поведению
- **Несколько независимых итераторов** — из одной коллекции можно создать сколько угодно через `iter()`

## 🚀 Быстрая справка

|Задача|Способ|Пример|
|---|---|---|
|Создать итератор|`iter(iterable)`|`it = iter([1, 2, 3])`|
|Следующий элемент|`next(it)`|`next(it)` → `1`|
|Следующий с дефолтом|`next(it, default)`|`next(it, -1)`|
|Применить функцию|`map(func, it)`|`map(str.upper, words)`|
|Отфильтровать|`filter(func, it)`|`filter(None, items)`|
|Пронумеровать|`enumerate(it, start=0)`|`enumerate(items, 1)`|
|Объединить|`zip(*iterables)`|`zip(keys, values)`|
|Перевернуть|`reversed(seq)`|`reversed([1,2,3])`|
|Iter с sentinel|`iter(callable, sentinel)`|`iter(file.readline, '')`|

---

## 📖 Теория

### Основы

**Итерируемый объект (Iterable)** — объект, по которому можно пройтись в цикле `for`. Это любой объект с методом `__iter__()`.

**Итератор (Iterator)** — специальный объект, который выдаёт элементы по одному.

Два типа итерируемых объектов:

- **Коллекции и последовательности** — хранят элементы (`list`, `tuple`, `str`, `set`, `dict`)
- **Итераторы** — выдают элементы по одному, не хранят всё в памяти

**💡 Ключевые моменты:**

- ✅ Любой итератор является итерируемым объектом (но не наоборот!)
- ✅ Итераторы ленивы — вычисляют значения только по требованию
- ✅ Экономят память — не хранят все элементы сразу
- ⚠️ Одноразовые — после исчерпания становятся пустыми
- ⚠️ Нет доступа по индексу и нет `len()`

---

## 💻 Примеры кода

### Раздел 1: Итерируемый объект (Iterable)

**Объект, который можно итерировать** — проходиться по нему, перебирая каждый элемент.

```python
numbers = [1, 2, 3]      # список - итерируемый
name = 'python'          # строка - итерируемая

for num in numbers:      # итерируем по списку
    print(num)

for char in name:        # итерируем по строке
    print(char)

print(2 in numbers)      # неявное итерирование
print(*numbers)          # распаковка
```

### Раздел 2: Итераторы (Iterator)

**Итератор** — специальный объект, который выдаёт свои элементы по одному за раз.

```python
numbers = [1, 2, 3, 4]

# Создаём итератор из списка
iterator = iter(numbers)

print(next(iterator))  # 1
print(next(iterator))  # 2
print(next(iterator))  # 3
print(next(iterator))  # 4
# print(next(iterator))  # StopIteration!
```

### Раздел 3: Встроенная функция next()

```python
nums = iter([1, 2, 3])

# С обработкой исключения
try:
    print(next(nums))  # 1
    print(next(nums))  # 2
    print(next(nums))  # 3
    print(next(nums))  # StopIteration
except StopIteration:
    print("Элементы закончились")

# Со значением по умолчанию
nums = iter([1, 2, 3])
print(next(nums, -1))  # 1
print(next(nums, -1))  # 2
print(next(nums, -1))  # 3
print(next(nums, -1))  # -1 (вместо StopIteration)
```

### Раздел 4: Коллекции и последовательности

**Коллекция** — объект, хранящий набор значений одного или различных типов.

**Последовательность** — коллекция с пронумерованными элементами в строгом порядке.

|Тип|Коллекция|Последовательность|
|---|---|---|
|`list`|✅|✅|
|`tuple`|✅|✅|
|`str`|✅|✅|
|`set`|✅|❌|
|`dict`|✅|❌|

```python
# Последовательность - можно обращаться по индексу
numbers = [1, 2, 3, 4]
print(numbers[2])  # 3 ✅

# Итератор - нельзя обращаться по индексу
iterator = iter(numbers)
# print(iterator[1])  # TypeError ❌
```

### Раздел 5: Несколько независимых итераторов

```python
numbers = [1, 2, 3, 4]

iterator1 = iter(numbers)
iterator2 = iter(numbers)
iterator3 = iter(numbers)

print(next(iterator1))  # 1
print(next(iterator1))  # 2

print(next(iterator2))  # 1

print(next(iterator3))  # 1
print(next(iterator3))  # 2

# Каждый итератор независим!
```

### Раздел 6: Преимущества итераторов

#### Однотипность работы

Разные типы работают одинаково через итераторы:

```python
for item in [1, 2, 3]:          # список
    print(item)

for item in (1, 2, 3):          # кортеж
    print(item)

for item in 'abc':              # строка
    print(item)

for item in {1, 2, 3}:          # множество
    print(item)

for item in range(3):           # range
    print(item)
```

#### Как работает цикл for

```python
# Этот код:
for num in numbers:
    print(num)

# За кулисами превращается в:
iterator = iter(numbers)
while True:
    try:
        num = next(iterator)
        print(num)
    except StopIteration:
        break
```

#### Ленивые вычисления и экономия памяти

**Ленивые вычисления** — не делать ничего до тех пор, пока это не нужно.

```python
from sys import getsizeof

# range не хранит все числа
numbers1 = range(5)
numbers2 = range(100000)
numbers3 = range(10000000000000)

print(getsizeof(numbers1))  # 48 байт
print(getsizeof(numbers2))  # 48 байт
print(getsizeof(numbers3))  # 48 байт (!)

# Список хранит ВСЕ числа
list1 = list(range(5))
list2 = list(range(100000))

print(getsizeof(list1))     # 96 байт
print(getsizeof(list2))     # 800056 байт

# list(range(10000000000000))  # MemoryError!
```

#### Комбинация итераторов

```python
sentence = 'In the face of ambiguity refuse the temptation to guess'

# Фильтруем → Преобразовываем → Нумеруем → Выводим
filter_it = filter(lambda w: len(w) > 4, sentence.split())
map_it = map(lambda w: w.upper(), filter_it)
enum_it = enumerate(map_it, 1)

for index, value in enum_it:
    print(f'{index}. {value}')

# 1. AMBIGUITY
# 2. REFUSE
# 3. TEMPTATION
# 4. GUESS

# Все промежуточные объекты — итераторы, не занимают память!
```

### Раздел 7: Работа с итераторами

#### Цикл for

```python
numbers = [-3, 6, 1, -90, 34]
positive = map(abs, numbers)

for num in positive:
    print(num)
# 3, 6, 1, 90, 34

# Второй цикл ничего не выведет
for num in positive:
    print(num)  # Итератор пуст!
```

#### Преобразование в коллекцию

```python
numbers = [-3, 6, 1, -90, 34]
positive = map(abs, numbers)

list1 = list(positive)  # [3, 6, 1, 90, 34]
list2 = list(positive)  # [] (итератор опустошён!)

print(list1)  # [3, 6, 1, 90, 34]
print(list2)  # []
```

#### Оператор принадлежности in

```python
numbers = [4, 8, 15, 16, 23, 42]
iterator = iter(numbers)

print(15 in iterator)  # True
print(15 in iterator)  # False (уже прошли 15!)

# Новый итератор
iterator = iter(numbers)
print(15 in iterator)  # True
print(23 in iterator)  # True (осталось: 42)

# Поиск опустошает итератор до найденного элемента!
```

#### Распаковка

```python
numbers = [4, 8, 15, 16, 23, 42]
iterator = iter(numbers)

print(*iterator)       # 4 8 15 16 23 42
print(list(iterator))  # [] (опустошён)
```

### Раздел 8: Встроенные функции-итераторы

#### map() - Применение функции

```python
from sys import getsizeof

numbers = [1, 2, 3, 4, 5]
squares = map(lambda x: x ** 2, numbers)

print(type(squares))         # <class 'map'>
print(getsizeof(squares))    # 48 байт
print(list(squares))         # [1, 4, 9, 16, 25]
```

#### filter() - Фильтрация

```python
numbers = [45, -90, 4, 89, -21, 43]
positive = filter(lambda x: x > 0, numbers)

print(type(positive))     # <class 'filter'>
print(list(positive))     # [45, 4, 89, 43]

# filter(None, ...) отбирает истинные значения
objects = ('a', None, 45, False, 0, '')
truthy = filter(None, objects)
print(list(truthy))       # ('a', 45)
```

#### enumerate() - Нумерация

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']

numbered = enumerate(seasons)
print(list(numbered))
# [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]

# С начальным значением
numbered = enumerate(seasons, start=1)
print(list(numbered))
# [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

#### zip() - Объединение

```python
languages = ['Python', 'C#', 'Java']
years = [1991, 2000, 1995]

combined = zip(languages, years)
print(list(combined))
# [('Python', 1991), ('C#', 2000), ('Java', 1995)]

# Несколько итераторов
authors = ['Guido', 'Anders', 'James']
combined = zip(languages, years, authors)
print(list(combined))
# [('Python', 1991, 'Guido'), ('C#', 2000, 'Anders'), ('Java', 1995, 'James')]

# Останавливается на самом коротком
numbers = [1, 2, 3, 4, 5]
letters = ['a', 'b', 'c']
print(list(zip(numbers, letters)))
# [(1, 'a'), (2, 'b'), (3, 'c')]

# Python 3.10+: strict=True проверяет одинаковую длину
# zip(numbers, letters, strict=True)  # ValueError если длины разные!
```

#### reversed() - Обратный порядок

```python
years = [1991, 2000, 1972, 1986]
letters = 'python'

backward_years = reversed(years)
backward_letters = reversed(letters)

print(list(backward_years))    # [1986, 1972, 2000, 1991]
print(list(backward_letters))  # ['n', 'o', 'h', 't', 'y', 'p']

# ⚠️ Работает только с последовательностями!
```

### Раздел 9: Магические методы (Dunders)

**Магические методы** (dunders = double underscore) — методы с двойным подчёркиванием, которые вызываются автоматически.

```python
words = ['hello', 'world']

# Внешний вызов → магический метод
print(len(words))      # words.__len__()
print(str(words))      # words.__str__()

# Можно вызвать напрямую (но не нужно)
print(words.__len__())  # 2
print(words.__str__())  # ['hello', 'world']
```

#### Просмотр всех методов

```python
words = ['hello', 'world']
print(dir(words))
# ['__add__', '__class__', ..., '__iter__', '__len__', ...,
#  'append', 'clear', 'copy', 'count', ...]
```

### Раздел 10: Протокол итератора

#### Магические методы итерируемых объектов

**`__iter__()`** — создаёт итератор на основе объекта

```python
words = ['hello', 'world']

# Внешний вызов
iterator = iter(words)

# За кулисами
iterator = words.__iter__()

print(type(words))     # <class 'list'>
print(type(iterator))  # <class 'list_iterator'>
```

#### Магические методы итераторов

**`__next__()`** — возвращает следующий элемент

```python
words = ['hello', 'world']
iterator = iter(words)

# Внешний вызов
print(next(iterator))  # hello

# За кулисами
print(iterator.__next__())  # world
```

**`__iter__()`** — возвращает сам себя

```python
iterator1 = iter(words)
iterator2 = iter(iterator1)  # Возвращает сам себя

print(iterator1 == iterator2)  # True
```

#### Формальный протокол

**Чтобы объект был итератором:**

1. ✅ Должен иметь метод `__next__()`
2. ✅ Должен иметь метод `__iter__()` (возвращает self)
3. ✅ `__next__()` возбуждает `StopIteration` при исчерпании

**Чтобы объект был итерируемым:**

1. ✅ Должен иметь метод `__iter__()` (возвращает итератор)

**Вывод:** Любой итератор — итерируемый объект, но не наоборот!

### Раздел 11: Создание собственных итераторов

#### Базовая структура

```python
class MyIterator:
    def __init__(self):
        pass

    def __iter__(self):
        return self  # возвращает сам себя

    def __next__(self):
        # возвращает следующий элемент
        # или возбуждает StopIteration
        pass
```

#### Пример 1: Counter - Диапазон чисел

```python
class Counter:
    def __init__(self, low, high):
        self.current = low
        self.high = high

    def __iter__(self):
        return self

    def __next__(self):
        if self.current > self.high:
            raise StopIteration
        else:
            self.current += 1
            return self.current - 1

# Использование
counter = Counter(3, 10)
for i in counter:
    print(i)
# 3, 4, 5, 6, 7, 8, 9, 10

# Явный вызов
counter2 = Counter(100, 102)
print(next(counter2))  # 100
print(next(counter2))  # 101
```

#### Пример 2: EvenNumbers - Бесконечный итератор

```python
class EvenNumbers:
    def __init__(self, begin):
        # Начинаем с ближайшего чётного
        self.current = begin + begin % 2

    def __iter__(self):
        return self

    def __next__(self):
        value = self.current
        self.current += 2
        return value

# Использование с ограничением
evens = EvenNumbers(10)
for index, num in enumerate(evens):
    if index > 5:
        break
    print(num)
# 10, 12, 14, 16, 18, 20

# ⚠️ Без ограничения - бесконечный цикл!
# for num in EvenNumbers(2):
#     print(num)  # 2, 4, 6, 8, ... ∞
```

#### Пример 3: StringWrapper - Обрамление символов

```python
class StringWrapper:
    def __init__(self, text, symbol):
        self.text = text
        self.symbol = symbol
        self.index = -1

    def __iter__(self):
        return self

    def __next__(self):
        self.index += 1
        if self.index >= len(self.text):
            raise StopIteration
        return f'{self.symbol}{self.text[self.index]}{self.symbol}'

# Использование
wrapper = StringWrapper('beegeek', '~')
for char in wrapper:
    print(char)
# ~b~, ~e~, ~e~, ~g~, ~e~, ~e~, ~k~

print(list(StringWrapper('hi', '+')))
# ['+h+', '+i+']
```

#### Пример 4: Factorials - Бесконечные факториалы

```python
class Factorials:
    def __init__(self):
        self.value = 1
        self.index = 1

    def __iter__(self):
        return self

    def __next__(self):
        self.value *= self.index
        self.index += 1
        return self.value

# Использование
factorials = Factorials()
for i, fact in enumerate(factorials, 1):
    if i > 5:
        break
    print(f'{i}! = {fact}')
# 1! = 1
# 2! = 2
# 3! = 6
# 4! = 24
# 5! = 120
```

### Раздел 12: Особенность функции iter()

#### Стандартное использование

```python
iter(iterable) -> iterator
```

#### Продвинутое использование

```python
iter(callable, sentinel) -> iterator
```

- `callable` — вызываемый объект (функция без аргументов)
- `sentinel` — стоп-значение (когда остановиться)

**🔍 Как это работает:**

1. Итератор вызывает `callable()`
2. Сравнивает результат с `sentinel`
3. Если результат == `sentinel` → возбуждает `StopIteration`
4. Иначе → возвращает результат

```python
# Бесконечная генерация чисел в промежутке
import random

def random_numbers(left, right):
    return iter(lambda: random.randrange(left, right + 1), None)

iterator = random_numbers(1, 10)
print(next(iterator))
print(next(iterator))

# Бесконечный итератор нулей
zero_iterator = iter(int, -1)
for i in range(5):
    print(next(zero_iterator))
# 0, 0, 0, 0, 0

# Случайные числа до 2
from random import choice

def random_num():
    return choice(range(1, 11))

rand_it = iter(random_num, 2)
for num in rand_it:
    print(num)
# 7, 9, 4, 3, 1, 8  (остановится при 2)

# Чтение файла до пустой строки
with open('data.txt') as file:
    for line in iter(file.readline, ''):
        print(line.strip())
```

### Частые ошибки

```python
# ❌ Итераторы одноразовые
numbers = map(abs, [-1, -2, -3])
print(list(numbers))  # [1, 2, 3]
print(list(numbers))  # [] — опустошён!

# ⚠️ max() и min() опустошают итератор
numbers = [1, 2, 3, 4, 5]
squares = map(lambda x: x ** 2, numbers)
print(max(squares))    # 25
print(list(squares))   # [] (опустошён)

# ⚠️ sorted() возвращает список, не итератор
numbers = [3, 1, 4, 1, 5]
sorted_nums = sorted(numbers)
print(type(sorted_nums))  # <class 'list'> (не итератор!)

# ⚠️ Изменение коллекции во время итерации
numbers = [10, 20, 30, 40, 50]
iterator = iter(numbers)
print(next(iterator))  # 10
print(next(iterator))  # 20
del numbers[2]         # Удаляем 30
print(next(iterator))  # 40 (не 30!)

# ⚠️ Словари возвращают только ключи
people = {'name': 'Timur', 'age': 29}
for key in people:
    print(key)
# name, age (только ключи!)

# ❌ len() не работает с итераторами
numbers = [1, 2, 3, 4, 5, 6]
evens = filter(lambda x: x % 2 == 0, numbers)
# print(len(evens))  # TypeError!

# ✅ Но работает с range
print(len(range(1000000000)))  # 1000000000 (мгновенно!)
```

---

### Сравнение: Список vs Итератор

|Особенность|Список|Итератор|
|---|---|---|
|Доступ по индексу|✅ `list[0]`|❌ TypeError|
|Получение длины|✅ `len(list)`|❌ TypeError|
|Повторная итерация|✅ Можно|❌ Одноразовый|
|Память|❌ Хранит все|✅ Ленивый|
|Скорость создания|❌ Медленнее|✅ Быстрее|
|Модификация|✅ Можно|❌ Нельзя|

---

### Итоги: когда использовать итераторы

✅ **Используй:**

- Для больших данных (экономия памяти)
- При последовательной обработке
- Для конвейерной обработки (`map` → `filter` → `reduce`)
- Когда данные генерируются "на лету"

❌ **Не используй:**

- Когда нужен доступ по индексу
- Когда нужна повторная итерация
- Когда нужна длина заранее
- Для небольших данных (список проще)

---

## 🔗 Связанные темы

- [[02 — ⚡ Генераторы (Generator)]]
- [[Итератор_vs_Генератор]]
- [[Итераторы_и_коллекции]]
- [[00 — 🔄 Модуль itertools - Полная памятка]]
- [[05 — 🔄 Циклы for и while]]

## ❓ Вопросы / Непонятное