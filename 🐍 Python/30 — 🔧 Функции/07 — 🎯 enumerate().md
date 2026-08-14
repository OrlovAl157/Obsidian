---
tags: [python, функции, enumerate, встроенные]
difficulty: beginner
---

# 🎯 enumerate() — памятка

> `enumerate()` оборачивает итерируемый объект и возвращает пары `(индекс, элемент)`. Избавляет от ручного счётчика `i = 0` / `i += 1` в цикле.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Базовое использование|Базовое использование]]
- [[#🔵 Параметр start|Параметр start]]
- [[#🔴 Распаковка и форматирование|Распаковка и форматирование]]
- [[#🟡 enumerate с условиями|С условиями]]
- [[#🟣 enumerate и zip вместе|enumerate и zip]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Параметр | Тип | По умолчанию | Описание |
|---|---|---|---|
| `iterable` | любой итерируемый | обязателен | список, строка, кортеж, файл... |
| `start` | `int` | `0` | начальное значение счётчика |

| Возвращает | Тип |
|---|---|
| Объект `enumerate` | ленивый итератор пар `(int, element)` |

---

## 📊 Общая схема

```
enumerate(iterable, start=0)
              │         │
              │         └── с какого числа начать счётчик
              └── любой итерируемый объект

Результат — итератор пар:
['a', 'b', 'c']  →  (0, 'a'), (1, 'b'), (2, 'c')
['a', 'b', 'c']  →  (1, 'a'), (2, 'b'), (3, 'c')  ← start=1

Без enumerate:              С enumerate:
i = 0                       for i, item in enumerate(items):
for item in items:              print(i, item)
    print(i, item)
    i += 1
```

---

## 🟢 Базовое использование

```python
fruits = ['apple', 'banana', 'cherry']

# Без enumerate — ручной счётчик
i = 0
for fruit in fruits:
    print(i, fruit)
    i += 1

# С enumerate — чисто и безопасно
for i, fruit in enumerate(fruits):
    print(i, fruit)

# Вывод:
# 0 apple
# 1 banana
# 2 cherry

# Работает с любым итерируемым
for i, char in enumerate('hello'):
    print(i, char)
# 0 h
# 1 e
# 2 l
# 3 l
# 4 o

for i, item in enumerate((10, 20, 30)):   # кортеж
    print(i, item)
# 0 10
# 1 20
# 2 30

# enumerate возвращает ленивый итератор
e = enumerate(['a', 'b', 'c'])
print(type(e))        # <class 'enumerate'>
print(list(e))        # [(0, 'a'), (1, 'b'), (2, 'c')]
```

---

## 🔵 Параметр start

```python
fruits = ['apple', 'banana', 'cherry']

# Нумерация с 1 (удобно для пользователя)
for i, fruit in enumerate(fruits, start=1):
    print(f'{i}. {fruit}')
# 1. apple
# 2. banana
# 3. cherry

# Нумерация с произвольного числа
for i, fruit in enumerate(fruits, start=10):
    print(i, fruit)
# 10 apple
# 11 banana
# 12 cherry

# Отрицательный start
for i, fruit in enumerate(fruits, start=-2):
    print(i, fruit)
# -2 apple
# -1 banana
#  0 cherry
```

---

## 🔴 Распаковка и форматирование

```python
students = ['Alice', 'Bob', 'Charlie', 'Diana']

# Распаковка в переменные
for index, name in enumerate(students, start=1):
    print(f'Студент #{index}: {name}')
# Студент #1: Alice
# Студент #2: Bob

# Без распаковки — кортеж
for pair in enumerate(students):
    print(pair)
# (0, 'Alice')
# (1, 'Bob')

# Доступ через индексы кортежа
for pair in enumerate(students):
    print(pair[0], pair[1])

# Создание словаря из enumerate
d = {i: name for i, name in enumerate(students, start=1)}
print(d)
# {1: 'Alice', 2: 'Bob', 3: 'Charlie', 4: 'Diana'}

# Создание перевёрнутого словаря
d = {name: i for i, name in enumerate(students, start=1)}
print(d)
# {'Alice': 1, 'Bob': 2, 'Charlie': 3, 'Diana': 4}
```

---

## 🟡 enumerate с условиями

```python
numbers = [10, 3, 7, 1, 8, 5]

# Найти индексы элементов удовлетворяющих условию
indices = [i for i, x in enumerate(numbers) if x > 5]
print(indices)   # [0, 2, 4]

# Найти первый элемент удовлетворяющий условию
first_big = next((i, x) for i, x in enumerate(numbers) if x > 5)
print(first_big)  # (0, 10)

# Заменить элемент по условию (зная индекс)
items = ['cat', 'DOG', 'bird', 'FISH']
for i, item in enumerate(items):
    if item.isupper():
        items[i] = item.lower()

print(items)  # ['cat', 'dog', 'bird', 'fish']

# Найти все чётные элементы с их позицией
evens = [(i, x) for i, x in enumerate(numbers) if x % 2 == 0]
print(evens)  # [(0, 10), (4, 8)]
```

---

## 🟣 enumerate и zip вместе

```python
names = ['Alice', 'Bob', 'Charlie']
scores = [95, 82, 78]

# enumerate + zip — нумерация пар
for i, (name, score) in enumerate(zip(names, scores), start=1):
    print(f'{i}. {name}: {score}')
# 1. Alice: 95
# 2. Bob: 82
# 3. Charlie: 78

# Найти индекс максимального элемента
values = [3, 7, 2, 9, 1]
max_idx, max_val = max(enumerate(values), key=lambda x: x[1])
print(max_idx, max_val)   # 3 9

# Найти индекс минимального
min_idx, min_val = min(enumerate(values), key=lambda x: x[1])
print(min_idx, min_val)   # 4 1
```

---

## ⚡ Быстрые примеры

```python
items = ['a', 'b', 'c', 'd']

# Базовый
list(enumerate(items))             # [(0,'a'), (1,'b'), (2,'c'), (3,'d')]

# С start=1
list(enumerate(items, 1))          # [(1,'a'), (2,'b'), (3,'c'), (4,'d')]

# В цикле
for i, v in enumerate(items): ...

# Словарь индекс → значение
{i: v for i, v in enumerate(items)}  # {0:'a', 1:'b', 2:'c', 3:'d'}

# Словарь значение → индекс
{v: i for i, v in enumerate(items)}  # {'a':0, 'b':1, 'c':2, 'd':3}

# Только чётные индексы
[v for i, v in enumerate(items) if i % 2 == 0]   # ['a', 'c']

# Только нечётные
[v for i, v in enumerate(items) if i % 2 != 0]   # ['b', 'd']

# Максимальный элемент и его индекс
max(enumerate(items), key=lambda x: x[1])

# Изменить элемент по индексу
for i, v in enumerate(items):
    if v == 'b':
        items[i] = 'B'

# Пронумерованный вывод
for i, v in enumerate(items, 1):
    print(f'{i}. {v}')
```

---

## 💡 Практические замечания

- `enumerate` — ленивый итератор, не создаёт список в памяти
- `start=1` — самое частое использование для пользовательских номеров
- Лучший способ изменить элемент в цикле — получить индекс через `enumerate`, а не `list.index()`
- Работает с любым итерируемым: список, кортеж, строка, генератор, файл, `range`
- Можно передавать в `list()`, `tuple()`, `dict()` напрямую

---

## ⚠️ Частые ошибки

**❌ Ручной счётчик вместо enumerate:**
```python
i = 0
for item in items:       # ❌ можно забыть i += 1, или сломать при continue
    print(i, item)
    i += 1

for i, item in enumerate(items):  # ✅ счётчик всегда синхронен
    print(i, item)
```

**❌ list.index() для поиска индекса в цикле:**
```python
for item in items:
    if item == 'b':
        print(items.index(item))  # ❌ O(n) поиск, не найдёт дубликаты правильно

for i, item in enumerate(items):
    if item == 'b':
        print(i)                  # ✅ O(1), всегда верный индекс
```

**❌ Забыл распаковать:**
```python
for pair in enumerate(items):
    print(pair + 1)    # ❌ TypeError — pair это кортеж (0, 'a')

for i, item in enumerate(items):  # ✅ распаковать сразу
    print(i + 1, item)
```

**❌ Изменение размера списка в цикле:**
```python
for i, item in enumerate(items):
    if item == 'b':
        items.remove(item)   # ❌ пропускает элементы!
# Лучше собрать новый список через list comprehension
```

---

## ✅ Главные правила

✅ `enumerate(iterable, start=0)` — счётчик + элемент  
✅ `start=1` — когда нужна нумерация с единицы для пользователя  
✅ Всегда распаковывай: `for i, item in enumerate(...)`  
✅ Для изменения элементов в цикле — используй индекс из `enumerate`  
✅ `enumerate` ленивый — не создаёт список в памяти  
✅ Работает с любым итерируемым объектом  
✅ `max(enumerate(x), key=lambda p: p[1])` — найти индекс максимума  

---

## 🔗 Связанные темы

- [[03 — 🎯 zip()]]
- [[00 — 🎯 map()]]
- [[01 — 🎯 filter()]]
- [[00 — 🎯 Встроенные функции Python]]

---

#python/функции #enumerate #встроенные
