---
tags:
  - python
  - тема/модули
  - тема/itertools
  - тема/итераторы
  - статус/завершён
уровень: средний
стандартная_библиотека: true
сторонний_пакет: false
---
# 🔄 Модуль itertools - Полная памятка

## 💡 Что это?

**itertools** — встроенный модуль Python для создания эффективных итераторов.

```python
import itertools
# Или короткая форма
import itertools as it
```

**Основные преимущества:**
- ⚡ Быстрые (написаны на C)
- 💾 Экономят память (ленивые вычисления)
- 🔧 Универсальные инструменты

**Прообразы:** функции из языков функционального программирования (Clojure, Haskell, APL, SML)

---

## 📑 Категории функций

### 1️⃣ Порождающие данные
- `count()` — бесконечный счётчик
- `cycle()` — циклическое повторение
- `repeat()` — повторение значения

### 2️⃣ Преобразующие данные
- `starmap()` — map с распаковкой аргументов
- `accumulate()` — накопительные суммы

### 3️⃣ Терминирующие итераторы
- `chain()` — объединение
- `compress()` — фильтрация по маске
- `dropwhile()` / `takewhile()` — условная обработка
- `filterfalse()` — обратная фильтрация
- `groupby()` — группировка
- `islice()` — срезы
- `tee()` — копирование
- `zip_longest()` — длинный zip

### 4️⃣ Комбинаторные
- `product()` — декартово произведение
- `permutations()` — перестановки
- `combinations()` — сочетания
- `combinations_with_replacement()` — сочетания с повторениями

---

## 🔁 1. Порождающие данные

**⚠️ По умолчанию создают бесконечные итераторы!**

### count(start=0, step=1)

Возвращает итератор, генерирующий **бесконечную** последовательность чисел.

**Аргументы:**
- `start` — начало отсчёта (по умолчанию 0)
- `step` — шаг (по умолчанию 1)

```python
from itertools import count

count1 = count()
print(next(count1))  # 0
print(next(count1), next(count1), next(count1))  # 1 2 3

count2 = count(69, 10)
print(next(count2))  # 69
print(next(count2))  # 79
print(next(count2), next(count2), next(count2))  # 89 99 109
```

**Практическое применение:**
```python
# Нумерация элементов
for i in zip(count(10), ['a', 'b', 'c']):
    print(i)
# (10, 'a')
# (11, 'b')
# (12, 'c')
```

**С дробными числами:**
```python
from itertools import count
from fractions import Fraction

# С float
for index, number in enumerate(count(1.0, 0.5)):
    if index < 6:
        print(number)
    else:
        break
# 1.0, 1.5, 2.0, 2.5, 3.0, 3.5

# С Fraction
frac_iter = count(1, Fraction(1, 2))
print(next(frac_iter), next(frac_iter), next(frac_iter))
# 1 3/2 2
```

**⚠️ Внимание:** Нельзя создать список из бесконечного итератора!

**Эквивалентная реализация:**
```python
def count(start=0, step=1):
    n = start
    while True:
        yield n
        n += step
```

---

### cycle(iterable)

Возвращает итератор, **циклично** генерирующий последовательность элементов.

**Аргументы:**
- `iterable` — итерируемый объект

**⚠️ Важно:** Функция сохраняет копию ВСЕХ элементов из iterable!

```python
from itertools import cycle

# Циклическое повторение строки
for index, char in enumerate(cycle('abcd')):
    if index < 7:
        print(char)
    else:
        break
# a b c d a b c

# С списком
cycle_iter = cycle([0, 1])
print(next(cycle_iter), next(cycle_iter), next(cycle_iter))
# 0 1 0

# Комбинация с range
for i in zip(range(7), cycle(['a', 'b', 'c'])):
    print(i)
# (0, 'a')
# (1, 'b')
# (2, 'c')
# (3, 'a')
# (4, 'b')
# (5, 'c')
# (6, 'a')
```

**Практическое применение:**
```python
# Чередование цветов строк таблицы
data = ['Alice', 'Bob', 'Charlie', 'David']
for name, bg in zip(data, cycle(['white', 'gray'])):
    print(f'{name} - {bg} background')
# Alice - white background
# Bob - gray background
# Charlie - white background
# David - gray background
```

**💾 Память:** Может потребовать много памяти в зависимости от длины iterable!

**Эквивалентная реализация:**
```python
def cycle(iterable):
    saved = []
    for element in iterable:
        yield element
        saved.append(element)
    while saved:
        for element in saved:
            yield element
```

---

### repeat(obj, times=None)

Возвращает итератор, **бесконечно** (или times раз) генерирующий одно значение.

**Аргументы:**
- `obj` — любой Python-объект
- `times` — количество повторений (по умолчанию None = бесконечно)

```python
from itertools import repeat

# Ограниченное повторение
for i in repeat('bee-and-geek', 5):
    print(i)
# bee-and-geek (5 раз)

# Бесконечное повторение
repeat_iter = repeat([1, 2, 3])
print(next(repeat_iter))  # [1, 2, 3]
print(next(repeat_iter))  # [1, 2, 3]
print(next(repeat_iter))  # [1, 2, 3]
```

**✅ Ленивость:** Использует память только для хранения ОДНОГО элемента!

**Практическое применение с zip():**
```python
from itertools import count, repeat

# Объединение с count
for i, s in zip(count(), repeat('bee-and-geek', 5)):
    print(i, s)
# 0 bee-and-geek
# 1 bee-and-geek
# 2 bee-and-geek
# 3 bee-and-geek
# 4 bee-and-geek
```

**Практическое применение с map():**
```python
from itertools import repeat

# Умножение на 2
for a, b, c in map(lambda x, y: (x, y, x * y), repeat(2), range(6)):
    print(f'{a} * {b} = {c}')
# 2 * 0 = 0
# 2 * 1 = 2
# 2 * 2 = 4
# 2 * 3 = 6
# 2 * 4 = 8
# 2 * 5 = 10
```

**Почему работает:** map() останавливается, когда исчерпывается ЛЮБОЙ итерируемый объект (range(6) вернёт только 6 элементов).

**Эквивалентная реализация:**
```python
def repeat(object, times=None):
    if times is None:
        while True:
            yield object
    else:
        for i in range(times):
            yield object
```

---

## 🔄 2. Преобразующие данные

### starmap(func, iterable)

Возвращает итератор, применяющий функцию к **распакованным** элементам iterable.

**Аргументы:**
- `func` — функция
- `iterable` — итерируемый объект, элементы которого являются итерируемыми объектами

**Отличие от map():**
- `map(func, iterable)` → `func(element)`
- `starmap(func, iterable)` → `func(*element)`

```python
from itertools import starmap

persons = [('Timur', 'Guev'), ('Arthur', 'Kharisov')]
pairs = [(1, 3), (2, 5), (6, 4)]
points = [(1, 1, 1), (1, 1, 2), (2, 2, 3)]

# Объединение имени и фамилии
full_names = list(starmap(lambda name, surname: f'{name} {surname}', persons))
print(full_names)
# ['Timur Guev', 'Arthur Kharisov']

# Сложение пар чисел
print(*starmap(lambda a, b: a + b, pairs))
# 4 7 10

# Произведение трёх чисел
print(*starmap(lambda x, y, z: x * y * z, points))
# 1 2 12
```

**Сравнение с map():**
```python
pairs = [(2, 5), (3, 4), (10, 2)]

# ❌ map - передаёт кортеж целиком
# result = map(pow, pairs)  # TypeError!

# ✅ starmap - распаковывает кортеж
result = starmap(pow, pairs)  # pow(2,5), pow(3,4), pow(10,2)
print(list(result))
# [32, 81, 100]
```

**Эквивалентная реализация:**
```python
def starmap(function, iterable):
    for args in iterable:
        yield function(*args)
```

---

### accumulate(iterable, func=operator.add, *, initial=None)

Возвращает итератор с **накопленными** суммами (или результатами функции).

**Аргументы:**
- `iterable` — итерируемый объект
- `func` — функция двух аргументов (по умолчанию `operator.add`)
- `initial` — начальное значение (по умолчанию None)

**Отличие от reduce():** accumulate() генерирует ВСЕ промежуточные результаты!

```python
from itertools import accumulate
import operator

data = [3, 4, 6, 2, 1, 9, 0, 7, 5, 8]

# Накопительная сумма
print(list(accumulate(data)))
# [3, 7, 13, 15, 16, 25, 25, 32, 37, 45]
# 3, 3+4, 3+4+6, 3+4+6+2, ...

# Накопительное произведение
print(list(accumulate(data, operator.mul)))
# [3, 12, 72, 144, 144, 1296, 0, 0, 0, 0]

# Накопительный максимум
print(list(accumulate(data, max)))
# [3, 4, 6, 6, 6, 9, 9, 9, 9, 9]

# Накопительный минимум
print(list(accumulate(data, min)))
# [3, 3, 3, 2, 1, 1, 0, 0, 0, 0]
```

**С начальным значением:**
```python
from itertools import accumulate

print(list(accumulate([1, 2, 3, 4, 5], initial=100)))
# [100, 101, 103, 106, 110, 115]
# initial, 100+1, 101+2, 103+3, ...
```

**⚠️ Количество элементов:**
- Без `initial`: равно длине iterable
- С `initial`: на 1 больше длины iterable

**Эквивалентная реализация:**
```python
import operator

def accumulate(iterable, func=operator.add, *, initial=None):
    it = iter(iterable)
    total = initial
    if initial is None:
        try:
            total = next(it)
        except StopIteration:
            return
    yield total
    for element in it:
        total = func(total, element)
        yield total
```

---

## ✂️ 3. Терминирующие итераторы

### chain(*iterables)

Объединяет несколько итераторов в один.

```python
from itertools import chain

result = chain([1, 2], [3, 4], [5, 6])
print(list(result))
# [1, 2, 3, 4, 5, 6]

result = chain('ABC', [1, 2], (7, 8))
print(list(result))
# ['A', 'B', 'C', 1, 2, 7, 8]
```

**chain.from_iterable()** — для вложенных последовательностей:
```python
nested = [[1, 2], [3, 4], [5, 6]]

result = chain.from_iterable(nested)
print(list(result))
# [1, 2, 3, 4, 5, 6]
```

---

### compress(data, selectors)

Фильтрует элементы по булевой маске.

```python
from itertools import compress

data = ['A', 'B', 'C', 'D', 'E']
mask = [1, 0, 1, 0, 1]

result = compress(data, mask)
print(list(result))
# ['A', 'C', 'E']
```

---

### dropwhile(predicate, iterable)

Пропускает элементы, пока условие истинно.

```python
from itertools import dropwhile

numbers = [1, 3, 5, 8, 2, 10, 4]

result = dropwhile(lambda x: x % 2 == 1, numbers)
print(list(result))
# [8, 2, 10, 4]
```

---

### takewhile(predicate, iterable)

Берёт элементы, пока условие истинно.

```python
from itertools import takewhile

numbers = [1, 3, 5, 8, 2, 10, 4]

result = takewhile(lambda x: x % 2 == 1, numbers)
print(list(result))
# [1, 3, 5]
```

---

### filterfalse(predicate, iterable)

Противоположность filter() — возвращает элементы, где условие ложно.

```python
from itertools import filterfalse

numbers = [1, 2, 3, 4, 5, 6, 7, 8]

odds = filterfalse(lambda x: x % 2 == 0, numbers)
print(list(odds))
# [1, 3, 5, 7]
```

---

### islice(iterable, stop) / islice(iterable, start, stop, step)

Срезы для итераторов.

```python
from itertools import islice

numbers = range(10)

print(list(islice(numbers, 5)))  # [0, 1, 2, 3, 4]
print(list(islice(numbers, 2, 8)))  # [2, 3, 4, 5, 6, 7]
print(list(islice(numbers, 0, 10, 2)))  # [0, 2, 4, 6, 8]
```

---

### groupby(iterable, key=None)

Группирует **последовательные** элементы с одинаковым значением key.

**⚠️ ВАЖНО:** Работает только на отсортированных данных!

```python
from itertools import groupby

data = [1, 1, 2, 2, 2, 3, 1, 1]

for key, group in groupby(data):
    print(f'{key}: {list(group)}')
# 1: [1, 1]
# 2: [2, 2, 2]
# 3: [3]
# 1: [1, 1]  ← Новая группа!
```

---

### tee(iterable, n=2)

Создаёт n независимых итераторов.

```python
from itertools import tee

numbers = [1, 2, 3, 4, 5]

iter1, iter2 = tee(numbers, 2)

print(list(iter1))  # [1, 2, 3, 4, 5]
print(list(iter2))  # [1, 2, 3, 4, 5]
```

---

### zip_longest(*iterables, fillvalue=None)

Как zip(), но не останавливается на самом коротком.

```python
from itertools import zip_longest

numbers = [1, 2, 3, 4, 5]
letters = ['a', 'b', 'c']

result = zip_longest(numbers, letters, fillvalue='?')
print(list(result))
# [(1, 'a'), (2, 'b'), (3, 'c'), (4, '?'), (5, '?')]
```

---

## 🎲 4. Комбинаторные итераторы

### product(*iterables, repeat=1)

Декартово произведение.

```python
from itertools import product

result = product([1, 2], ['a', 'b'])
print(list(result))
# [(1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]

result = product([1, 2], repeat=2)
print(list(result))
# [(1, 1), (1, 2), (2, 1), (2, 2)]
```

---

### permutations(iterable, r=None)

Все перестановки (порядок важен).

```python
from itertools import permutations

result = permutations([1, 2, 3], 2)
print(list(result))
# [(1, 2), (1, 3), (2, 1), (2, 3), (3, 1), (3, 2)]
```

---

### combinations(iterable, r)

Все сочетания (порядок НЕ важен).

```python
from itertools import combinations

result = combinations([1, 2, 3, 4], 2)
print(list(result))
# [(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)]
```

---

### combinations_with_replacement(iterable, r)

Сочетания с повторениями.

```python
from itertools import combinations_with_replacement

result = combinations_with_replacement([1, 2, 3], 2)
print(list(result))
# [(1, 1), (1, 2), (1, 3), (2, 2), (2, 3), (3, 3)]
```

---

## 🎨 Практический пример: анимация

```python
import itertools as it
import time

symbols = ['.', '-', "'", '"', "'", '-', '.', '_']

for c in it.cycle(symbols):
    print(c, end='')
    time.sleep(0.05)
```

---

## 📝 Шпаргалка

### Импорт
```python
import itertools
# или
import itertools as it
```

### Бесконечные
```python
count(start, step)        # 0, 1, 2, 3, ...
cycle(iterable)           # 'A', 'B', 'A', 'B', ...
repeat(value, times)      # 'X', 'X', 'X'
```

### Преобразующие
```python
starmap(func, iterable)   # func(*element)
accumulate(iter, func)    # Накопительные суммы
```

### Ключевые моменты

✅ **Все возвращают итераторы** (ленивые)  
✅ **count, cycle, repeat** — бесконечные по умолчанию  
✅ **starmap ≠ map** — распаковывает аргументы  
✅ **accumulate ≠ reduce** — все промежуточные результаты  
✅ **cycle** сохраняет копию всех элементов  
✅ **repeat** хранит только один элемент  
✅ **groupby** требует сортировки

## 🔗 Связанные темы

- [[01 — 🔄 Бесконечные итераторы]]
- [[02 — 🔤 Комбинаторика]]
- [[03 — ✂️ Фильтрация и выборка]]
- [[04 — 🧲 Группировка и объединение]]
- [[05 — 🎯 Преобразующие функции]]
- [[01 — Итераторы (Iterator)]]
- [[02 — ⚡ Генераторы (Generator)]]
