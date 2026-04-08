---
tags:
  - python
  - тема/функции
  - тема/встроенные_функции
  - статус/завершён
уровень: начальный
возвращает_итератор: true
ленивые_вычисления: true
принимает_key: false
встроенная: true
---

# 🔗 Функция zip() - Полная памятка

## 💡 Что это?

**zip()** — встроенная функция Python, которая объединяет элементы нескольких итерируемых объектов в кортежи.

```python
zip(*iterables, strict=False)
```

**Возвращает:** итератор, содержащий кортежи

---

## 🎯 Базовое использование

### Простой пример

```python
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']

result = zip(numbers, letters)

print(type(result))  # <class 'zip'>
print(list(result))  # [(1, 'a'), (2, 'b'), (3, 'c')]
```

### Визуализация

```
numbers: [1,    2,    3   ]
letters: ['a',  'b',  'c' ]
         ↓      ↓     ↓
result:  (1,'a')(2,'b')(3,'c')
```

---

## 📝 Основы работы

### С двумя последовательностями

```python
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]

combined = zip(names, ages)

for name, age in combined:
    print(f'{name} - {age} лет')

# Alice - 25 лет
# Bob - 30 лет
# Charlie - 35 лет
```

### С тремя и более

```python
languages = ['Python', 'Java', 'C++']
years = [1991, 1995, 1985]
authors = ['Guido', 'Gosling', 'Stroustrup']

result = zip(languages, years, authors)

for lang, year, author in result:
    print(f'{lang} ({year}) - {author}')

# Python (1991) - Guido
# Java (1995) - Gosling
# C++ (1985) - Stroustrup
```

### С одним аргументом

```python
numbers = [1, 2, 3]
result = zip(numbers)

print(list(result))
# [(1,), (2,), (3,)]  ← Кортежи с одним элементом!
```

### Без аргументов

```python
result = zip()
print(list(result))  # []  ← Пустой итератор
```

---

## 🔍 Особенности работы

### 1. Останавливается на самом коротком

```python
numbers = [1, 2, 3, 4, 5]
letters = ['a', 'b', 'c']  # Короче!

result = zip(numbers, letters)
print(list(result))
# [(1, 'a'), (2, 'b'), (3, 'c')]
# 4 и 5 игнорируются!
```

### 2. Возвращает итератор (ленивый)

```python
from sys import getsizeof

numbers = range(1000000)
letters = 'abcdefgh' * 125000

zipped = zip(numbers, letters)

print(getsizeof(zipped))  # 64 байта (в Python 3.10)
# Не хранит все данные в памяти!
```

### 3. Одноразовый (как все итераторы)

```python
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']

zipped = zip(numbers, letters)

print(list(zipped))  # [(1, 'a'), (2, 'b'), (3, 'c')]
print(list(zipped))  # [] ← Пуст!
```

---

## 🆕 Параметр strict (Python 3.10+)

### Что это?

**strict=True** — проверяет, что все итерируемые объекты имеют одинаковую длину.

### Пример

```python
numbers = [1, 2, 3, 4]
letters = ['a', 'b', 'c']  # Разная длина!

# strict=False (по умолчанию)
result = zip(numbers, letters)
print(list(result))  # [(1, 'a'), (2, 'b'), (3, 'c')]

# strict=True
result = zip(numbers, letters, strict=True)
# ValueError: zip() argument 2 is shorter than argument 1
```

### Когда использовать?

```python
# Когда длины ДОЛЖНЫ совпадать
scores = [85, 90, 78]
names = ['Alice', 'Bob', 'Charlie']

# Защита от ошибок
for name, score in zip(names, scores, strict=True):
    print(f'{name}: {score}')
```

---

## 🎨 Практические примеры

### 1. Создание словаря

```python
keys = ['name', 'age', 'city']
values = ['Alice', 25, 'Moscow']

person = dict(zip(keys, values))
print(person)
# {'name': 'Alice', 'age': 25, 'city': 'Moscow'}
```

### 2. Параллельная итерация

```python
questions = ['Имя?', 'Возраст?', 'Город?']
answers = ['Bob', '30', 'SPb']

for q, a in zip(questions, answers):
    print(f'{q} {a}')

# Имя? Bob
# Возраст? 30
# Город? SPb
```

### 3. Транспонирование матрицы

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

transposed = list(zip(*matrix))
print(transposed)
# [(1, 4, 7), (2, 5, 8), (3, 6, 9)]

# Обратно в списки
transposed = [list(row) for row in zip(*matrix)]
print(transposed)
# [[1, 4, 7], [2, 5, 8], [3, 6, 9]]
```

### 4. Группировка данных

```python
names = ['Alice', 'Bob', 'Charlie']
math = [85, 90, 78]
physics = [88, 85, 92]

for name, m, p in zip(names, math, physics):
    avg = (m + p) / 2
    print(f'{name}: средний балл = {avg}')

# Alice: средний балл = 86.5
# Bob: средний балл = 87.5
# Charlie: средний балл = 85.0
```

### 5. Сравнение последовательностей

```python
list1 = [1, 2, 3, 4]
list2 = [1, 2, 5, 4]

for i, (a, b) in enumerate(zip(list1, list2), 1):
    if a != b:
        print(f'Отличие в позиции {i}: {a} != {b}')

# Отличие в позиции 3: 3 != 5
```

### 6. Нумерация с enumerate

```python
words = ['apple', 'banana', 'cherry']
prices = [100, 50, 75]

for i, (word, price) in enumerate(zip(words, prices), 1):
    print(f'{i}. {word}: {price}₽')

# 1. apple: 100₽
# 2. banana: 50₽
# 3. cherry: 75₽
```

### 7. Обработка CSV-подобных данных

```python
headers = ['Name', 'Age', 'City']
row1 = ['Alice', '25', 'Moscow']
row2 = ['Bob', '30', 'SPb']

for row in [row1, row2]:
    person = dict(zip(headers, row))
    print(person)

# {'Name': 'Alice', 'Age': '25', 'City': 'Moscow'}
# {'Name': 'Bob', 'Age': '30', 'City': 'SPb'}
```

---

## 🔄 Распаковка zip (unzip)

### Проблема

Как "разархивировать" результат zip обратно?

```python
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]

# Как получить обратно [1, 2, 3] и ['a', 'b', 'c']?
```

### Решение: zip(*pairs)

```python
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]

numbers, letters = zip(*pairs)

print(numbers)  # (1, 2, 3)
print(letters)  # ('a', 'b', 'c')
```

### Как это работает?

```python
# Распаковка * превращает
pairs = [(1, 'a'), (2, 'b'), (3, 'c')]

# В отдельные аргументы:
zip((1, 'a'), (2, 'b'), (3, 'c'))

# zip берёт первые элементы: (1, 2, 3)
# и вторые элементы: ('a', 'b', 'c')
```

### Пример с данными

```python
data = [
    ('Alice', 25, 'Moscow'),
    ('Bob', 30, 'SPb'),
    ('Charlie', 35, 'Kazan')
]

names, ages, cities = zip(*data)

print(names)   # ('Alice', 'Bob', 'Charlie')
print(ages)    # (25, 30, 35)
print(cities)  # ('Moscow', 'SPb', 'Kazan')

# Средний возраст
print(f'Средний возраст: {sum(ages) / len(ages)}')
# Средний возраст: 30.0
```

---

## ⚡ Продвинутые техники

### 1. Zip с enumerate

```python
names = ['Alice', 'Bob', 'Charlie']
scores = [85, 90, 78]

for i, (name, score) in enumerate(zip(names, scores), 1):
    print(f'{i}. {name}: {score}')

# 1. Alice: 85
# 2. Bob: 90
# 3. Charlie: 78
```

### 2. Zip в генераторах

```python
numbers = [1, 2, 3, 4, 5]
letters = ['a', 'b', 'c', 'd', 'e']

# Генератор словаря
result = {num: letter for num, letter in zip(numbers, letters)}
print(result)
# {1: 'a', 2: 'b', 3: 'c', 4: 'd', 5: 'e'}

# Генераторное выражение
squares = (x**2 for x, _ in zip(numbers, letters))
print(list(squares))
# [1, 4, 9, 16, 25]
```

### 3. Zip с функциями высшего порядка

```python
list1 = [1, 2, 3, 4]
list2 = [10, 20, 30, 40]

# map + zip для поэлементного сложения
sums = list(map(lambda pair: pair[0] + pair[1], zip(list1, list2)))
print(sums)  # [11, 22, 33, 44]

# Или проще с sum
sums = [sum(pair) for pair in zip(list1, list2)]
print(sums)  # [11, 22, 33, 44]

# Или еще проще
sums = [a + b for a, b in zip(list1, list2)]
print(sums)  # [11, 22, 33, 44]
```

### 4. Zip для сравнения

```python
def are_equal(list1, list2):
    """Проверяет равенство двух списков"""
    return all(a == b for a, b in zip(list1, list2))

print(are_equal([1, 2, 3], [1, 2, 3]))  # True
print(are_equal([1, 2, 3], [1, 2, 4]))  # False
```

### 5. Zip с бесконечными итераторами

```python
from itertools import count

names = ['Alice', 'Bob', 'Charlie']

# count() - бесконечный итератор
for id, name in zip(count(1), names):
    print(f'ID {id}: {name}')

# ID 1: Alice
# ID 2: Bob
# ID 3: Charlie
# (останавливается на names, т.к. он короче)
```

---

## 🔧 Альтернативы zip

### itertools.zip_longest

**Проблема:** zip останавливается на самом коротком

**Решение:** `zip_longest` заполняет пропуски

```python
from itertools import zip_longest

numbers = [1, 2, 3, 4, 5]
letters = ['a', 'b', 'c']

# Обычный zip (короткий)
result = zip(numbers, letters)
print(list(result))
# [(1, 'a'), (2, 'b'), (3, 'c')]

# zip_longest (длинный)
result = zip_longest(numbers, letters, fillvalue='?')
print(list(result))
# [(1, 'a'), (2, 'b'), (3, 'c'), (4, '?'), (5, '?')]
```

### Ручная реализация zip

```python
def my_zip(*iterables):
    """Упрощённая реализация zip"""
    iterators = [iter(it) for it in iterables]
    while True:
        result = []
        for it in iterators:
            try:
                result.append(next(it))
            except StopIteration:
                return
        yield tuple(result)

# Использование
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']
print(list(my_zip(numbers, letters)))
# [(1, 'a'), (2, 'b'), (3, 'c')]
```

---

## 📊 Сравнительная таблица

|Характеристика|zip()|zip_longest()|
|---|---|---|
|Длина результата|Самый короткий|Самый длинный|
|Заполнение пропусков|Нет|fillvalue|
|Импорт|Встроенная|itertools|
|Использование|Частое|Редкое|

---

## ⚠️ Частые ошибки

### 1. Забыли list()

```python
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']

result = zip(numbers, letters)

# ❌ ОШИБКА
print(result)  # <zip object at 0x...>

# ✅ ПРАВИЛЬНО
print(list(result))  # [(1, 'a'), (2, 'b'), (3, 'c')]
```

### 2. Повторное использование

```python
numbers = [1, 2, 3]
letters = ['a', 'b', 'c']

zipped = zip(numbers, letters)

# ❌ ОШИБКА - второй раз пустой
print(list(zipped))  # [(1, 'a'), (2, 'b'), (3, 'c')]
print(list(zipped))  # []

# ✅ РЕШЕНИЕ - создать заново
zipped1 = zip(numbers, letters)
zipped2 = zip(numbers, letters)

print(list(zipped1))  # [(1, 'a'), (2, 'b'), (3, 'c')]
print(list(zipped2))  # [(1, 'a'), (2, 'b'), (3, 'c')]
```

### 3. Разная длина без strict

```python
scores1 = [85, 90, 78]
scores2 = [88, 92]  # Короче!

# ❌ ОШИБКА - молчаливо теряем данные
result = zip(scores1, scores2)
print(list(result))  # [(85, 88), (90, 92)]
# 78 потеряно!

# ✅ РЕШЕНИЕ - используйте strict=True
result = zip(scores1, scores2, strict=True)
# ValueError: zip() argument 2 is shorter than argument 1
```

### 4. Распаковка пустого zip

```python
pairs = []

# ❌ ОШИБКА
numbers, letters = zip(*pairs)
# ValueError: not enough values to unpack (expected 2, got 0)

# ✅ РЕШЕНИЕ - проверка
if pairs:
    numbers, letters = zip(*pairs)
else:
    numbers, letters = [], []
```

---

## 💡 Полезные паттерны

### 1. Словарь из двух списков

```python
keys = ['a', 'b', 'c']
values = [1, 2, 3]

d = dict(zip(keys, values))
print(d)  # {'a': 1, 'b': 2, 'c': 3}
```

### 2. Обновление словаря

```python
d = {'a': 1, 'b': 2}
new_keys = ['a', 'c']
new_values = [10, 30]

d.update(zip(new_keys, new_values))
print(d)  # {'a': 10, 'b': 2, 'c': 30}
```

### 3. Пары соседних элементов

```python
numbers = [1, 2, 3, 4, 5]

pairs = list(zip(numbers, numbers[1:]))
print(pairs)
# [(1, 2), (2, 3), (3, 4), (4, 5)]
```

### 4. Группировка по N элементов

```python
def grouper(iterable, n):
    """Группирует элементы по n штук"""
    args = [iter(iterable)] * n
    return zip(*args)

data = [1, 2, 3, 4, 5, 6, 7, 8]
print(list(grouper(data, 3)))
# [(1, 2, 3), (4, 5, 6)]
# 7, 8 теряются (используйте zip_longest для сохранения)
```

### 5. Суммирование нескольких списков

```python
list1 = [1, 2, 3]
list2 = [10, 20, 30]
list3 = [100, 200, 300]

sums = [sum(values) for values in zip(list1, list2, list3)]
print(sums)  # [111, 222, 333]
```

---

## 📝 Шпаргалка

### Основной синтаксис

```python
# Базовое использование
zip(iterable1, iterable2, ...)

# С проверкой длины (3.10+)
zip(iterable1, iterable2, strict=True)

# Распаковка
zip(*sequence_of_tuples)
```

### Типичные операции

```python
# В список
list(zip(a, b))

# В словарь
dict(zip(keys, values))

# В цикле
for x, y in zip(list1, list2):
    print(x, y)

# Распаковка
a, b = zip(*pairs)

# Транспонирование
transposed = list(zip(*matrix))
```

### Что запомнить

✅ Возвращает итератор (ленивый)  
✅ Останавливается на самом коротком  
✅ Одноразовый (истощается)  
✅ Используйте strict=True для безопасности  
✅ zip(*pairs) для "разархивирования"  
✅ Отлично работает с dict(), list(), tuple()

❌ Нет len()  
❌ Нет индексации  
❌ Нельзя использовать дважды

## 🔗 Связанные темы

- [[01 — 📋 Cписки ( list ) 🔹]]
- [[02 — 🔒 Кортежи (tuple) 🔹]]
- [[00 — 🎯 map()]]
- [[00 — 🎯 Встроенные функции Python]]
