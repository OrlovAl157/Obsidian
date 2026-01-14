Модуль для работы с итераторами, создания и комбинирования итераторов.

## 📚 Классификация функций itertools

### 1. 🔄 **Бесконечные итераторы**

| Функция | Аргументы | Результат | Пример |
|---------|-----------|-----------|--------|
| `count()` | start=0, step=1 | Бесконечный счетчик | `0, 1, 2, ...` |
| `cycle()` | iterable | Бесконечно повторяет | `A, B, C, A, B, ...` |
| `repeat()` | object, [times] | Повторяет объект | `x, x, x, ...` |

```python
import itertools

# count(start=0, step=1) - бесконечный счетчик
for i in itertools.count(10, 2):  # 10, 12, 14, ...
    if i > 20:
        break

# cycle(iterable) - бесконечное повторение
cycle_iter = itertools.cycle('ABC')  # A, B, C, A, B, C, ...
next(cycle_iter)  # 'A'
next(cycle_iter)  # 'B'

# repeat(object[, times]) - повторение объекта
repeat_iter = itertools.repeat('X', 3)  # X, X, X
list(repeat_iter)  # ['X', 'X', 'X']
```

### 2. 🔤 **Комбинаторика (конечные последовательности)**

| Функция | Описание | Формула | Пример (ABCD, 2) |
|---------|----------|---------|------------------|
| `product()` | Декартово произведение | r^n | AA, AB, AC, AD, BA, BB, ... |
| `permutations()` | Перестановки | n!/(n-r)! | AB, AC, AD, BA, BC, BD, ... |
| `combinations()` | Комбинации | n!/r!(n-r)! | AB, AC, AD, BC, BD, CD |
| `combinations_with_replacement()` | Комбинации с повторами | (n+r-1)!/r!(n-1)! | AA, AB, AC, AD, BB, BC, ... |

```python
import itertools

data = ['A', 'B', 'C', 'D']

# product(*iterables, repeat=1) - декартово произведение
list(itertools.product(data, repeat=2))  
# [('A','A'), ('A','B'), ..., ('D','D')] - 16 элементов

list(itertools.product([0,1], repeat=3))  
# [(0,0,0), (0,0,1), (0,1,0), ..., (1,1,1)]

# permutations(iterable, r=None) - перестановки
list(itertools.permutations(data, 2))  
# [('A','B'), ('A','C'), ..., ('D','C')] - 12 элементов

# combinations(iterable, r) - комбинации (без повторов)
list(itertools.combinations(data, 2))  
# [('A','B'), ('A','C'), ('A','D'), ('B','C'), ('B','D'), ('C','D')] - 6 элементов

# combinations_with_replacement(iterable, r) - комбинации с повторами
list(itertools.combinations_with_replacement(data, 2))  
# [('A','A'), ('A','B'), ..., ('D','D')] - 10 элементов
```

### 3. ✂️ **Фильтрация и выборка**

| Функция | Описание | Пример |
|---------|----------|--------|
| `filterfalse()` | Элементы, где predicate=False | `[1, 3, 5]` из `[1, 2, 3, 4, 5]` с `is_even` |
| `dropwhile()` | Пропускает, пока условие True | `[3, 4, 5]` из `[1, 2, 3, 4, 5]` с `x<3` |
| `takewhile()` | Берет, пока условие True | `[1, 2]` из `[1, 2, 3, 4, 5]` с `x<3` |
| `compress()` | Выбирает по маске True/False | `['A', 'C']` из `['A','B','C']` с `[1,0,1]` |
| `islice()` | Срез как у последовательности | `[2, 3, 4]` из `range(10)` с `slice(2,5)` |

```python
import itertools

# filterfalse(predicate, iterable) - противоположность filter()
numbers = [1, 2, 3, 4, 5]
list(itertools.filterfalse(lambda x: x % 2 == 0, numbers))  # [1, 3, 5]

# dropwhile(predicate, iterable) - пропускает пока условие истинно
list(itertools.dropwhile(lambda x: x < 3, [1, 2, 3, 4, 5]))  # [3, 4, 5]

# takewhile(predicate, iterable) - берет пока условие истинно
list(itertools.takewhile(lambda x: x < 3, [1, 2, 3, 4, 5]))  # [1, 2]

# compress(data, selectors) - фильтрация по маске
data = ['A', 'B', 'C', 'D']
selectors = [1, 0, 1, 0]  # True/False
list(itertools.compress(data, selectors))  # ['A', 'C']

# islice(iterable, stop) или islice(iterable, start, stop[, step])
list(itertools.islice(range(10), 5))  # [0, 1, 2, 3, 4]
list(itertools.islice(range(10), 2, 8, 2))  # [2, 4, 6]
```

### 4. 🧲 **Группировка и объединение**

| Функция | Описание | Пример |
|---------|----------|--------|
| `groupby()` | Группирует по ключу | `[(k, list(g)) for k, g in groupby('AAAABBBCCD')]` |
| `chain()` | Объединяет итераторы | `[1, 2, 3, 4, 5, 6]` из `[1,2,3]` и `[4,5,6]` |
| `chain.from_iterable()` | Распаковывает и объединяет | `[1, 2, 3, 4]` из `[[1,2], [3,4]]` |
| `zip_longest()` | zip() для разной длины | `[(1,'a'), (2,'b'), (3,None)]` |
| `tee()` | Клонирует итератор | `(iter1, iter2) = tee(original)` |

```python
import itertools

# groupby(iterable, key=None) - группировка по ключу
data = sorted(['apple', 'banana', 'cherry', 'apricot', 'blueberry'], key=lambda x: x[0])
for key, group in itertools.groupby(data, key=lambda x: x[0]):
    print(key, list(group))
# a ['apple', 'apricot']
# b ['banana', 'blueberry']
# c ['cherry']

# chain(*iterables) - объединение итераторов
list(itertools.chain([1, 2], [3, 4], [5, 6]))  # [1, 2, 3, 4, 5, 6]

# chain.from_iterable(iterable) - для вложенных структур
list(itertools.chain.from_iterable([[1, 2], [3, 4]]))  # [1, 2, 3, 4]

# zip_longest(*iterables, fillvalue=None) - zip для разной длины
list(itertools.zip_longest([1, 2, 3], ['a', 'b'], fillvalue='-'))
# [(1, 'a'), (2, 'b'), (3, '-')]

# tee(iterable, n=2) - клонирование итератора
original = iter([1, 2, 3])
copy1, copy2 = itertools.tee(original, 2)
list(copy1)  # [1, 2, 3]
list(copy2)  # [1, 2, 3] (независимый)
```

### 5. 🎯 **Полезные утилиты**

| Функция | Описание | Пример |
|---------|----------|--------|
| `accumulate()` | Накопление с функцией | `[1, 3, 6, 10]` из `[1,2,3,4]` с `+` |
| `pairwise()` | Пары соседей (Python 3.10+) | `[(1,2), (2,3), (3,4)]` из `[1,2,3,4]` |
| `starmap()` | map() с распаковкой | `[6, 12]` из `[(2,3), (3,4)]` с `mul` |

```python
import itertools
import operator

# accumulate(iterable[, func]) - накопление
list(itertools.accumulate([1, 2, 3, 4]))  # [1, 3, 6, 10] (сумма)
list(itertools.accumulate([1, 2, 3, 4], operator.mul))  # [1, 2, 6, 24] (произведение)

# pairwise(iterable) - пары соседних элементов (Python 3.10+)
list(itertools.pairwise([1, 2, 3, 4]))  # [(1, 2), (2, 3), (3, 4)]

# starmap(function, iterable) - map с распаковкой аргументов
list(itertools.starmap(pow, [(2, 3), (3, 2)]))  # [8, 9] (2³=8, 3²=9)
```

## 🚀 **Практические примеры**

### 1. 🔢 **Генерация последовательностей**
```python
import itertools

# Генерация бинарных последовательностей
binary = [''.join(seq) for seq in itertools.product('01', repeat=3)]
# ['000', '001', '010', '011', '100', '101', '110', '111']

# Все перестановки имени
name = 'ABC'
perms = [''.join(p) for p in itertools.permutations(name)]
# ['ABC', 'ACB', 'BAC', 'BCA', 'CAB', 'CBA']

# Все возможные пароли длины 4 из букв
letters = 'ABCD'
passwords = [''.join(p) for p in itertools.product(letters, repeat=4)]
# 256 комбинаций
```

### 2. 📊 **Анализ данных**
```python
import itertools

# Группировка данных
data = [
    {'name': 'Alice', 'dept': 'HR', 'salary': 50000},
    {'name': 'Bob', 'dept': 'IT', 'salary': 60000},
    {'name': 'Charlie', 'dept': 'HR', 'salary': 55000},
    {'name': 'David', 'dept': 'IT', 'salary': 65000},
]

# Сортировка для groupby
data_sorted = sorted(data, key=lambda x: x['dept'])

# Группировка по отделу
for dept, group in itertools.groupby(data_sorted, key=lambda x: x['dept']):
    employees = list(group)
    avg_salary = sum(e['salary'] for e in employees) / len(employees)
    print(f"{dept}: {len(employees)} employees, avg salary: {avg_salary}")
```

### 3. 🎮 **Комбинаторные задачи**
```python
import itertools
import math

# Задача о рюкзаке (упрощенная)
items = ['A', 'B', 'C', 'D']
weights = [2, 3, 4, 5]
values = [3, 4, 5, 6]
capacity = 7

best_value = 0
best_combination = None

# Проверяем все комбинации
for r in range(1, len(items) + 1):
    for combo in itertools.combinations(range(len(items)), r):
        total_weight = sum(weights[i] for i in combo)
        total_value = sum(values[i] for i in combo)
        
        if total_weight <= capacity and total_value > best_value:
            best_value = total_value
            best_combination = combo

print(f"Best: {[items[i] for i in best_combination]}, value: {best_value}")
```

### 4. 🔄 **Обработка вложенных структур**
```python
import itertools

# Выравнивание вложенного списка
nested = [[1, 2], [3, 4, 5], [6]]
flat = list(itertools.chain.from_iterable(nested))
# [1, 2, 3, 4, 5, 6]

# Пакетная обработка
def batch_process(data, batch_size=3):
    """Разбивает данные на батчи"""
    it = iter(data)
    while True:
        batch = list(itertools.islice(it, batch_size))
        if not batch:
            break
        yield batch

data = range(10)
for batch in batch_process(data, 3):
    print(batch)
# [0, 1, 2]
# [3, 4, 5]
# [6, 7, 8]
# [9]
```

### 5. 🧮 **Математические вычисления**
```python
import itertools
import operator

# Скользящее среднее
def moving_average(data, window_size=3):
    """Вычисление скользящего среднего"""
    it = iter(data)
    window = list(itertools.islice(it, window_size))
    yield sum(window) / window_size
    
    for value in it:
        window.pop(0)
        window.append(value)
        yield sum(window) / window_size

data = [1, 2, 3, 4, 5, 6, 7, 8, 9]
list(moving_average(data, 3))
# [2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0]

# Все делители числа
def divisors(n):
    """Все делители числа"""
    factors = []
    for i in range(1, int(n**0.5) + 1):
        if n % i == 0:
            factors.append(i)
            if i != n // i:
                factors.append(n // i)
    return sorted(factors)
```

## ⚡ **Оптимизация с itertools**

### 1. 🏎️ **Эффективная обработка больших данных**
```python
import itertools

# Ленивое чтение файла
def read_large_file(file_path):
    with open(file_path, 'r') as f:
        for line in itertools.islice(f, 1000):  # только первые 1000 строк
            yield line.strip()

# Генерация без хранения в памяти
def generate_combinations(elements, r):
    """Генерация комбинаций без хранения в памяти"""
    for combo in itertools.combinations(elements, r):
        yield combo
        # Обрабатываем и забываем - экономия памяти
```

### 2. 🎯 **Частые паттерны**
```python
import itertools

# 1. Все пары элементов
items = ['A', 'B', 'C', 'D']
pairs = list(itertools.combinations(items, 2))
# [('A','B'), ('A','C'), ('A','D'), ('B','C'), ('B','D'), ('C','D')]

# 2. Декартова степень
coordinates = list(itertools.product([0, 1], repeat=3))
# [(0,0,0), (0,0,1), (0,1,0), ..., (1,1,1)]

# 3. Циклический сдвиг
def rotate(iterable):
    """Все циклические сдвиги"""
    n = len(iterable)
    return [iterable[i:] + iterable[:i] for i in range(n)]

# 4. Разбиение на группы
def chunks(iterable, size):
    """Разбивает на группы фиксированного размера"""
    it = iter(iterable)
    while True:
        chunk = list(itertools.islice(it, size))
        if not chunk:
            break
        yield chunk
```

## ⚠️ **Важные предупреждения**

### 1. 🚫 **Бесконечные итераторы**
```python
import itertools

# ❌ ОПАСНО: бесконечный цикл
# for i in itertools.count():
#     print(i)  # никогда не остановится

# ✅ Всегда добавляйте условие выхода
for i in itertools.count():
    if i > 100:
        break
    print(i)
```

### 2. 💾 **Память и производительность**
```python
import itertools

# ❌ Плохо: создает список всех комбинаций
all_combos = list(itertools.combinations(range(100), 3))
# Огромный список в памяти!

# ✅ Хорошо: обрабатываем по одной
for combo in itertools.combinations(range(100), 3):
    process(combo)  # обрабатываем и забываем
```

### 3. 🔄 **Одноразовые итераторы**
```python
import itertools

iterable = [1, 2, 3, 4]

# ❌ tee создает независимые итераторы
a, b = itertools.tee(iterable)
list(a)  # [1, 2, 3, 4]
list(b)  # [1, 2, 3, 4] - работает

# ❌ Но обычный итератор одноразовый
it = iter(iterable)
list(it)  # [1, 2, 3, 4]
list(it)  # [] - ПУСТО!
```

## 🎯 **Когда что использовать**

### 📋 **Быстрая шпаргалка по выбору**

| Задача | Решение | Пример |
|--------|---------|--------|
| **Все комбинации** | `product()` | Пароли, координаты |
| **Уникальные наборы** | `combinations()` | Выбор команды |
| **Упорядоченные наборы** | `permutations()` | Расстановки, анаграммы |
| **С повторами** | `combinations_with_replacement()` | Размен монет |
| **Группировка** | `groupby()` + `sorted()` | Агрегация данных |
| **Цепочка данных** | `chain()` | Объединение списков |
| **Пакетная обработка** | `islice()` | Чтение чанками |
| **Фильтрация** | `filterfalse()`, `compress()` | Условный отбор |
| **Накопление** | `accumulate()` | Сумма, произведение |

### 🔍 **Сравнение с альтернативами**

```python
import itertools

# 1. Вложенные циклы vs product()
# Старый способ (медленнее, много кода):
result = []
for i in range(3):
    for j in range(3):
        for k in range(3):
            result.append((i, j, k))

# Новый способ (быстрее, чище):
result = list(itertools.product(range(3), repeat=3))

# 2. Ручная комбинаторика vs combinations()
# Старый способ:
def manual_combinations(items, r):
    # Сложный рекурсивный код...
    pass

# Новый способ:
result = list(itertools.combinations(items, r))

# 3. Разбиение на чанки вручную vs islice()
# Старый способ:
def chunk_manual(data, size):
    chunks = []
    for i in range(0, len(data), size):
        chunks.append(data[i:i+size])
    return chunks

# Новый способ (ленивый):
def chunk_iter(data, size):
    it = iter(data)
    while True:
        chunk = list(itertools.islice(it, size))
        if not chunk:
            break
        yield chunk
```

## 🧪 **Тестирование и отладка**

### 1. 🔍 **Проверка работы**
```python
import itertools

# Проверка количества элементов
items = ['A', 'B', 'C', 'D']

# Перестановки: P(n, r) = n! / (n-r)!
n, r = len(items), 2
expected = math.factorial(n) // math.factorial(n - r)  # 12
actual = len(list(itertools.permutations(items, r)))
assert actual == expected

# Комбинации: C(n, r) = n! / (r! * (n-r)!)
expected = math.factorial(n) // (math.factorial(r) * math.factorial(n - r))  # 6
actual = len(list(itertools.combinations(items, r)))
assert actual == expected
```

### 2. 🐛 **Распространенные ошибки**
```python
import itertools

# Ошибка 1: забыли отсортировать перед groupby
data = [1, 2, 2, 3, 1, 2, 3, 3]
# ❌ Неправильно:
for key, group in itertools.groupby(data):
    print(key, list(group))
# 1 [1]
# 2 [2, 2]
# 3 [3]
# 1 [1]
# 2 [2]
# 3 [3, 3]

# ✅ Правильно:
for key, group in itertools.groupby(sorted(data)):
    print(key, list(group))
# 1 [1, 1]
# 2 [2, 2, 2]
# 3 [3, 3, 3]

# Ошибка 2: бесконечные итераторы
# ❌ Никогда не завершится:
# infinite = itertools.count()
# list(infinite)  # MemoryError!

# ✅ Ограничиваем:
limited = itertools.islice(itertools.count(), 100)
list(limited)  # ок
```

## 📈 **Производительность**

### 1. ⏱️ **Сравнение скоростей**
```python
import itertools
import timeit

# Генерация всех пар
items = list(range(100))

# Способ 1: Вложенные циклы
def pairs_loop():
    result = []
    for i in range(len(items)):
        for j in range(i+1, len(items)):
            result.append((items[i], items[j]))
    return result

# Способ 2: combinations
def pairs_itertools():
    return list(itertools.combinations(items, 2))

# Замер времени
time_loop = timeit.timeit(pairs_loop, number=10)
time_itertools = timeit.timeit(pairs_itertools, number=10)

print(f"Loop: {time_loop:.3f}s")
print(f"itertools: {time_itertools:.3f}s")
# itertools обычно быстрее и использует меньше памяти!
```

## 🎓 **Продвинутые техники**

### 1. 🧩 **Комбинирование itertools функций**
```python
import itertools

# Сложная обработка данных
def process_data(data, window_size=3, threshold=5):
    """Скользящее окно + фильтрация + группировка"""
    
    # 1. Создаем скользящее окно
    windows = zip(*[itertools.islice(data, i, None) 
                   for i in range(window_size)])
    
    # 2. Фильтруем по среднему значению
    filtered = itertools.filterfalse(
        lambda w: sum(w)/len(w) < threshold,
        windows
    )
    
    # 3. Группируем по первому элементу
    for key, group in itertools.groupby(filtered, key=lambda x: x[0]):
        yield key, list(group)

# Использование
data = [1, 4, 7, 2, 5, 8, 3, 6, 9]
for key, group in process_data(data, 3, 4):
    print(f"{key}: {group}")
```

### 2. 🔄 **Создание собственных итераторов**
```python
import itertools

# Кастомный итератор на основе itertools
def fibonacci_iter():
    """Бесконечный генератор чисел Фибоначчи"""
    a, b = 0, 1
    for _ in itertools.count():
        yield a
        a, b = b, a + b

# Использование
fib = fibonacci_iter()
first_10 = list(itertools.islice(fib, 10))
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# Итератор с состоянием
def sliding_window(iterable, size=3):
    """Скользящее окно с памятью"""
    it = iter(iterable)
    window = list(itertools.islice(it, size))
    if len(window) == size:
        yield tuple(window)
    for elem in it:
        window = window[1:] + [elem]
        yield tuple(window)

list(sliding_window(range(5), 3))
# [(0, 1, 2), (1, 2, 3), (2, 3, 4)]
```

---

## 📌 **Ключевые выводы**

1. **✅ Используйте itertools для:**
   - Комбинаторных задач (перестановки, комбинации)
   - Ленивой обработки больших данных
   - Эффективного объединения итераторов
   - Группировки и фильтрации

2. **❌ Избегайте когда:**
   - Нужны простые циклы (используйте for/while)
   - Данные помещаются в память (используйте списки)
   - Нужны индексы (используйте enumerate)

3. **💡 Помните:**
   - Итераторы одноразовые (кроме tee)
   - Сортируйте перед groupby
   - Ограничивайте бесконечные итераторы
   - itertools экономит память, но не всегда время

## 🔗 **Дополнительные ресурсы**
- [Официальная документация](https://docs.python.org/3/library/itertools.html)
- [Recipes from docs](https://docs.python.org/3/library/itertools.html#itertools-recipes)
- [More-itertools library](https://more-itertools.readthedocs.io/)