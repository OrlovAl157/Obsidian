---
tags:
  - python
  - тема/итераторы
  - тема/генераторы
  - статус/завершён
---

# ⚡ Генераторы (Generator)

## 📌 Коротко

> Генератор — удобный способ создания итераторов через `yield`, который приостанавливает функцию и сохраняет её состояние; экономит память за счёт ленивых вычислений.

---

## 💡 Полезные советы

- **Два способа создать генератор** — функция с `yield` или генераторное выражение `(x for x in ...)`
- **`yield` ≠ `return`** — `yield` приостанавливает функцию, сохраняя все локальные переменные; `return` завершает её навсегда
- **`return` в генераторе** — возбуждает `StopIteration`, код после него никогда не выполнится
- **Генераторы одноразовые** — как все итераторы, после исчерпания повторно не используются
- **Генераторное выражение vs список** — `(x for x in ...)` не хранит элементы, `[x for x in ...]` хранит все сразу
- **`yield from`** — делегирует итерацию другому итерируемому, заменяет вложенный `for` + `yield`
- **Конвейер генераторов** — мощный паттерн для обработки больших данных без загрузки в память

## 🚀 Быстрая справка

| Задача | Способ | Пример |
|---|---|---|
| Функция-генератор | `def f(): yield x` | `def gen(): yield 1` |
| Генераторное выражение | `(expr for x in it)` | `(x**2 for x in range(5))` |
| Следующий элемент | `next(gen)` | `next(gen)` → `0` |
| Делегирование | `yield from iterable` | `yield from range(5)` |
| Все элементы | `list(gen)` | `list(gen)` → `[0, 1, 2]` |
| Распаковка | `print(*gen)` | `print(*gen)` |

---

## 📖 Теория

### Основы

**Генератор** — удобный способ создания итераторов без написания классов с `__iter__()` и `__next__()`.

**Было (класс-итератор) — 17 строк:**

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
        self.current += 1
        return self.current - 1

counter = Counter(1, 5)
print(list(counter))  # [1, 2, 3, 4, 5]
```

**Стало (генератор) — 5 строк:**

```python
def counter(low, high):
    for num in range(low, high + 1):
        yield num

gen = counter(1, 5)
print(list(gen))  # [1, 2, 3, 4, 5]
```

### Два типа генераторов

1. **Функции-генераторы** — функции с `yield`
2. **Генераторные выражения** — компактный синтаксис с `()`

---

## 💻 Примеры кода

### Раздел 1: Функции-генераторы

#### Ключевое слово yield

**`yield`** — приостанавливает функцию и возвращает значение, сохраняя состояние.

```python
def generate_ints(n):
    for num in range(n):
        yield num

generator = generate_ints(5)
print(type(generator))  # <class 'generator'>

print(next(generator))  # 0
print(next(generator))  # 1
print(next(generator))  # 2
```

#### Как работает yield

```python
def simple_gen():
    print('Перед первым yield')
    yield 1
    print('Между yield')
    yield 2
    print('После последнего yield')

gen = simple_gen()
print(next(gen))  # Перед первым yield → 1
print(next(gen))  # Между yield → 2
# print(next(gen))  # После последнего yield → StopIteration

# Состояние сохраняется между вызовами!
```

#### yield vs return

| Характеристика | `return` | `yield` |
|---|---|---|
| Завершает функцию | ✅ Да | ❌ Приостанавливает |
| Сохраняет локальные переменные | ❌ Нет | ✅ Да |
| Количество значений | 1 | Много |
| Возвращаемый тип | Любой | Generator |

#### return в генераторах

```python
def generate_ints():
    yield 1
    yield 2
    return 3  # Возбуждает StopIteration
    yield 4   # Никогда не выполнится

for num in generate_ints():
    print(num)
# 1
# 2

# `return` в генераторе = `StopIteration`
```

#### Без цикла тоже можно!

```python
def generate_1234():
    yield 1
    yield 2
    yield 3
    yield 4

print(*generate_1234())  # 1 2 3 4
```

#### Генераторы с побочными эффектами

```python
def generate_AB():
    print('start')
    yield 'A'
    print('continue')
    yield 'B'
    print('end')

for char in generate_AB():
    print('-->', char)

# Вывод:
# start
# --> A
# continue
# --> B
# end
```

### Раздел 2: Примеры функций-генераторов

#### Счётчик

```python
def counter(low, high):
    for num in range(low, high + 1):
        yield num

for i in counter(3, 10):
    print(i)  # 3, 4, 5, ..., 10
```

#### Бесконечный генератор

```python
def even_numbers(begin):
    begin += begin % 2
    while True:
        yield begin
        begin += 2

evens = even_numbers(10)
for i, num in enumerate(evens):
    if i > 5:
        break
    print(num)  # 10, 12, 14, 16, 18, 20
```

#### Обработка строк

```python
def string_wrapper(text, symbol):
    for char in text:
        yield symbol + char + symbol

print(list(string_wrapper('hi', '~')))
# ['~h~', '~i~']
```

#### Факториалы

```python
def factorials():
    value = 1
    index = 1
    while True:
        yield value
        index += 1
        value *= index

facts = factorials()
for i, num in enumerate(facts, 1):
    if i > 5:
        break
    print(f'{i}! = {num}')
# 1! = 1
# 2! = 2
# 3! = 6
# 4! = 24
# 5! = 120
```

### Раздел 3: Генераторные выражения

#### Синтаксис — круглые скобки!

```python
# Генератор списка (квадратные)
list_comp = [x**2 for x in range(5)]
print(list_comp)  # [0, 1, 4, 9, 16]
print(type(list_comp))  # <class 'list'>

# Генераторное выражение (круглые)
gen_expr = (x**2 for x in range(5))
print(gen_expr)  # <generator object>
print(type(gen_expr))  # <class 'generator'>
print(list(gen_expr))  # [0, 1, 4, 9, 16]
```

#### Примеры

```python
squares  = (i**2 for i in range(1, 7))
capitals = (s.upper() for s in 'abc')
stars    = ('*' for i in range(5))

for num in squares:
    print(num)  # 1, 4, 9, 16, 25, 36

print(next(capitals))  # A
print(*stars)          # * * * * *
```

#### Особенности синтаксиса

```python
# ❌ Нельзя писать без скобок
# squares = i*i for i in range(10)

# ✅ Правильно
squares = (i*i for i in range(10))

# В функциях скобки можно опустить
print(sum(i*i for i in range(10)))
print(sum((i*i for i in range(10))))  # оба варианта работают

# Можно переносить строки
even_squares = (
    i ** 2
    for i in range(10)
    if i % 2 == 0
)
```

#### Ограничения (как у всех итераторов)

```python
squares = (i*i for i in range(10))

# ❌ Нельзя узнать длину
# len(squares)  # TypeError

# ❌ Нельзя получить по индексу
# squares[3]  # TypeError

# ❌ Нельзя срезать
# squares[1:5]  # TypeError

# ❌ Одноразовые
print(list(squares))  # [0, 1, 4, ...]
print(list(squares))  # [] (пуст!)
```

#### vs map() и filter()

```python
fruits = ['apple', 'pineapple', 'banana']

# С map/filter
result1 = map(lambda s: s.upper(),
              filter(lambda s: len(s) > 5, fruits))

# С генераторным выражением (читабельнее!)
result2 = (s.upper() for s in fruits if len(s) > 5)

print(list(result1))  # ['PINEAPPLE', 'BANANA']
print(list(result2))  # ['PINEAPPLE', 'BANANA']
```

#### Память: генератор vs список

```python
from sys import getsizeof

range_obj = range(1000000)
list_obj  = list(range_obj)
gen_expr  = (x for x in range_obj)

print(getsizeof(range_obj))  # 48 байт
print(getsizeof(list_obj))   # 8000056 байт
print(getsizeof(gen_expr))   # 104 байт

# Генераторы экономят память!
```

#### vs Генераторные функции

```python
# Взаимозаменяемы в простых случаях:

# Функция-генератор
def do_something(elements):
    for item in elements:
        yield item * 2

# Генераторное выражение
def do_something(elements):
    return (item * 2 for item in elements)

# Когда что использовать:
# Генераторное выражение — для простой логики (1 строка)
# Функция-генератор     — для сложной логики, циклов, условий
```

### Раздел 4: yield from — Делегирование

#### Упрощение кода

```python
# Было:
def get_data():
    for num in range(5):
        yield num
    for char in 'ABC':
        yield char

# Стало:
def get_data():
    yield from range(5)
    yield from 'ABC'

print(list(get_data()))  # [0, 1, 2, 3, 4, 'A', 'B', 'C']
```

#### Композиция генераторов

```python
def chain(*iterables):
    for it in iterables:
        yield from it

result = chain('AB', [1, 2], (4, 5))
print(list(result))  # ['A', 'B', 1, 2, 4, 5]
```

#### Вложенные генераторы

```python
def generator2():
    yield 'Red'
    yield 'Blue'

def generator1():
    yield 'Green'
    yield from generator2()  # Делегирование!
    yield 'Yellow'

print(list(generator1()))
# ['Green', 'Red', 'Blue', 'Yellow']
```

#### Рекурсивные генераторы

```python
def numbers(start):
    if not isinstance(start, int):
        raise TypeError('Должно быть int')
    yield start
    yield from numbers(start + 1)

# Бесконечный генератор чисел
for i, num in enumerate(numbers(5)):
    if i > 5:
        break
    print(num)  # 5, 6, 7, 8, 9, 10
```

### Раздел 5: Цепочки генераторных выражений

Циклы указываются в порядке вложенности — возвращаемый элемент ставится в начало, а не в конец. Вот и вся разница с обычными вложенными циклами.

#### Функция-генератор

```python
def smth(iterables):
    for iterable in iterables:
        for seq in iterable:
            for elem in seq:
                yield elem

iterables = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]
print(*smth(iterables))  # 1 2 3 4 5 6 7 8
```

#### Генераторное выражение

```python
iterables = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]

smth = (elem for iterable in iterables for seq in iterable for elem in seq)
print(*smth)  # 1 2 3 4 5 6 7 8
```

### Раздел 6: Конвейеры генераторов

#### Композиция генераторов

**Обработка данных по цепочке:**

```python
def integers(n):
    for i in range(1, n + 1):
        yield i

def evens(iterable):
    for i in iterable:
        if i % 2 == 0:
            yield i

def squared(iterable):
    for i in iterable:
        yield i * i

def negated(iterable):
    for i in iterable:
        yield -i

# Создаём конвейер
chain = negated(squared(evens(integers(10))))
print(list(chain))  # [-4, -16, -36, -64, -100]
```

#### С генераторными выражениями

```python
n = 10

integers = (i for i in range(1, n + 1))
evens    = (i for i in integers if i % 2 == 0)
squared  = (i * i for i in evens)
negated  = (-i for i in squared)

print(list(negated))  # [-4, -16, -36, -64, -100]

# Читается как pipeline: числа → чётные → квадраты → инверсия
```

#### Обработка больших файлов

```python
# Читаем CSV построчно без загрузки в память
with open('data.csv', 'r', encoding='utf-8') as file:
    # Шаг 1: Читаем строки
    file_lines = (line for line in file)

    # Шаг 2: Разбиваем на значения
    line_values = (line.rstrip().split(',') for line in file_lines)

    # Шаг 3: Извлекаем заголовки
    headers = next(line_values)

    # Шаг 4: Создаём словари
    line_dicts = (dict(zip(headers, data)) for data in line_values)

    # Шаг 5: Фильтруем и обрабатываем
    result = (
        (line['user_name'], line['user_ip'])
        for line in line_dicts
        if 'stepik.org' in line['page_url']
    )

    # Шаг 6: Используем данные
    for name, ip in result:
        print(f'{name} --- {ip}')

# Каждая строка обрабатывается по мере чтения!
```

### Раздел 7: Производительность

#### Тест: Чтение файла

```python
# Вариант 1: return (загружает всё в память)
def read_immediately(file_name):
    with open(file_name) as file:
        result = []
        for line in file:
            line = line.rstrip('\n')
            if line:
                result.append(line)
        return result

# Вариант 2: yield (обрабатывает по одной)
def read_lazy(file_name):
    with open(file_name) as file:
        for line in file:
            line = line.rstrip('\n')
            if line:
                yield line
```

#### Результаты

| Размер файла | return (память) | yield (память) |
|---|---|---|
| 4 КБ | 5.3 МБ | 5.42 МБ |
| 324 КБ | 9.98 МБ | 5.37 МБ |
| 26 МБ | 392 МБ | 5.52 МБ |
| 263 МБ | 3.65 ГБ | 5.55 МБ |

**Вывод:** Генераторы держат память постоянной!

### Раздел 8: Практические паттерны

#### 1. Чтение больших файлов

```python
def read_large_file(file_path):
    with open(file_path) as file:
        for line in file:
            yield line.strip()

for line in read_large_file('big_data.txt'):
    process(line)
```

#### 2. Бесконечные последовательности

```python
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1

for i in infinite_sequence():
    if i > 10:
        break
    print(i)
```

#### 3. Обработка данных пакетами

```python
def batch(iterable, n):
    """Разбивает итератор на пакеты по n элементов"""
    batch = []
    for item in iterable:
        batch.append(item)
        if len(batch) == n:
            yield batch
            batch = []
    if batch:
        yield batch

data = range(10)
for chunk in batch(data, 3):
    print(chunk)
# [0, 1, 2]
# [3, 4, 5]
# [6, 7, 8]
# [9]
```

#### 4. Фильтрация с логированием

```python
def filter_with_log(predicate, iterable):
    for item in iterable:
        if predicate(item):
            print(f'Прошёл: {item}')
            yield item
        else:
            print(f'Отклонён: {item}')

numbers = [1, 2, 3, 4, 5]
evens = filter_with_log(lambda x: x % 2 == 0, numbers)
print(list(evens))
```

### Раздел 9: Сравнение

#### Генератор vs Итератор vs Range

| Характеристика | Генератор | Итератор (класс) | Range |
|---|---|---|---|
| **Создание** | `yield` или `()` | Класс с `__iter__`/`__next__` | `range()` |
| **Тип** | `generator` | Свой класс | `range` |
| **Протокол** | Iterator | Iterator | Iterable |
| **len()** | ❌ | ❌ | ✅ |
| **Индексация** | ❌ | ❌ | ✅ |
| **Срезы** | ❌ | ❌ | ✅ |
| **Повторное использование** | ❌ Одноразовый | ❌ Одноразовый | ✅ Многоразовый |
| **Память** | Минимальная | Минимальная | Минимальная |
| **in оператор** | ✅ Опустошает | ✅ Опустошает | ✅ Не опустошает |

```python
# Генератор - одноразовый
gen = (i for i in range(3))
print(list(gen))  # [0, 1, 2]
print(list(gen))  # [] (пуст!)

# Range - многоразовый
r = range(3)
print(list(r))  # [0, 1, 2]
print(list(r))  # [0, 1, 2] (снова!)

# Range поддерживает индексацию
print(r[1])    # 1
print(len(r))  # 3
print(r[1:3])  # range(1, 3)
```

---

### Шпаргалка

#### Создание генераторов

```python
# Функция-генератор
def my_gen():
    yield 1
    yield 2

# Генераторное выражение
gen = (x for x in range(5))

# yield from
def chain(*args):
    for arg in args:
        yield from arg
```

#### Использование

```python
gen = (x for x in range(5))

print(next(gen))       # 0
for item in gen: ...   # цикл
print(list(gen))       # список
print(*gen)            # распаковка
```

#### Ключевые моменты

✅ **Генератор = удобный итератор**
✅ **`yield` = приостановка с сохранением состояния**
✅ **Одноразовые** (истощаются после использования)
✅ **Ленивые** (вычисляют по требованию)
✅ **Экономят память** (не хранят всё)
✅ **Компонуются** (конвейеры)

❌ **Нет `len()`**
❌ **Нет индексации**
❌ **Нет срезов**
❌ **Нельзя повторно использовать**

#### Когда использовать

| Задача | Решение |
|---|---|
| Простое преобразование | Генераторное выражение |
| Сложная логика | Функция-генератор |
| Большие файлы | Генератор |
| Бесконечные последовательности | Генератор |
| Нужна индексация | Список |
| Нужна повторная итерация | Список или range |

---

## 🔗 Связанные темы

- [[Итераторы]]
- [[Итератор vs Генератор]]
- [[Итераторы и коллекции]]
- [[Модуль itertools]]

## ❓ Вопросы / Непонятное
