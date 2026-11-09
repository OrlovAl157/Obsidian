---
tags: [python, итераторы, iter, next]
difficulty: intermediate
---

# 🔄 Итераторы (Iterator) — памятка

> Итератор — объект, который выдаёт элементы по одному через `next()` и возбуждает `StopIteration`, когда они закончились. На итераторах построен любой цикл `for`. Итераторы ленивые и одноразовые.

## Содержание

- [[#📋 Справка|Справка]]
- [[#🧠 Iterable и Iterator|Iterable и Iterator]]
- [[#🔧 Функции iter() и next()|Функции iter() и next()]]
- [[#⚙️ Протокол итератора|Протокол итератора]]
- [[#🛠 Свои итераторы|Свои итераторы]]
- [[#🪞 Зачем итератору возвращать self|Зачем итератору возвращать self]]
- [[#🔁 Как устроен цикл for|Как устроен цикл for]]
- [[#💤 Ленивость и память|Ленивость и память]]
- [[#🧰 Встроенные функции-итераторы|Встроенные функции-итераторы]]
- [[#🎭 iter() с sentinel|iter() с sentinel]]
- [[#🔍 Что опустошает итератор|Что опустошает итератор]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

## 📋 Справка

| Задача | Способ | Пример |
| --- | --- | --- |
| Создать итератор | `iter(iterable)` | `it = iter([1, 2, 3])` |
| Следующий элемент | `next(it)` | `next(it)` → `1` |
| Следующий с дефолтом | `next(it, default)` | `next(it, -1)` |
| Итератор из функции | `iter(callable, sentinel)` | `iter(file.readline, '')` |
| Применить функцию | `map(func, it)` | `map(str.upper, words)` |
| Отфильтровать | `filter(func, it)` | `filter(None, items)` |
| Пронумеровать | `enumerate(it, start=0)` | `enumerate(items, 1)` |
| Объединить | `zip(*iterables)` | `zip(keys, values)` |
| Перевернуть | `reversed(seq)` | `reversed([1, 2, 3])` |

| Метод протокола | Что делает |
| --- | --- |
| `__iter__()` | у iterable — создаёт итератор; у итератора — возвращает `self` |
| `__next__()` | возвращает следующий элемент или возбуждает `StopIteration` |

---

## 🧠 Iterable и Iterator

**Назначение:** различать объект, по которому можно пройтись (iterable), и объект, который выдаёт элементы (iterator).

**Iterable (итерируемый объект)** — любой объект с методом `__iter__()`, по которому можно пройтись циклом `for`. На его основе можно создать итератор.

**Iterator (итератор)** — объект, который выдаёт элементы по одному. После выдачи элемента он сдвигается к следующему. Когда все элементы выданы, итератор опустошается и доступ к нему невозможен.

Два вида итерируемых объектов:

- **Коллекции и последовательности** — хранят элементы (`list`, `tuple`, `str`, `set`, `dict`)
- **Итераторы** — выдают элементы по одному и не хранят всё в памяти

| Особенность | Список | Итератор |
| --- | --- | --- |
| Доступ по индексу | ✅ `list[0]` | ❌ `TypeError` |
| Длина | ✅ `len(list)` | ❌ `TypeError` |
| Повторная итерация | ✅ можно | ❌ одноразовый |
| Память | ❌ хранит всё | ✅ ленивый |
| Модификация | ✅ можно | ❌ нельзя |

**Важное:**

- Любой итератор — итерируемый объект, но не наоборот
- Итераторы ленивы: значение вычисляется только по запросу
- Итераторы одноразовые: после исчерпания пусты
- Чем коллекция отличается от последовательности и где место `range` — в [[04 — 🗂 Итерируемые объекты и коллекции]]

**Примеры:**

```python
numbers = [1, 2, 3]      # список — iterable
name = 'python'          # строка — iterable

for num in numbers:      # итерируем список
    print(num)

for char in name:        # итерируем строку
    print(char)

print(2 in numbers)      # → True (неявная итерация)
print(*numbers)          # → 1 2 3 (распаковка тоже итерирует)
```

---

## 🔧 Функции iter() и next()

**Назначение:** создать итератор и по очереди получать из него элементы.

- `iter(iterable)` — создаёт итератор на основе итерируемого объекта
- `next(iterator)` — возвращает следующий элемент
- Когда элементы закончились, `next()` возбуждает `StopIteration`
- `next(iterator, default)` — вместо `StopIteration` вернёт `default`

**Примеры:**

```python
numbers = [1, 2, 3, 4, 5]
iterator = iter(numbers)

print(next(iterator))   # → 1
print(next(iterator))   # → 2
print(next(iterator))   # → 3

for n in iterator:      # цикл неявно вызывает next()
    print(n)            # → 4, 5

# print(next(iterator)) # → StopIteration: итератор исчерпан
```

```python
# Обработка исчерпания через try/except
nums = iter([1, 2, 3])
try:
    while True:
        print(next(nums))
except StopIteration:
    print('Элементы закончились')

# Значение по умолчанию вместо исключения
nums = iter([1, 2, 3])
print(next(nums, -1))   # → 1
print(next(nums, -1))   # → 2
print(next(nums, -1))   # → 3
print(next(nums, -1))   # → -1 (вместо StopIteration)
```

**Несколько независимых итераторов** — из одной коллекции можно создать сколько угодно, у каждого своя позиция:

```python
numbers = [1, 2, 3, 4]

it1 = iter(numbers)
it2 = iter(numbers)

print(next(it1))   # → 1
print(next(it1))   # → 2
print(next(it2))   # → 1 (it2 не зависит от it1)
```

**Тип итератора зависит от коллекции:**

```python
print(type(iter([1, 2, 3])))   # → <class 'list_iterator'>
print(type(iter({1, 2, 3})))   # → <class 'set_iterator'>
print(type(iter((1, 2, 3))))   # → <class 'tuple_iterator'>
```

---

## ⚙️ Протокол итератора

**Назначение:** набор правил, которым должен следовать объект, чтобы быть итератором или итерируемым.

**Магические методы (dunder)** — методы с двойным подчёркиванием, которые Python вызывает сам: `len(x)` вызывает `x.__len__()`, `str(x)` — `x.__str__()`, `iter(x)` — `x.__iter__()`. Посмотреть все методы объекта можно через `dir(x)`.

| Объект | Что должен иметь |
| --- | --- |
| Итерируемый (iterable) | `__iter__()` — возвращает итератор |
| Итератор (iterator) | `__next__()` и `__iter__()`; `__iter__()` возвращает `self` |

`__next__()` отвечает за вычисление очередного элемента и за `StopIteration` при исчерпании. Он вызывается, когда итератор передают в `next()`. `__iter__()` у итератора нужен, чтобы по нему можно было итерироваться, например циклом `for`.

**Примеры:**

```python
words = ['hello', 'world']

iterator = iter(words)                 # внешний вызов
iterator = words.__iter__()            # то же самое «за кулисами»
print(type(words))                     # → <class 'list'>
print(type(iterator))                  # → <class 'list_iterator'>

print(next(iterator))                  # → hello
print(iterator.__next__())             # → world

iterator2 = iter(iterator)             # iter() от итератора возвращает его же
print(iterator is iterator2)           # → True
```

**Важное:** прямые вызовы `__iter__()` и `__next__()` нужны только для понимания. В коде используют `iter()` и `next()`.

---

## 🛠 Свои итераторы

**Назначение:** написать собственный тип итератора через класс с методами `__iter__()` и `__next__()`.

Шаблон:

```python
class MyIterator:
    def __iter__(self):
        return self          # возвращает сам итератор

    def __next__(self):
        ...                  # вернуть элемент или raise StopIteration
```

**Конечный итератор — диапазон чисел:**

```python
class Counter:
    def __init__(self, low, high):
        self.low = low
        self.high = high

    def __iter__(self):
        return self

    def __next__(self):
        if self.low > self.high:
            raise StopIteration
        value = self.low
        self.low += 1
        return value

for i in Counter(3, 6):
    print(i)                 # → 3, 4, 5, 6

counter = Counter(100, 103)
print(next(counter))         # → 100
print(next(counter))         # → 101
```

Пока `low <= high`, итератор выдаёт элементы. Как только `low` превысит `high`, он исчерпан, и каждое следующее обращение возбуждает `StopIteration`.

**Бесконечный итератор** — `StopIteration` не возбуждается, значит, остановить цикл нужно самому:

```python
class EvenNumbers:
    def __init__(self, begin):
        self.current = begin + begin % 2     # ближайшее чётное

    def __iter__(self):
        return self

    def __next__(self):
        value = self.current
        self.current += 2
        return value

for index, num in enumerate(EvenNumbers(10)):
    if index > 5:
        break                                # без break цикл был бы вечным
    print(num)                               # → 10, 12, 14, 16, 18, 20
```

**Преобразование элементов:**

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

print(list(StringWrapper('hi', '+')))        # → ['+h+', '+i+']
```

**Бесконечные факториалы:**

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

for i, fact in enumerate(Factorials(), 1):
    if i > 5:
        break
    print(f'{i}! = {fact}')                  # → 1! = 1, 2! = 2, 3! = 6, 4! = 24, 5! = 120
```

---

## 🪞 Зачем итератору возвращать self

**Назначение:** понять, почему `__iter__()` у итератора обязан возвращать `self`.

Итератор хранит состояние и сдвигается вперёд при каждом `next()`. Если `__iter__()` вернёт **другой** объект, то по одному и тому же итератору начнут работать два независимых «курсора», и поведение станет противоречивым.

**Пример — неправильный итератор:**

```python
class Counter:
    def __init__(self, low, high):
        self.low = low
        self.high = high

    def __iter__(self):
        return Counter(self.low, self.high)   # ❌ новый итератор вместо self

    def __next__(self):
        if self.low > self.high:
            raise StopIteration
        self.low += 1
        return self.low - 1

counter = Counter(3, 6)

for i in counter:
    print(i)              # → 3, 4, 5, 6

print(next(counter))      # → 3 (!)
print(next(counter))      # → 4 (!)
```

Цикл `for` вызвал `iter(counter)` и получил **новый** объект `Counter`, который и исчерпал. Сам `counter` не сдвинулся, поэтому `next(counter)` после цикла снова выдаёт `3` и `4`. Две независимые итерации скрываются за одной переменной.

**Важное:** итератор возвращает `self`, чтобы `iter(it)` и `it` были одним и тем же объектом и состояние было общим. Хочешь многоразовый перебор — делай отдельный **iterable-класс**, который при каждом `__iter__()` создаёт новый итератор (см. [[04 — 🗂 Итерируемые объекты и коллекции]]).

---

## 🔁 Как устроен цикл for

**Назначение:** понять, что `for` — это `iter()` плюс бесконечный `while` с `next()`.

Цикл `for` сначала вызывает `iter()` для объекта после `in`, затем запускает бесконечный цикл, в котором применяет `next()`. Как только возникает `StopIteration`, он перехватывает её и прерывает цикл.

**Примеры:**

```python
# Этот код:
for num in numbers:
    print(num)

# работает как:
iterator = iter(numbers)
while True:
    try:
        num = next(iterator)
        print(num)
    except StopIteration:
        break
```

```python
# Примерная реализация for в виде функции
def my_for(iterable, body):
    iterator = iter(iterable)          # создаём итератор
    while True:
        try:
            value = next(iterator)     # пробуем взять элемент
        except StopIteration:
            break                      # конец — выходим из цикла
        body(value)                    # тело цикла
```

**Однотипность:** благодаря итераторам цикл одинаково работает с разными типами:

```python
for item in [1, 2, 3]: ...     # список
for item in (1, 2, 3): ...     # кортеж
for item in 'abc': ...         # строка
for item in {1, 2, 3}: ...     # множество
for item in range(3): ...      # range
```

---

## 💤 Ленивость и память

**Назначение:** итераторы не хранят все элементы, а вычисляют их по запросу.

**Ленивые вычисления** — не делать работу, пока результат не понадобился.

```python
from sys import getsizeof

# range не хранит числа
print(getsizeof(range(5)))                 # → 48 байт
print(getsizeof(range(10000000000000)))    # → 48 байт (!)

# список хранит все
print(getsizeof(list(range(5))))           # → 96 байт
print(getsizeof(list(range(100000))))      # → 800056 байт

# list(range(10000000000000))              # → MemoryError
```

Точные значения `getsizeof` зависят от версии Python, важен порядок.

**Цепочка итераторов** — промежуточные объекты тоже итераторы и память не занимают:

```python
sentence = 'In the face of ambiguity refuse the temptation to guess'

filter_it = filter(lambda w: len(w) > 4, sentence.split())   # фильтруем
map_it = map(lambda w: w.upper(), filter_it)                 # преобразуем
enum_it = enumerate(map_it, 1)                               # нумеруем

for index, value in enum_it:
    print(f'{index}. {value}')
# → 1. AMBIGUITY
# → 2. REFUSE
# → 3. TEMPTATION
# → 4. GUESS
```

---

## 🧰 Встроенные функции-итераторы

**Назначение:** готовые итераторы для типовых задач. Все они ленивые, результат смотрим через `list()`.

| Функция | Что делает | Результат |
| --- | --- | --- |
| `map(f, it)` | применяет `f` к каждому элементу | тип `map` |
| `filter(f, it)` | оставляет элементы, где `f` истинно | тип `filter` |
| `enumerate(it, start=0)` | добавляет номер элемента | пары `(номер, элемент)` |
| `zip(*its)` | собирает элементы по позициям | кортежи |
| `reversed(seq)` | идёт с конца | только для последовательностей |

**Примеры:**

```python
numbers = [1, 2, 3, 4, 5]
squares = map(lambda x: x ** 2, numbers)
print(list(squares))                  # → [1, 4, 9, 16, 25]

numbers = [45, -90, 4, 89, -21, 43]
print(list(filter(lambda x: x > 0, numbers)))     # → [45, 4, 89, 43]

objects = ('a', None, 45, False, 0, '')
print(list(filter(None, objects)))    # → ['a', 45] (None отбирает истинные значения)

seasons = ['Spring', 'Summer', 'Fall', 'Winter']
print(list(enumerate(seasons, start=1)))
# → [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]

languages = ['Python', 'C#', 'Java']
years = [1991, 2000, 1995]
print(list(zip(languages, years)))
# → [('Python', 1991), ('C#', 2000), ('Java', 1995)]

print(list(zip([1, 2, 3, 4, 5], ['a', 'b', 'c'])))   # → [(1, 'a'), (2, 'b'), (3, 'c')]
# zip останавливается на самом коротком; zip(..., strict=True) (3.10+) → ValueError при разной длине

print(list(reversed([1991, 2000, 1972])))             # → [1972, 2000, 1991]
print(list(reversed('python')))                       # → ['n', 'o', 'h', 't', 'y', 'p']
```

---

## 🎭 iter() с sentinel

**Назначение:** превратить обычную функцию в итератор, который останавливается на стоп-значении.

```python
iter(callable, sentinel)
```

- `callable` — функция без аргументов
- `sentinel` — стоп-значение

Итератор вызывает `callable()` и сравнивает результат с `sentinel`. Если равно — возбуждает `StopIteration`, иначе возвращает результат.

**Примеры:**

```python
import random

# Бесконечные случайные числа (стоп-значение None никогда не выпадет)
it = iter(lambda: random.randrange(1, 11), None)
print(next(it))

# Бесконечный итератор нулей: int() всегда возвращает 0, а стоп — -1
zeros = iter(int, -1)
print([next(zeros) for _ in range(5)])        # → [0, 0, 0, 0, 0]

# Случайные числа до первой двойки
from random import choice

def random_num():
    return choice(range(1, 11))

for num in iter(random_num, 2):
    print(num)                                # остановится, когда выпадет 2

# Чтение файла до пустой строки
with open('data.txt') as file:
    for line in iter(file.readline, ''):
        print(line.strip())
```

---

## 🔍 Что опустошает итератор

**Назначение:** запомнить, какие операции «съедают» элементы итератора.

| Операция | Что происходит |
| --- | --- |
| `for x in it` | проходит до конца, итератор пуст |
| `list(it)`, `tuple(it)`, `set(it)` | забирает все элементы |
| `*it` (распаковка) | забирает все элементы |
| `x in it` | идёт до найденного элемента, остаток остаётся |
| `max(it)`, `min(it)`, `sum(it)` | проходят до конца |

**Примеры:**

```python
numbers = [-3, 6, 1, -90, 34]
positive = map(abs, numbers)

for num in positive:
    print(num)                 # → 3, 6, 1, 90, 34
for num in positive:
    print(num)                 # (ничего: итератор уже пуст)

positive = map(abs, numbers)
print(list(positive))          # → [3, 6, 1, 90, 34]
print(list(positive))          # → [] (опустошён)
```

```python
numbers = [4, 8, 15, 16, 23, 42]
iterator = iter(numbers)

print(15 in iterator)          # → True
print(15 in iterator)          # → False (15 уже пройден)

iterator = iter(numbers)
print(15 in iterator)          # → True
print(23 in iterator)          # → True (поиск продолжился с места остановки)

iterator = iter(numbers)
print(*iterator)               # → 4 8 15 16 23 42
print(list(iterator))          # → [] (опустошён)
```

```python
squares = map(lambda x: x ** 2, [1, 2, 3, 4, 5])
print(max(squares))            # → 25
print(list(squares))           # → [] (max опустошил итератор)
```

---

## ⚠️ Частые ошибки

**❌ ОШИБКА:** повторно использовать исчерпанный итератор.

```python
numbers = map(abs, [-1, -2, -3])
print(list(numbers))           # → [1, 2, 3]
print(list(numbers))           # → [] (молча пусто, без ошибки)
```

✅ Создай итератор заново (`iter(коллекция)`) или сохрани результат в список.

---

**❌ ОШИБКА:** менять коллекцию во время итерации.

```python
numbers = [10, 20, 30, 40, 50]
iterator = iter(numbers)
print(next(iterator))          # → 10
print(next(iterator))          # → 20
del numbers[2]                 # удалили 30
print(next(iterator))          # → 40 (30 пропущен)
```

Итератор списка идёт по индексам, после удаления элементы сдвинулись, и один из них пропущен.

---

**❌ ОШИБКА:** ждать от итератора `len()` и индексации.

```python
evens = filter(lambda x: x % 2 == 0, [1, 2, 3, 4, 5, 6])
# len(evens)       # → TypeError
# evens[0]         # → TypeError
```

✅ Если нужны длина или индекс, преобразуй в список: `list(evens)`.

---

**❌ ОШИБКА:** запустить бесконечный итератор без ограничения.

```python
# for num in EvenNumbers(2):
#     print(num)               # → 2, 4, 6, ... бесконечно
```

✅ Добавь `break`, `itertools.islice` или условие остановки.

---

**⚠️ Ловушки, которые не ошибки:**

```python
# sorted() возвращает список, а не итератор
print(type(sorted([3, 1, 4])))      # → <class 'list'>

# for по словарю даёт только ключи
people = {'name': 'Timur', 'age': 29}
for key in people:
    print(key)                      # → name, age
# нужны пары — people.items()

# reversed() работает только с последовательностями
# reversed(iter([1, 2, 3]))         # → TypeError
```

---

## ✅ Главные правила

✅ Итератор = `__next__()` + `__iter__()` (возвращает `self`)  
✅ `iter()` создаёт итератор, `next()` берёт элемент, `StopIteration` — конец  
✅ `next(it, default)` — безопасный вариант без исключения  
✅ Итератор одноразовый: после исчерпания нужен новый через `iter(коллекция)`  
✅ `in`, `max()`, `min()`, `sum()`, `list()` и `*` опустошают итератор  
✅ Нет `len()`, индексации и срезов  
✅ Не меняй коллекцию во время итерации  
✅ Итератор подходит для больших данных, потоков и конвейеров (`map` → `filter`)  
✅ Для небольших данных и повторного обхода проще список  
✅ Бесконечный итератор останавливай сам: `break` или `islice`

---

## 🔗 Связанные темы

- [[02 — ⚡ Генераторы (Generator)]]
- [[03 — 🎛 Управление генератором]]
- [[04 — 🗂 Итерируемые объекты и коллекции]]
- [[05 — 🆚 Итератор vs Генератор]]
- [[00 — 🔄 Модуль itertools - Полная памятка]]
- [[05 — 🔄 Циклы for и while]]

#python #итераторы #iter #next #протокол-итератора
