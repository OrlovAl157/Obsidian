---
tags:
  - python
  - тема/функции
  - тема/встроенные_функции
  - статус/завершён
уровень: начальный
возвращает_итератор: false
ленивые_вычисления: false
принимает_key: false
встроенная: true
---

# Памятка по функции sum()

## 💡 Полезные советы

- **sum()** вычисляет сумму элементов итерируемого объекта
- **Работает с числами**: int, float, complex, Decimal, Fraction
- **Необязательный start**: начальное значение (по умолчанию 0)
- **Не для строк**: используйте `''.join()` вместо `sum()` для строк
- **Быстрее цикла**: оптимизирована на уровне C
- **Для вложенных списков**: используйте `sum(lists, [])` для объединения
- **С генератором**: экономит память для больших последовательностей
- **Вызовет ошибку**: на нечисловых типах (кроме start=[])

## 🚀 Быстрая справка

| Задача                      | Синтаксис                    |                  Пример                  |
| --------------------------- | ---------------------------- | :--------------------------------------: |
| Сумма списка                | `sum(iterable)`              |          `sum([1, 2, 3])` → `6`          |
| Сумма с начальным значением | `sum(iterable, start)`       |       `sum([1, 2, 3], 10)` → `16`        |
| Сумма range                 | `sum(range(n))`              |          `sum(range(5))` → `10`          |
| Сумма с условием            | `sum(x for x in ... if ...)` | `sum(x for x in [1,2,3] if x > 1)` → `5` |
| Сумма квадратов             | `sum(x**2 for x in ...)`     |   `sum(x**2 for x in [1,2,3])` → `14`    |
| Подсчёт True                | `sum(condition for ...)`     |   `sum(x > 0 for x in [-1,2,3])` → `2`   |
| Объединение списков         | `sum(lists, [])`             |  `sum([[1,2],[3,4]], [])` → `[1,2,3,4]`  |
| Сумма значений словаря      | `sum(dict.values())`         |   `sum({'a':1, 'b':2}.values())` → `3`   |

## 📝 Базовое использование

```python
# ========== СУММА СПИСКА ==========

numbers = [1, 2, 3, 4, 5]
total = sum(numbers)            # → 15

# Пустой список
empty = []
total = sum(empty)              # → 0 (не ошибка!)

# С разными типами чисел
integers = [1, 2, 3]
sum(integers)                   # → 6

floats = [1.5, 2.5, 3.0]
sum(floats)                     # → 7.0

mixed = [1, 2.5, 3]
sum(mixed)                      # → 6.5 (int + float = float)

# ========== СУММА КОРТЕЖА ==========

numbers = (10, 20, 30, 40)
sum(numbers)                    # → 100

# ========== СУММА МНОЖЕСТВА ==========

numbers = {1, 2, 3, 4, 5}
sum(numbers)                    # → 15

# ========== СУММА RANGE ==========

sum(range(1, 11))               # → 55 (1+2+3+...+10)
sum(range(5))                   # → 10 (0+1+2+3+4)
sum(range(10, 20, 2))           # → 70 (10+12+14+16+18)

# ========== С НАЧАЛЬНЫМ ЗНАЧЕНИЕМ (start) ==========

numbers = [1, 2, 3, 4, 5]

sum(numbers, 0)                 # → 15 (то же что sum(numbers))
sum(numbers, 10)                # → 25 (15 + 10)
sum(numbers, 100)               # → 115 (15 + 100)
sum(numbers, -5)                # → 10 (15 + (-5))

# Практический пример: добавление бонуса
scores = [80, 90, 75]
bonus = 10
total_with_bonus = sum(scores, bonus)  # → 255 (245 + 10)
```

## 🎯 Генераторы и comprehensions

```python
# ========== СУММА С УСЛОВИЕМ ==========

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Сумма чётных чисел
even_sum = sum(x for x in numbers if x % 2 == 0)  # → 30

# Сумма нечётных чисел
odd_sum = sum(x for x in numbers if x % 2 != 0)   # → 25

# Сумма положительных
numbers = [-5, -2, 0, 3, 7, -1]
positive_sum = sum(x for x in numbers if x > 0)   # → 10

# ========== СУММА С ПРЕОБРАЗОВАНИЕМ ==========

# Сумма квадратов
squares_sum = sum(x**2 for x in range(1, 6))      # → 55 (1+4+9+16+25)

# Сумма длин строк
words = ['hello', 'world', 'python']
total_length = sum(len(word) for word in words)   # → 16

# Сумма абсолютных значений
numbers = [-5, 3, -2, 7, -1]
abs_sum = sum(abs(x) for x in numbers)            # → 18

# ========== ВЛОЖЕННЫЕ COMPREHENSIONS ==========

# Сумма всех элементов в матрице
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

total = sum(sum(row) for row in matrix)           # → 45

# Альтернатива (более эффективная)
from itertools import chain
total = sum(chain.from_iterable(matrix))          # → 45

# ========== ПОДСЧЁТ УСЛОВИЙ (True = 1, False = 0) ==========

numbers = [1, -2, 3, -4, 5, -6, 7]

# Сколько положительных
positive_count = sum(x > 0 for x in numbers)      # → 4

# Сколько чётных
even_count = sum(x % 2 == 0 for x in numbers)     # → 3

# Сколько больше 3
greater_than_3 = sum(x > 3 for x in numbers)      # → 2

words = ['apple', 'banana', 'cherry', 'date']
# Сколько длиннее 5 символов
long_words = sum(len(w) > 5 for w in words)       # → 2
```

## 📊 Работа со словарями

```python
# ========== СУММА ЗНАЧЕНИЙ ==========

prices = {'apple': 50, 'banana': 30, 'cherry': 80}

# Сумма всех цен
total_price = sum(prices.values())                # → 160

# ========== СУММА КЛЮЧЕЙ (если ключи числовые) ==========

data = {1: 'a', 2: 'b', 3: 'c'}
key_sum = sum(data.keys())                        # → 6
# или
key_sum = sum(data)                               # → 6 (по умолчанию итерирует ключи)

# ========== УСЛОВНАЯ СУММА ==========

prices = {'apple': 50, 'banana': 30, 'cherry': 80, 'date': 45}

# Сумма цен > 40
expensive_sum = sum(price for price in prices.values() if price > 40)  # → 175

# Сумма цен товаров на 'a'
a_items_sum = sum(price for item, price in prices.items() if item.startswith('a'))  # → 50

# ========== СПИСОК СЛОВАРЕЙ ==========

products = [
    {'name': 'apple', 'price': 50, 'quantity': 3},
    {'name': 'banana', 'price': 30, 'quantity': 5},
    {'name': 'cherry', 'price': 80, 'quantity': 2}
]

# Общая стоимость
total_cost = sum(item['price'] * item['quantity'] for item in products)  # → 460

# Общее количество товаров
total_quantity = sum(item['quantity'] for item in products)  # → 10

# Средняя цена
average_price = sum(item['price'] for item in products) / len(products)  # → 53.33
```

## 🎯 Практические примеры

### Статистика оценок

```python
# Оценки студентов
grades = [85, 92, 78, 95, 88, 76, 90]

# Общая сумма
total = sum(grades)                               # → 604

# Средний балл
average = sum(grades) / len(grades)               # → 86.29

# Сумма оценок выше среднего
above_avg = sum(g for g in grades if g > average) # → 365

# Количество сдавших (>= 75)
passed = sum(g >= 75 for g in grades)             # → 7
```

### Финансовый отчёт

```python
# Транзакции (+ доход, - расход)
transactions = [1000, -200, -150, 500, -75, -300, 800, -100]

# Общий баланс
balance = sum(transactions)                       # → 1475

# Общий доход
income = sum(t for t in transactions if t > 0)    # → 2300

# Общий расход
expenses = sum(t for t in transactions if t < 0)  # → -825
# или
expenses = sum(abs(t) for t in transactions if t < 0)  # → 825

# Количество транзакций доходов
income_count = sum(t > 0 for t in transactions)   # → 3
```

### Анализ текста

```python
text = "Hello World Python Programming"
words = text.split()

# Общая длина всех слов
total_chars = sum(len(word) for word in words)    # → 28

# Среднее длина слова
avg_length = sum(len(word) for word in words) / len(words)  # → 7.0

# Количество длинных слов (> 5 символов)
long_words = sum(len(word) > 5 for word in words) # → 3

# Сумма ASCII кодов первых букв
first_letters_sum = sum(ord(word[0]) for word in words)  # → 292
```

### Обработка матрицы

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Сумма всех элементов
total = sum(sum(row) for row in matrix)           # → 45

# Сумма диагонали
diagonal_sum = sum(matrix[i][i] for i in range(len(matrix)))  # → 15

# Сумма первой строки
first_row = sum(matrix[0])                        # → 6

# Сумма первого столбца
first_col = sum(row[0] for row in matrix)         # → 12

# Сумма чётных элементов
even_sum = sum(x for row in matrix for x in row if x % 2 == 0)  # → 20
```

### Временные интервалы

```python
# Время в секундах
times = [120, 85, 150, 95, 110]  # секунды

# Общее время в секундах
total_seconds = sum(times)                        # → 560

# Конвертация в минуты
total_minutes = sum(times) / 60                   # → 9.33

# Среднее время
average_time = sum(times) / len(times)            # → 112.0

# Время задач > 100 сек
long_tasks_time = sum(t for t in times if t > 100)  # → 380
```

### Координаты и расстояния

```python
import math

# Точки на плоскости
points = [(1, 2), (3, 4), (5, 6)]

# Сумма всех x координат
sum_x = sum(p[0] for p in points)                 # → 9

# Сумма всех y координат
sum_y = sum(p[1] for p in points)                 # → 12

# Центр масс
center_x = sum(p[0] for p in points) / len(points)  # → 3.0
center_y = sum(p[1] for p in points) / len(points)  # → 4.0

# Общее расстояние от начала координат
total_distance = sum(math.sqrt(x**2 + y**2) for x, y in points)  # → 18.44
```

## 🔧 Специальные случаи

```python
# ========== ОБЪЕДИНЕНИЕ СПИСКОВ (start=[]) ==========

lists = [[1, 2], [3, 4], [5, 6]]

# Объединение в один список
flat = sum(lists, [])                             # → [1, 2, 3, 4, 5, 6]

# ⚠️ Медленно для больших данных! Лучше использовать:
from itertools import chain
flat = list(chain.from_iterable(lists))           # → быстрее

# ========== С DECIMAL ДЛЯ ТОЧНОСТИ ==========

from decimal import Decimal

prices = [Decimal('10.50'), Decimal('20.30'), Decimal('15.75')]
total = sum(prices)                               # → Decimal('46.55')

# Сравнение с float
prices_float = [10.50, 20.30, 15.75]
total_float = sum(prices_float)                   # → 46.55 (может быть неточность)

# ========== С FRACTIONS ==========

from fractions import Fraction

fractions = [Fraction(1, 2), Fraction(1, 3), Fraction(1, 4)]
total = sum(fractions)                            # → Fraction(13, 12)

# ========== КОМПЛЕКСНЫЕ ЧИСЛА ==========

complex_nums = [1+2j, 3+4j, 5+6j]
total = sum(complex_nums)                         # → (9+12j)

# ========== БУЛЕВЫ ЗНАЧЕНИЯ (True=1, False=0) ==========

bools = [True, False, True, True, False]
count_true = sum(bools)                           # → 3

# Процент True
percentage = sum(bools) / len(bools) * 100        # → 60.0
```

## 🔧 Комбинации с другими функциями

```python
# ========== С MAP ==========

numbers = [1, 2, 3, 4, 5]

# Сумма квадратов
sum_of_squares = sum(map(lambda x: x**2, numbers))  # → 55

# Сумма длин
words = ['hi', 'hello', 'hey']
total_length = sum(map(len, words))               # → 10

# ========== С FILTER ==========

numbers = [1, -2, 3, -4, 5, -6]

# Сумма только положительных
positive_sum = sum(filter(lambda x: x > 0, numbers))  # → 9

# ========== С ZIP ==========

prices = [10, 20, 30]
quantities = [2, 3, 1]

# Общая стоимость
total_cost = sum(p * q for p, q in zip(prices, quantities))  # → 110

# ========== С ENUMERATE ==========

scores = [85, 90, 78, 92, 88]

# Взвешенная сумма (индекс как вес)
weighted_sum = sum(score * i for i, score in enumerate(scores, 1))  # → 1313

# ========== ВЛОЖЕННЫЕ SUM ==========

# Сумма средних значений строк
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
sum_of_averages = sum(sum(row)/len(row) for row in matrix)  # → 15.0

# ========== С ANY/ALL ==========

# Есть ли хотя бы одно True
values = [False, False, True, False]
has_any_true = sum(values) > 0                    # → True
# или лучше
has_any_true = any(values)                        # → True (предпочтительно)

# Все ли True
all_true = sum(values) == len(values)             # → False
# или лучше
all_true = all(values)                            # → False (предпочтительно)
```

## ⚠️ Частые ошибки

```python
# ❌ КОНКАТЕНАЦИЯ СТРОК через sum()
strings = ['Hello', ' ', 'World']
# result = sum(strings)                           # TypeError!

# ✅ Используйте join()
result = ''.join(strings)                         # → 'Hello World'

# ❌ СЛОЖЕНИЕ СЛОВАРЕЙ через sum()
dicts = [{'a': 1}, {'b': 2}, {'c': 3}]
# result = sum(dicts, {})                         # TypeError!

# ✅ Используйте update() или |
result = {}
for d in dicts:
    result.update(d)                              # → {'a': 1, 'b': 2, 'c': 3}

# или (Python 3.9+)
from functools import reduce
result = reduce(lambda x, y: x | y, dicts)       # → {'a': 1, 'b': 2, 'c': 3}

# ❌ Неправильное использование start с несовместимым типом
numbers = [1, 2, 3]
# total = sum(numbers, '0')                       # TypeError!

# ✅ start должен быть совместим с элементами
total = sum(numbers, 0)                           # → 6

# ⚠️ sum() не изменяет оригинал
numbers = [1, 2, 3, 4, 5]
total = sum(numbers)                              # → 15
print(numbers)                                    # → [1, 2, 3, 4, 5] (не изменился)

# ⚠️ Точность с float
prices = [0.1, 0.1, 0.1]
total = sum(prices)                               # → 0.30000000000000004 (неточность!)

# ✅ Используйте Decimal для денег
from decimal import Decimal
prices = [Decimal('0.1'), Decimal('0.1'), Decimal('0.1')]
total = sum(prices)                               # → Decimal('0.3') (точно!)

# ⚠️ Медленное объединение списков
lists = [[1], [2], [3]] * 1000
# flat = sum(lists, [])                           # Очень медленно O(n²)!

# ✅ Используйте chain
from itertools import chain
flat = list(chain.from_iterable(lists))           # → Быстро O(n)

# ❌ Попытка суммировать нечисловые без start
data = [None, None, None]
# total = sum(data)                               # TypeError!

# ✅ Фильтруйте или используйте условия
total = sum(x for x in data if x is not None)    # → 0 (пустая сумма)
```

## 🎯 Когда использовать sum()

**✅ Используйте sum() когда:**

- Нужна сумма чисел
- Работаете с итерируемыми объектами
- Нужно подсчитать количество True значений
- Вычисляете среднее значение
- Агрегируете числовые данные
- Нужно простое и читабельное решение

**❌ НЕ используйте sum() для:**

- Конкатенации строк (используйте `''.join()`)
- Объединения словарей (используйте `update()` или `|`)
- Больших вложенных списков (используйте `chain()`)

**🔄 Альтернативы:**

```python
numbers = [1, 2, 3, 4, 5]

# ✅ sum() - стандартный способ
total = sum(numbers)                              # → 15

# ❌ Ручной цикл - избыточно
total = 0
for num in numbers:
    total += num                                  # → 15 (менее читабельно)

# ✅ functools.reduce - когда нужна сложная логика
from functools import reduce
total = reduce(lambda x, y: x + y, numbers)       # → 15

# ✅ numpy.sum() - для больших массивов (быстрее)
import numpy as np
arr = np.array([1, 2, 3, 4, 5])
total = np.sum(arr)                               # → 15 (оптимизировано)
```

## 📈 Производительность

```python
import timeit

# Сравнение способов суммирования
numbers = list(range(10000))

# sum() - самый быстрый
time_sum = timeit.timeit(lambda: sum(numbers), number=10000)

# Ручной цикл - медленнее
def manual_sum(nums):
    total = 0
    for n in nums:
        total += n
    return total
time_manual = timeit.timeit(lambda: manual_sum(numbers), number=10000)

# reduce - самый медленный
from functools import reduce
time_reduce = timeit.timeit(
    lambda: reduce(lambda x, y: x + y, numbers), 
    number=10000
)

print(f"sum():    {time_sum:.4f}s")
print(f"manual:   {time_manual:.4f}s") 
print(f"reduce(): {time_reduce:.4f}s")

# Результат (примерно):
# sum():    0.0150s  ← самый быстрый
# manual:   0.0850s
# reduce(): 0.1200s  ← самый медленный
```

## 🔗 Связанные темы

- [[06 — 🎯 reduce()]]
- [[Целые числа (int) 🔹]]
- [[00 — 🎯 Встроенные функции Python]]
