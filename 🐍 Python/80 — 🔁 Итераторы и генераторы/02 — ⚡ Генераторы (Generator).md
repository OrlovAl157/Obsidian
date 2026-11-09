---
tags: [python, генераторы, yield]
difficulty: intermediate
---

# ⚡ Генераторы (Generator) — памятка

> Генератор — ленивый объект, частный случай итератора. Создаётся функцией с `yield` или генераторным выражением `(x for x in ...)`. Приостанавливает выполнение, сохраняет состояние и экономит память.

## Содержание

- [[#📋 Справка|Справка]]
- [[#🧠 Что такое генератор|Что такое генератор]]
- [[#⚡ Функции-генераторы|Функции-генераторы]]
- [[#🧪 Примеры функций-генераторов|Примеры функций-генераторов]]
- [[#🔤 Генераторные выражения|Генераторные выражения]]
- [[#🔗 yield и yield from|yield и yield from]]
- [[#🪆 Цепочки генераторных выражений|Цепочки генераторных выражений]]
- [[#🏭 Конвейеры генераторов|Конвейеры генераторов]]
- [[#📊 Производительность и память|Производительность и память]]
- [[#🧩 Практические паттерны|Практические паттерны]]
- [[#⏳ Эффект позднего связывания|Эффект позднего связывания]]
- [[#🆚 Генератор, итератор и range|Генератор, итератор и range]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

## 📋 Справка

| Задача | Способ | Пример |
| --- | --- | --- |
| Функция-генератор | `def f(): yield x` | `def gen(): yield 1` |
| Генераторное выражение | `(expr for x in it)` | `(x**2 for x in range(5))` |
| Следующий элемент | `next(gen)` | `next(gen)` → `0` |
| Делегирование | `yield from iterable` | `yield from range(5)` |
| Все элементы | `list(gen)` | `list(gen)` → `[0, 1, 2]` |
| Распаковка | `print(*gen)` | `print(*gen)` |
| Управление | `send()`, `close()`, `throw()` | см. [[03 — 🎛 Управление генератором]] |

---

## 🧠 Что такое генератор

**Назначение:** создавать итераторы без написания класса с `__iter__()` и `__next__()`. Генератор неявно реализует оба метода.

Два вида генераторов:

1. **Функции-генераторы** — функции с оператором `yield` или `yield from`
2. **Генераторные выражения** — компактная запись в круглых скобках

**Было — класс-итератор:**

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

print(list(Counter(1, 5)))      # → [1, 2, 3, 4, 5]
```

**Стало — генератор:**

```python
def counter(low, high):
    for num in range(low, high + 1):
        yield num

print(list(counter(1, 5)))      # → [1, 2, 3, 4, 5]
```

**Важное:** функция-генератор выглядит как обычная функция, но вместо одного `return` отдаёт значения по очереди через `yield`. Вызов такой функции не выполняет тело, а возвращает объект-генератор.

---

## ⚡ Функции-генераторы

**Назначение:** описывать последовательность значений обычным кодом.

`yield` приостанавливает выполнение функции и возвращает значение. Следующий `next()` продолжает с того же места, все локальные переменные сохранены. Так происходит, пока тело функции не завершится (например, закончится цикл).

**Примеры:**

```python
def generate_ints(n):
    for num in range(n):
        yield num

generator = generate_ints(5)
print(type(generator))       # → <class 'generator'>
print(next(generator))       # → 0
print(next(generator))       # → 1
print(next(generator))       # → 2
```

**Как работает `yield`** — тело выполняется порциями между `yield`:

```python
def simple_gen():
    print('Перед первым yield')
    yield 1
    print('Между yield')
    yield 2
    print('После последнего yield')

gen = simple_gen()
print(next(gen))     # → Перед первым yield, затем 1
print(next(gen))     # → Между yield, затем 2
# next(gen)          # → «После последнего yield», затем StopIteration
```

**Побочные эффекты** выполняются лениво — только когда запрашивают элемент:

```python
def generate_AB():
    print('start')
    yield 'A'
    print('continue')
    yield 'B'
    print('end')

for char in generate_AB():
    print('-->', char)
# → start, --> A, continue, --> B, end
```

**Без цикла тоже можно:**

```python
def generate_1234():
    yield 1
    yield 2
    yield 3
    yield 4

print(*generate_1234())      # → 1 2 3 4
```

| Характеристика | `return` | `yield` |
| --- | --- | --- |
| Что делает с функцией | завершает | приостанавливает |
| Локальные переменные | теряются | сохраняются |
| Количество значений | одно | много |
| Результат вызова | значение | объект-генератор |

**`return` внутри генератора** допустим: он завершает генератор, то есть возбуждает `StopIteration`, а код после него не выполнится. Значение из `return` попадает в `StopIteration.value` (подробнее в [[03 — 🎛 Управление генератором]]):

```python
def generate_ints():
    yield 1
    yield 2
    return 3         # завершает генератор
    yield 4          # недостижимо

for num in generate_ints():
    print(num)       # → 1, 2
```

---

## 🧪 Примеры функций-генераторов

**Назначение:** типовые генераторы — те же задачи, что в [[01 — Итераторы (Iterator)]], но короче.

```python
# Счётчик
def counter(low, high):
    for num in range(low, high + 1):
        yield num

for i in counter(3, 6):
    print(i)                     # → 3, 4, 5, 6
```

```python
# Бесконечный генератор (останавливаем сами)
def even_numbers(begin):
    begin += begin % 2
    while True:
        yield begin
        begin += 2

for i, num in enumerate(even_numbers(10)):
    if i > 5:
        break
    print(num)                   # → 10, 12, 14, 16, 18, 20
```

```python
# Преобразование элементов
def string_wrapper(text, symbol):
    for char in text:
        yield symbol + char + symbol

print(list(string_wrapper('hi', '~')))     # → ['~h~', '~i~']
```

```python
# Бесконечные факториалы
def factorials():
    value = 1
    index = 1
    while True:
        yield value
        index += 1
        value *= index

for i, num in enumerate(factorials(), 1):
    if i > 5:
        break
    print(f'{i}! = {num}')       # → 1! = 1, 2! = 2, 3! = 6, 4! = 24, 5! = 120
```

---

## 🔤 Генераторные выражения

**Назначение:** простой способ создать генератор, когда тело умещается в одно выражение.

Синтаксис отличается от списка только скобками: `[...]` — список, `(...)` — генератор.

```python
list_comp = [x**2 for x in range(5)]
print(list_comp)                  # → [0, 1, 4, 9, 16]
print(type(list_comp))            # → <class 'list'>

gen_expr = (x**2 for x in range(5))
print(type(gen_expr))             # → <class 'generator'>
print(list(gen_expr))             # → [0, 1, 4, 9, 16]
```

**Примеры:**

```python
squares  = (i ** 2 for i in range(1, 7))
capitals = (s.upper() for s in 'abc')
stars    = ('*' for i in range(5))

for num in squares:
    print(num)                    # → 1, 4, 9, 16, 25, 36

print(next(capitals))             # → A
print(*stars)                     # → * * * * *
```

**Особенности синтаксиса:**

```python
# squares = i*i for i in range(10)       # ❌ без скобок нельзя
squares = (i*i for i in range(10))       # ✅

print(sum(i*i for i in range(10)))       # в вызове функции скобки можно опустить
print(sum((i*i for i in range(10))))     # оба варианта работают

even_squares = (                         # можно переносить строки
    i ** 2
    for i in range(10)
    if i % 2 == 0
)
```

**Ограничения** (как у всех итераторов):

```python
squares = (i*i for i in range(10))
# len(squares)       # → TypeError
# squares[3]         # → TypeError
# squares[1:5]       # → TypeError
print(list(squares)) # → [0, 1, 4, ...]
print(list(squares)) # → [] (одноразовый)
```

**Вместо `map()` и `filter()`** выражение читается проще:

```python
fruits = ['apple', 'pineapple', 'banana']

result1 = map(lambda s: s.upper(), filter(lambda s: len(s) > 5, fruits))
result2 = (s.upper() for s in fruits if len(s) > 5)

print(list(result1))     # → ['PINEAPPLE', 'BANANA']
print(list(result2))     # → ['PINEAPPLE', 'BANANA']
```

**Функция-генератор или выражение:** в простых случаях они взаимозаменяемы.

```python
def doubled_gen(elements):          # функция-генератор
    for item in elements:
        yield item * 2

def doubled_expr(elements):         # то же выражением
    return (item * 2 for item in elements)
```

Выражение — для простой логики в одну строку, функция — для сложной логики, нескольких циклов и условий.

---

## 🔗 yield и yield from

**Назначение:** отдавать значения по одному (`yield`) или делегировать перебор другому итерируемому (`yield from`).

`yield` возвращает **ровно одно** значение, `yield from` перебирает объект рядом с собой и возвращает его элементы по очереди.

| Оператор | С какими объектами | Что отдаёт |
| --- | --- | --- |
| `yield x` | с любыми | сам объект `x` как одно значение |
| `yield from x` | только с iterable | элементы `x` по очереди |

`yield from` работает с любым итерируемым объектом (коллекцией, итератором, другим генератором), а не только с коллекциями.

**Примеры:**

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

print(list(get_data()))          # → [0, 1, 2, 3, 4, 'A', 'B', 'C']
```

```python
# yield отдаёт объект целиком, yield from — его элементы
def counter(low, high):
    yield range(low, high + 1)

print(next(counter(3, 10)))      # → range(3, 11)

def counter(low, high):
    for n in range(low, high + 1):
        yield from n             # n — целое число, оно не iterable

# next(counter(3, 10))           # → TypeError: 'int' object is not iterable
```

```python
# Композиция генераторов
def chain(*iterables):
    for it in iterables:
        yield from it

print(list(chain('AB', [1, 2], (4, 5))))    # → ['A', 'B', 1, 2, 4, 5]
```

```python
# Вложенные генераторы
def generator2():
    yield 'Red'
    yield 'Blue'

def generator1():
    yield 'Green'
    yield from generator2()
    yield 'Yellow'

print(list(generator1()))        # → ['Green', 'Red', 'Blue', 'Yellow']
```

```python
# Рекурсивный генератор
def numbers(start):
    if not isinstance(start, int):
        raise TypeError('Должно быть int')
    yield start
    yield from numbers(start + 1)

for i, num in enumerate(numbers(5)):
    if i > 5:
        break
    print(num)                   # → 5, 6, 7, 8, 9, 10
```

**Важное:** этот рекурсивный генератор «бесконечный» только на бумаге. Каждый элемент добавляет уровень вложенности, и примерно после тысячи элементов будет `RecursionError`. `TypeError` возникнет при первом `next()`, а не при вызове функции: тело генератора лениво.

---

## 🪆 Цепочки генераторных выражений

**Назначение:** вложенные циклы в одном выражении.

Циклы указываются в порядке вложенности, возвращаемый элемент ставится **в начало**, а не в конец. Это единственная разница с обычными вложенными циклами.

```python
iterables = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]

# Функция-генератор
def smth(iterables):
    for iterable in iterables:
        for seq in iterable:
            for elem in seq:
                yield elem

# То же выражением
flat = (elem for iterable in iterables for seq in iterable for elem in seq)

print(*smth(iterables))          # → 1 2 3 4 5 6 7 8
print(*flat)                     # → 1 2 3 4 5 6 7 8
```

---

## 🏭 Конвейеры генераторов

**Назначение:** обрабатывать данные по цепочке, где каждый этап — отдельный генератор и ничего не хранится целиком.

```python
# Функции-генераторы
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

pipeline = negated(squared(evens(integers(10))))
print(list(pipeline))            # → [-4, -16, -36, -64, -100]
```

```python
# То же генераторными выражениями
n = 10
integers = (i for i in range(1, n + 1))
evens    = (i for i in integers if i % 2 == 0)
squared  = (i * i for i in evens)
negated  = (-i for i in squared)

print(list(negated))             # → [-4, -16, -36, -64, -100]
# читается как pipeline: числа → чётные → квадраты → инверсия
```

**Обработка большого файла** — каждая строка обрабатывается по мере чтения:

```python
with open('data.csv', 'r', encoding='utf-8') as file:
    line_values = (line.rstrip().split(',') for line in file)   # разбиваем на значения
    headers = next(line_values)                                 # первая строка — заголовки
    line_dicts = (dict(zip(headers, data)) for data in line_values)
    result = (
        (line['user_name'], line['user_ip'])
        for line in line_dicts
        if 'stepik.org' in line['page_url']
    )
    for name, ip in result:
        print(f'{name} --- {ip}')
```

---

## 📊 Производительность и память

**Назначение:** показать, почему генераторы выигрывают на больших данных.

```python
from sys import getsizeof

range_obj = range(1000000)
list_obj  = list(range_obj)
gen_expr  = (x for x in range_obj)

print(getsizeof(range_obj))      # → 48 байт
print(getsizeof(list_obj))       # → 8000056 байт
print(getsizeof(gen_expr))       # → ~100–200 байт (зависит от версии Python)
```

**Чтение файла: `return` против `yield`:**

```python
def read_immediately(file_name):         # всё сразу в память
    with open(file_name) as file:
        result = []
        for line in file:
            line = line.rstrip('\n')
            if line:
                result.append(line)
        return result

def read_lazy(file_name):                # по одной строке
    with open(file_name) as file:
        for line in file:
            line = line.rstrip('\n')
            if line:
                yield line
```

| Размер файла | Память: `return` | Память: `yield` |
| --- | --- | --- |
| 4 КБ | 5.3 МБ | 5.42 МБ |
| 324 КБ | 9.98 МБ | 5.37 МБ |
| 26 МБ | 392 МБ | 5.52 МБ |
| 263 МБ | 3.65 ГБ | 5.55 МБ |

**Важное:** цифры взяты из материалов курса, версия Python и способ замера там не указаны, считай их ориентиром. Вывод от этого не меняется: у генератора расход памяти почти постоянный.

---

## 🧩 Практические паттерны

```python
# 1. Чтение больших файлов
def read_large_file(file_path):
    with open(file_path) as file:
        for line in file:
            yield line.strip()

# 2. Бесконечная последовательность
def infinite_sequence():
    num = 0
    while True:
        yield num
        num += 1

# 3. Обработка пакетами
def batch(iterable, n):
    chunk = []
    for item in iterable:
        chunk.append(item)
        if len(chunk) == n:
            yield chunk
            chunk = []
    if chunk:
        yield chunk

for part in batch(range(10), 3):
    print(part)                  # → [0, 1, 2], [3, 4, 5], [6, 7, 8], [9]

# 4. Фильтрация с логированием
def filter_with_log(predicate, iterable):
    for item in iterable:
        if predicate(item):
            print(f'Прошёл: {item}')
            yield item
        else:
            print(f'Отклонён: {item}')

print(list(filter_with_log(lambda x: x % 2 == 0, [1, 2, 3, 4, 5])))
```

---

## ⏳ Эффект позднего связывания

**Назначение:** понять, почему ленивый генератор может выдать не то, что лежит в коллекции.

В генераторном выражении есть две части с разной «жизнью»:

- **первый итерируемый объект** (`for i in numbers`) вычисляется **сразу** при создании выражения, генератор запоминает ссылку на этот список;
- **всё остальное** (условия, выражение элемента) вычисляется **в момент перебора**, и переменные берутся тогда, а не при создании.

```python
numbers = [1, 8, 1, 22, 8, 8, 33, 18]
generator = (i for i in numbers if numbers.count(i) > 1)
print(list(generator))           # → [1, 8, 1, 8, 8]
```

Но если переменную `numbers` перепривязать до перебора:

```python
numbers = [1, 8, 1, 22, 8, 8, 33, 18]
generator = (i for i in numbers if numbers.count(i) > 1)

numbers = [7, 7, 7, 33, 1, 12, 1]    # теперь numbers ссылается на другой список
print(list(generator))               # → [1, 1]
```

Элементы берутся из **старого** списка (ссылка запомнена), а `numbers.count(i)` считается по **новому**. Из старого списка условию удовлетворяет только `1`: в новом он встречается дважды.

Это и есть эффект позднего связывания. Он возникает, потому что генераторы ленивы, а переменные в Python хранят ссылки, а не значения.

**Как избежать:**

```python
# Лучше: не обращаться внутри выражения к изменяемой внешней переменной
from collections import Counter

numbers = [1, 8, 1, 22, 8, 8, 33, 18]
counts = Counter(numbers)                        # снимок подсчётов
generator = (i for i in numbers if counts[i] > 1)

numbers = [7, 7, 7, 33, 1, 12, 1]
print(list(generator))                           # → [1, 8, 1, 8, 8] (результат не изменился)
```

Вариант из курса: менять список на месте методами `clear()` и `extend()` вместо перепривязки. Тогда генератор увидит новое содержимое того же списка (результат `[7, 7, 7, 1, 1]`). Это не защита, а лишь способ получить согласованный результат.

**Тот же эффект у лямбд и замыканий** (подробно в разделе про замыкания, папка `30 — 🔧 Функции`):

```python
funcs = []
for number in range(5):
    funcs.append(lambda: number)

print([f() for f in funcs])      # → [4, 4, 4, 4, 4], а не [0, 1, 2, 3, 4]
```

Функция запоминает **переменную** из внешней области, а не её значение, поэтому при вызове видит последнее присвоенное (`4`). Решение: параметр со значением по умолчанию, значение фиксируется в момент определения:

```python
funcs = []
for number in range(5):
    funcs.append(lambda num=number: num)

print([f() for f in funcs])      # → [0, 1, 2, 3, 4]
```

---

## 🆚 Генератор, итератор и range

| Характеристика | Генератор | Итератор (класс) | `range` |
| --- | --- | --- | --- |
| Создание | `yield` или `()` | класс с `__iter__`/`__next__` | `range()` |
| Тип | `generator` | свой класс | `range` |
| Протокол | Iterator | Iterator | Iterable (последовательность) |
| `len()` | ❌ | ❌ | ✅ |
| Индексация и срезы | ❌ | ❌ | ✅ |
| Повторное использование | ❌ одноразовый | ❌ одноразовый | ✅ многоразовый |
| Память | минимальная | минимальная | минимальная |
| Оператор `in` | опустошает | опустошает | не опустошает |

```python
gen = (i for i in range(3))
print(list(gen))      # → [0, 1, 2]
print(list(gen))      # → [] (пуст)

r = range(3)
print(list(r))        # → [0, 1, 2]
print(list(r))        # → [0, 1, 2] (снова)
print(r[1], len(r), r[1:3])    # → 1 3 range(1, 3)
```

Чем `range` устроен и почему он не итератор — в [[04 — 🗂 Итерируемые объекты и коллекции]]. Чем итератор отличается от генератора — в [[05 — 🆚 Итератор vs Генератор]].

---

## ⚠️ Частые ошибки

**❌ ОШИБКА:** перебирать генератор дважды.

```python
gen = (i for i in range(3))
print(list(gen))      # → [0, 1, 2]
print(list(gen))      # → [] (молча пусто)
```

✅ Создай генератор заново или сохрани результат в список.

---

**❌ ОШИБКА:** ждать, что вызов функции-генератора выполнит её тело.

```python
def noisy():
    print('Работаю')
    yield 1

gen = noisy()         # ничего не напечатано: тело ещё не запускалось
next(gen)             # → Работаю
```

---

**❌ ОШИБКА:** писать генераторное выражение без скобок.

```python
# squares = i*i for i in range(10)     # → SyntaxError
squares = (i*i for i in range(10))     # ✅
```

---

**❌ ОШИБКА:** `len()`, индекс или срез у генератора → `TypeError`. Нужны они — используй список.

**❌ ОШИБКА:** бесконечный генератор без `break` или `islice` → вечный цикл.

**❌ ОШИБКА:** `yield from` с неитерируемым объектом (например, `int`) → `TypeError`. Один объект отдавай через `yield`.

**⚠️ Ловушка:** внешние переменные в условиях и выражениях генератора читаются в момент перебора (см. «Эффект позднего связывания»).

---

## ✅ Главные правила

✅ Генератор — удобный способ создать итератор, а не отдельная сущность  
✅ `yield` приостанавливает функцию и сохраняет состояние  
✅ Вызов функции-генератора тело не запускает, это делает `next()`  
✅ `yield` отдаёт объект целиком, `yield from` — его элементы по очереди  
✅ Генераторы одноразовые, ленивые и экономят память  
✅ Простая логика — генераторное выражение, сложная — функция-генератор  
✅ Конвейеры из генераторов обрабатывают большие данные без загрузки в память  
✅ Переменные в условиях генератора читаются в момент перебора  
✅ Нет `len()`, индексации и срезов  
✅ Управление генератором (`send`, `close`, `throw`) — в [[03 — 🎛 Управление генератором]]

---

## 🔗 Связанные темы

- [[01 — Итераторы (Iterator)]]
- [[03 — 🎛 Управление генератором]]
- [[04 — 🗂 Итерируемые объекты и коллекции]]
- [[05 — 🆚 Итератор vs Генератор]]
- [[00 — 🔄 Модуль itertools - Полная памятка]]
- [[🔄 Рекурсия]]

#python #генераторы #yield #yield-from #ленивые-вычисления
