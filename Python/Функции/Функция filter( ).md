# Памятка: Функция filter() в Python

## 💡 Полезные советы

- **filter()** отбирает элементы, для которых функция возвращает True
- **Возвращает итератор**: нужно преобразовать в list/tuple/set
- **Ленивые вычисления**: элементы проверяются по требованию
- **lambda**: удобно для простых условий
- **Альтернатива**: list comprehension часто читабельнее
- **None как функция**: удаляет все ложные значения (0, ‘’, [], False, None)
- **Для сложных условий**: используйте list comprehension
- **Производительность**: filter быстрее для простых функций

## 🚀 Быстрая справка

|Задача                 |Синтаксис                            |Пример                                         |
|-----------------------|-------------------------------------|-----------------------------------------------|
|Базовая фильтрация     |`filter(func, iterable)`             |`filter(is_even, [1,2,3])`                     |
|С lambda               |`filter(lambda x: x > 5, iterable)`  |`filter(lambda x: x > 5, [1,5,10])`            |
|Удалить ложные значения|`filter(None, iterable)`             |`filter(None, [0, 1, '', 'a'])` → `[1, 'a']`   |
|Чётные числа           |`filter(lambda x: x % 2 == 0, nums)` |`filter(lambda x: x % 2 == 0, [1,2,3,4])`      |
|Больше N               |`filter(lambda x: x > n, iterable)`  |`filter(lambda x: x > 10, numbers)`            |
|Непустые строки        |`filter(str.strip, strings)`         |`filter(str.strip, ['a', '', 'b'])`            |
|По длине               |`filter(lambda x: len(x) > 3, words)`|`filter(lambda x: len(x) > 3, ['hi', 'hello'])`|
|Преобразовать в список |`list(filter(...))`                  |`list(filter(lambda x: x > 0, [-1, 0, 1]))`    |

## 📝 Базовое использование

```python
# Синтаксис: filter(function, iterable)

# Фильтрация чётных чисел
def is_even(x):
    return x % 2 == 0

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = filter(is_even, numbers)
print(list(result))             # → [2, 4, 6, 8, 10]

# С lambda функцией
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = filter(lambda x: x % 2 == 0, numbers)
print(list(result))             # → [2, 4, 6, 8, 10]

# Фильтрация по условию "больше"
numbers = [1, 5, 10, 15, 20, 25]
result = filter(lambda x: x > 10, numbers)
print(list(result))             # → [15, 20, 25]

# filter возвращает итератор (не список!)
numbers = [1, 2, 3, 4, 5]
result = filter(lambda x: x > 3, numbers)
print(result)                   # → <filter object at 0x...>
print(type(result))             # → <class 'filter'>
```

## 🔄 Преобразование результата

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# В список
result = list(filter(lambda x: x % 2 == 0, numbers))
# → [2, 4, 6, 8, 10]

# В кортеж
result = tuple(filter(lambda x: x % 2 == 0, numbers))
# → (2, 4, 6, 8, 10)

# В множество
result = set(filter(lambda x: x % 2 == 0, numbers))
# → {2, 4, 6, 8, 10}

# Итерация без преобразования
for value in filter(lambda x: x > 5, numbers):
    print(value)                # → 6, 7, 8, 9, 10

# Подсчёт отфильтрованных элементов
count = len(list(filter(lambda x: x > 5, numbers)))
print(count)                    # → 5

# Проверка, есть ли хотя бы один
has_any = any(filter(lambda x: x > 100, numbers))
print(has_any)                  # → False
```

## 🎯 Фильтрация с различными условиями

### Числовые условия

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Чётные
evens = list(filter(lambda x: x % 2 == 0, numbers))
# → [2, 4, 6, 8, 10]

# Нечётные
odds = list(filter(lambda x: x % 2 != 0, numbers))
# → [1, 3, 5, 7, 9]

# Больше 5
greater = list(filter(lambda x: x > 5, numbers))
# → [6, 7, 8, 9, 10]

# Меньше или равно 3
less_equal = list(filter(lambda x: x <= 3, numbers))
# → [1, 2, 3]

# В диапазоне
in_range = list(filter(lambda x: 3 <= x <= 7, numbers))
# → [3, 4, 5, 6, 7]

# Кратные 3
multiples_of_3 = list(filter(lambda x: x % 3 == 0, numbers))
# → [3, 6, 9]

# Положительные
numbers_with_neg = [-5, -3, 0, 2, 4, 6]
positives = list(filter(lambda x: x > 0, numbers_with_neg))
# → [2, 4, 6]
```

### Строковые условия

```python
words = ['hello', 'world', 'python', 'programming', 'code']

# Длина больше 5
long_words = list(filter(lambda s: len(s) > 5, words))
# → ['python', 'programming']

# Начинается с 'p'
starts_with_p = list(filter(lambda s: s.startswith('p'), words))
# → ['python', 'programming']

# Заканчивается на 'o'
ends_with_o = list(filter(lambda s: s.endswith('o'), words))
# → ['hello']

# Содержит 'or'
contains_or = list(filter(lambda s: 'or' in s, words))
# → ['world', 'programming']

# Только буквы (без цифр)
texts = ['hello', 'world123', 'python', 'code42']
only_alpha = list(filter(str.isalpha, texts))
# → ['hello', 'python']

# Только цифры
items = ['123', 'abc', '456', 'def']
only_digits = list(filter(str.isdigit, items))
# → ['123', '456']
```

### Булевы и None значения

```python
# Удалить ложные значения (0, '', [], False, None)
mixed = [0, 1, '', 'hello', [], [1, 2], False, True, None, 'world']
truthy = list(filter(None, mixed))
# → [1, 'hello', [1, 2], True, 'world']

# Это эквивалентно:
truthy = list(filter(lambda x: x, mixed))
truthy = list(filter(bool, mixed))

# Удалить только None
data = [1, None, 2, None, 3]
no_none = list(filter(lambda x: x is not None, data))
# → [1, 2, 3]

# Удалить пустые строки
strings = ['hello', '', 'world', '', 'python']
non_empty = list(filter(None, strings))
# → ['hello', 'world', 'python']

# Или явно
non_empty = list(filter(lambda s: s != '', strings))
non_empty = list(filter(str.strip, strings))  # Убирает пробельные строки тоже
```

## 🆚 filter() vs List Comprehension

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# ========== ПРОСТЫЕ УСЛОВИЯ ==========

# С filter
result = list(filter(lambda x: x % 2 == 0, numbers))

# С list comprehension
result = [x for x in numbers if x % 2 == 0]

# ✅ Оба способа хороши, list comprehension читабельнее

# ========== СЛОЖНЫЕ УСЛОВИЯ ==========

# С filter (сложнее читать)
result = list(filter(lambda x: x % 2 == 0 and x > 5, numbers))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [x for x in numbers if x % 2 == 0 and x > 5]

# ✅ List comprehension лучше для сложных условий

# ========== С ПРЕОБРАЗОВАНИЕМ ==========

# С filter + map (сложно)
result = list(map(lambda x: x * 2, filter(lambda x: x % 2 == 0, numbers)))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [x * 2 for x in numbers if x % 2 == 0]

# ✅ List comprehension лучше когда нужно и фильтровать, и преобразовывать

# ========== ВСТРОЕННЫЕ ФУНКЦИИ ==========

# С filter (ЛУЧШЕ!)
texts = ['hello', 'world123', 'python']
only_alpha = list(filter(str.isalpha, texts))

# С list comprehension (избыточно)
only_alpha = [x for x in texts if x.isalpha()]

# ✅ filter лучше для простых встроенных функций

# ========== ВЛОЖЕННЫЕ УСЛОВИЯ ==========

# С filter (очень сложно)
result = list(filter(lambda x: x % 2 == 0 if x > 5 else x % 3 == 0, numbers))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [x for x in numbers if (x % 2 == 0 if x > 5 else x % 3 == 0)]

# ✅ List comprehension лучше для вложенных условий
```

## 🎯 Фильтрация коллекций

### Фильтрация списка словарей

```python
users = [
    {'name': 'Alice', 'age': 30, 'active': True},
    {'name': 'Bob', 'age': 25, 'active': False},
    {'name': 'Charlie', 'age': 35, 'active': True},
    {'name': 'David', 'age': 28, 'active': False}
]

# Только активные пользователи
active_users = list(filter(lambda u: u['active'], users))
# → [{'name': 'Alice', ...}, {'name': 'Charlie', ...}]

# Возраст больше 28
older_than_28 = list(filter(lambda u: u['age'] > 28, users))
# → [{'name': 'Alice', ...}, {'name': 'Charlie', ...}]

# Имя начинается с 'A'
starts_with_a = list(filter(lambda u: u['name'].startswith('A'), users))
# → [{'name': 'Alice', ...}]

# Несколько условий
result = list(filter(lambda u: u['active'] and u['age'] > 25, users))
# → [{'name': 'Alice', ...}, {'name': 'Charlie', ...}]
```

### Фильтрация кортежей

```python
# Список кортежей
data = [
    ('Alice', 30, 85),
    ('Bob', 25, 90),
    ('Charlie', 35, 75),
    ('David', 28, 95)
]

# Балл больше 80
high_scores = list(filter(lambda x: x[2] > 80, data))
# → [('Alice', 30, 85), ('Bob', 25, 90), ('David', 28, 95)]

# Возраст меньше 30
young = list(filter(lambda x: x[1] < 30, data))
# → [('Bob', 25), ('David', 28)]

# Имя длиннее 5 символов
long_names = list(filter(lambda x: len(x[0]) > 5, data))
# → [('Charlie', 35, 75)]
```

### Фильтрация вложенных списков

```python
# Матрица
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Строки, где есть число больше 5
rows_with_large = list(filter(lambda row: any(x > 5 for x in row), matrix))
# → [[4, 5, 6], [7, 8, 9]]

# Строки, где все числа чётные
all_even_rows = list(filter(lambda row: all(x % 2 == 0 for x in row), matrix))
# → [[4, 6, 8]] (если такая строка есть)

# Строки с суммой больше 10
sum_greater_10 = list(filter(lambda row: sum(row) > 10, matrix))
# → [[4, 5, 6], [7, 8, 9]]
```

## 🎯 Практические примеры

### Очистка данных

```python
# Удалить пустые строки и пробельные строки
lines = ['hello', '  ', 'world', '', '   ', 'python', '\n']
cleaned = list(filter(str.strip, lines))
# → ['hello', 'world', 'python']

# Удалить комментарии
code_lines = [
    'def func():',
    '    # comment',
    '    return 42',
    '# another comment'
]
no_comments = list(filter(lambda line: not line.strip().startswith('#'), code_lines))
# → ['def func():', '    return 42']

# Удалить дубликаты (сохранив порядок через set)
seen = set()
numbers = [1, 2, 2, 3, 3, 3, 4, 4, 5]
unique = list(filter(lambda x: not (x in seen or seen.add(x)), numbers))
# → [1, 2, 3, 4, 5]
```

### Валидация данных

```python
# Проверить email адреса (простая проверка)
emails = ['user@example.com', 'invalid', 'test@test.com', 'bad@']
valid_emails = list(filter(lambda e: '@' in e and '.' in e.split('@')[1], emails))
# → ['user@example.com', 'test@test.com']

# Проверить телефоны (только цифры)
phones = ['1234567890', '123-456', 'abc', '9876543210']
valid_phones = list(filter(lambda p: p.isdigit() and len(p) == 10, phones))
# → ['1234567890', '9876543210']

# Фильтрация корректных чисел
inputs = ['123', '45.6', 'abc', '-78', '0']
valid_numbers = list(filter(lambda s: s.replace('-', '').replace('.', '').isdigit(), inputs))
# → ['123', '45.6', '-78', '0']
```

### Поиск и отбор

```python
# Найти файлы по расширению
files = ['image.jpg', 'doc.txt', 'photo.png', 'data.csv', 'pic.jpg']
images = list(filter(lambda f: f.endswith(('.jpg', '.png')), files))
# → ['image.jpg', 'photo.png', 'pic.jpg']

# Найти чётные числа в диапазоне
numbers = range(1, 21)
evens = list(filter(lambda x: x % 2 == 0, numbers))
# → [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

# Найти слова, содержащие подстроку
words = ['python', 'javascript', 'typescript', 'java', 'rust']
contains_script = list(filter(lambda w: 'script' in w, words))
# → ['javascript', 'typescript']
```

### Работа с файлами

```python
# Фильтрация строк из файла
with open('data.txt', 'r') as f:
    lines = f.readlines()

# Удалить пустые строки
non_empty = list(filter(lambda line: line.strip(), lines))

# Только строки с числами
with_numbers = list(filter(lambda line: any(c.isdigit() for c in line), lines))

# Строки, начинающиеся с '#'
comments = list(filter(lambda line: line.strip().startswith('#'), lines))

# Строки длиннее 80 символов
long_lines = list(filter(lambda line: len(line) > 80, lines))
```

### Комбинирование filter и map

```python
# Фильтровать, затем преобразовать
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Чётные числа, умноженные на 2
result = list(map(lambda x: x * 2, filter(lambda x: x % 2 == 0, numbers)))
# → [4, 8, 12, 16, 20]

# Лучше через list comprehension
result = [x * 2 for x in numbers if x % 2 == 0]

# Извлечь и преобразовать
words = ['hello', 'world', 'python', 'programming']
upper_long = list(map(str.upper, filter(lambda w: len(w) > 5, words)))
# → ['PYTHON', 'PROGRAMMING']

# Через comprehension
upper_long = [w.upper() for w in words if len(w) > 5]
```

### Фильтрация с подсчётом

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Подсчитать отфильтрованные элементы
even_count = len(list(filter(lambda x: x % 2 == 0, numbers)))
print(f"Чётных чисел: {even_count}")  # → Чётных чисел: 5

# Процент отфильтрованных
total = len(numbers)
filtered = len(list(filter(lambda x: x > 5, numbers)))
percentage = (filtered / total) * 100
print(f"{percentage}%")         # → 50.0%

# Статистика
data = [10, 20, 5, 30, 15, 25]
above_15 = list(filter(lambda x: x > 15, data))
print(f"Элементов > 15: {len(above_15)}")
print(f"Среднее: {sum(above_15) / len(above_15)}")
```

## 🔍 Особенности filter()

```python
# filter() возвращает итератор (ленивые вычисления)
numbers = [1, 2, 3, 4, 5]
result = filter(lambda x: x > 3, numbers)

print(result)                   # → <filter object>
print(list(result))             # → [4, 5]
print(list(result))             # → [] (итератор исчерпан!)

# Итератор можно использовать только один раз
result = filter(lambda x: x > 3, numbers)
list1 = list(result)            # → [4, 5]
list2 = list(result)            # → [] (пусто!)

# Чтобы использовать повторно, создайте список
result = list(filter(lambda x: x > 3, numbers))
list1 = result                  # → [4, 5]
list2 = result                  # → [4, 5]

# Ленивые вычисления - функция не вызывается сразу
def check(x):
    print(f"Checking {x}")
    return x > 3

numbers = [1, 2, 3, 4, 5]
result = filter(check, numbers) # Ничего не печатается!
print("Filter created")
list(result)                    # Теперь печатается
# → Checking 1
# → Checking 2
# → Checking 3
# → Checking 4
# → Checking 5

# filter(None, iterable) - удаляет ложные значения
data = [0, 1, '', 'hello', [], [1], False, True, None]
truthy = list(filter(None, data))
# → [1, 'hello', [1], True]

# Это специальный случай filter!
```

## 🔧 Комбинации с другими функциями

```python
# filter + sorted
numbers = [5, 2, 8, 1, 9, 3, 7]
result = sorted(filter(lambda x: x > 4, numbers))
# → [5, 7, 8, 9]

# filter + sum
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
sum_of_evens = sum(filter(lambda x: x % 2 == 0, numbers))
# → 30

# filter + max/min
numbers = [1, 5, 3, 9, 2, 8]
max_even = max(filter(lambda x: x % 2 == 0, numbers))
# → 8

# filter + any/all
numbers = [2, 4, 6, 8]
all_even = all(filter(lambda x: x % 2 == 0, numbers))
# → True (но бессмысленно, лучше: all(x % 2 == 0 for x in numbers))

# Вложенные filter
numbers = range(1, 101)
result = list(filter(
    lambda x: x % 5 == 0,
    filter(lambda x: x % 2 == 0, numbers)
))
# → [10, 20, 30, 40, 50, 60, 70, 80, 90, 100]

# Лучше через один filter
result = list(filter(lambda x: x % 2 == 0 and x % 5 == 0, numbers))
```

## ⚠️ Частые ошибки

```python
# ❌ Забыли преобразовать в список
numbers = [1, 2, 3, 4, 5]
result = filter(lambda x: x > 3, numbers)
print(result)                   # → <filter object> (не список!)

# ✅ Преобразуйте в список
result = list(filter(lambda x: x > 3, numbers))
print(result)                   # → [4, 5]

# ❌ Использование итератора дважды
result = filter(lambda x: x > 3, [1, 2, 3, 4, 5])
list1 = list(result)
list2 = list(result)            # → [] (пусто!)

# ✅ Создайте список сразу
result = list(filter(lambda x: x > 3, [1, 2, 3, 4, 5]))
list1 = result
list2 = result                  # → [4, 5]

# ❌ Изменение значений вместо фильтрации
numbers = [1, 2, 3, 4, 5]
# filter НЕ изменяет значения, только отбирает!
result = filter(lambda x: x * 2, numbers)  # Неправильно!

# ✅ Используйте map для изменения значений
result = map(lambda x: x * 2, numbers)

# ⚠️ Путаница с None
data = [0, 1, 2, None, 3]
result = list(filter(lambda x: x, data))
# → [1, 2, 3] (0 тоже удалён!)

# ✅ Явно проверяйте на None
result = list(filter(lambda x: x is not None, data))
# → [0, 1, 2, 3]

# ❌ Забыли про короткое замыкание
words = ['hello', '', 'world']
# Ошибка если строка пустая!
# result = list(filter(lambda w: w[0] == 'h', words))  # IndexError!

# ✅ Проверяйте длину
result = list(filter(lambda w: len(w) > 0 and w[0] == 'h', words))
# или
result = list(filter(lambda w: w and w[0] == 'h', words))
```

## 🎯 Когда использовать filter()

**Используйте filter() когда:**

- Простое условие с встроенной функцией (`filter(str.isdigit, ...)`)
- Удаление ложных значений (`filter(None, ...)`)
- Работаете с итераторами (ленивые вычисления)
- Функциональный стиль программирования
- Производительность критична для простых условий

**Используйте list comprehension когда:**

- Сложные условия
- Нужна читабельность
- Фильтрация с преобразованием одновременно
- Вложенные условия
- Общепринятый стиль в проекте

```python
# ✅ ХОРОШО для filter
list(filter(str.isalpha, words))
list(filter(None, data))
list(filter(lambda x: x > 0, numbers))

# ✅ ЛУЧШЕ list comprehension
[x for x in numbers if x > 0 and x % 2 == 0]
[w for w in words if len(w) > 5 and w.startswith('p')]
[x * 2 for x in numbers if x > 0]  # фильтрация + преобразование
```