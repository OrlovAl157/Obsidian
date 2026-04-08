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

# Памятка: Функция map() в Python

## 💡 Полезные советы

- **map()** применяет функцию к каждому элементу итерируемого объекта
- **Возвращает итератор**: нужно преобразовать в list/tuple/set для использования
- **Ленивые вычисления**: элементы обрабатываются по требованию
- **Несколько итераторов**: можно передать несколько последовательностей
- **Альтернатива**: list comprehension часто читабельнее
- **Для сложной логики**: используйте list comprehension вместо map
- **lambda**: удобно с map для простых операций
- **Производительность**: map быстрее для простых функций

## 🚀 Быстрая справка

|Задача                |Синтаксис                     |Пример                       |
|----------------------|------------------------------|-----------------------------|
|Базовое использование |`map(func, iterable)`         |`map(str, [1,2,3])`          |
|С lambda              |`map(lambda x: x*2, iterable)`|`map(lambda x: x*2, [1,2,3])`|
|Несколько итераторов  |`map(func, iter1, iter2)`     |`map(max, [1,2], [3,4])`     |
|Преобразовать в список|`list(map(...))`              |`list(map(int, ['1','2']))`  |
|С встроенной функцией |`map(int, iterable)`          |`map(int, ['1','2','3'])`    |
|С методом             |`map(str.upper, iterable)`    |`map(str.upper, ['a','b'])`  |

## 📝 Базовое использование

```python
# Синтаксис: map(function, iterable)

# Преобразовать числа в строки
numbers = [1, 2, 3, 4, 5]
result = map(str, numbers)
print(list(result))             # → ['1', '2', '3', '4', '5']

# Преобразовать строки в числа
strings = ['1', '2', '3', '4', '5']
result = map(int, strings)
print(list(result))             # → [1, 2, 3, 4, 5]

# Возвести в квадрат
def square(x):
    return x ** 2

numbers = [1, 2, 3, 4, 5]
result = map(square, numbers)
print(list(result))             # → [1, 4, 9, 16, 25]

# С lambda функцией
numbers = [1, 2, 3, 4, 5]
result = map(lambda x: x * 2, numbers)
print(list(result))             # → [2, 4, 6, 8, 10]

# map возвращает итератор (не список!)
numbers = [1, 2, 3]
result = map(str, numbers)
print(result)                   # → <map object at 0x...>
print(type(result))             # → <class 'map'>
```

## 🔄 Преобразование результата

```python
numbers = [1, 2, 3, 4, 5]

# В список
result = list(map(lambda x: x * 2, numbers))
# → [2, 4, 6, 8, 10]

# В кортеж
result = tuple(map(lambda x: x * 2, numbers))
# → (2, 4, 6, 8, 10)

# В множество
result = set(map(lambda x: x % 2, numbers))
# → {0, 1}

# В строку (через join)
numbers = [1, 2, 3]
result = ', '.join(map(str, numbers))
# → "1, 2, 3"

# Итерация без преобразования
for value in map(lambda x: x * 2, numbers):
    print(value)                # → 2, 4, 6, 8, 10
```

## 🎯 Использование с различными функциями

### Встроенные функции

```python
# str() - преобразование в строку
numbers = [1, 2, 3]
list(map(str, numbers))         # → ['1', '2', '3']

# int() - преобразование в число
strings = ['1', '2', '3']
list(map(int, strings))         # → [1, 2, 3]

# float() - преобразование в float
strings = ['1.5', '2.7', '3.9']
list(map(float, strings))       # → [1.5, 2.7, 3.9]

# abs() - абсолютное значение
numbers = [-1, -2, 3, -4, 5]
list(map(abs, numbers))         # → [1, 2, 3, 4, 5]

# len() - длина
words = ['hi', 'hello', 'world']
list(map(len, words))           # → [2, 5, 5]

# round() - округление
numbers = [1.4, 2.6, 3.8]
list(map(round, numbers))       # → [1, 3, 4]

# bool() - преобразование в булево
values = [0, 1, '', 'text', [], [1]]
list(map(bool, values))         # → [False, True, False, True, False, True]
```

### Методы строк

```python
words = ['hello', 'world', 'python']

# Верхний регистр
list(map(str.upper, words))     # → ['HELLO', 'WORLD', 'PYTHON']

# Нижний регистр
list(map(str.lower, words))     # → ['hello', 'world', 'python']

# Заглавная первая буква
list(map(str.capitalize, words)) # → ['Hello', 'World', 'Python']

# Удалить пробелы
texts = ['  hello  ', '  world  ']
list(map(str.strip, texts))     # → ['hello', 'world']

# Разделить строки
sentences = ['a b', 'c d', 'e f']
list(map(str.split, sentences)) # → [['a', 'b'], ['c', 'd'], ['e', 'f']]
```

### Пользовательские функции

```python
# Одинарная функция
def double(x):
    return x * 2

numbers = [1, 2, 3]
list(map(double, numbers))      # → [2, 4, 6]

# Функция с условием
def is_even(x):
    return x % 2 == 0

numbers = [1, 2, 3, 4, 5]
list(map(is_even, numbers))     # → [False, True, False, True, False]

# Функция с несколькими операциями
def process(x):
    x = x * 2
    x = x + 10
    return x

numbers = [1, 2, 3]
list(map(process, numbers))     # → [12, 14, 16]
```

## 🔢 Несколько итераторов

```python
# map с двумя итераторами
# Функция должна принимать 2 аргумента

# Сложение двух списков
list1 = [1, 2, 3]
list2 = [10, 20, 30]
result = map(lambda x, y: x + y, list1, list2)
print(list(result))             # → [11, 22, 33]

# Умножение
list1 = [1, 2, 3]
list2 = [4, 5, 6]
result = map(lambda x, y: x * y, list1, list2)
print(list(result))             # → [4, 10, 18]

# Максимум из двух
list1 = [1, 5, 3]
list2 = [4, 2, 6]
result = map(max, list1, list2)
print(list(result))             # → [4, 5, 6]

# Минимум из двух
result = map(min, list1, list2)
print(list(result))             # → [1, 2, 3]

# Три итератора
list1 = [1, 2, 3]
list2 = [10, 20, 30]
list3 = [100, 200, 300]
result = map(lambda x, y, z: x + y + z, list1, list2, list3)
print(list(result))             # → [111, 222, 333]

# Разная длина - останавливается на самом коротком
list1 = [1, 2, 3, 4, 5]
list2 = [10, 20]
result = map(lambda x, y: x + y, list1, list2)
print(list(result))             # → [11, 22] (только 2 элемента!)

# Создание кортежей из двух списков
names = ['Alice', 'Bob', 'Charlie']
ages = [25, 30, 35]
result = map(lambda name, age: (name, age), names, ages)
print(list(result))             # → [('Alice', 25), ('Bob', 30), ('Charlie', 35)]
```

## 🆚 map() vs List Comprehension

```python
numbers = [1, 2, 3, 4, 5]

# ========== ПРОСТЫЕ ОПЕРАЦИИ ==========

# С map
result = list(map(lambda x: x * 2, numbers))

# С list comprehension
result = [x * 2 for x in numbers]

# ✅ Оба способа хороши, выбирайте что читабельнее

# ========== С УСЛОВИЕМ ==========

# С map + filter
result = list(map(lambda x: x * 2, filter(lambda x: x % 2 == 0, numbers)))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [x * 2 for x in numbers if x % 2 == 0]

# ✅ List comprehension лучше для условий

# ========== СЛОЖНАЯ ЛОГИКА ==========

# С map (сложно читать)
result = list(map(lambda x: x ** 2 if x % 2 == 0 else x ** 3, numbers))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [x ** 2 if x % 2 == 0 else x ** 3 for x in numbers]

# ✅ List comprehension лучше для сложной логики

# ========== ВСТРОЕННЫЕ ФУНКЦИИ ==========

# С map (ЛУЧШЕ!)
strings = ['1', '2', '3']
result = list(map(int, strings))

# С list comprehension (избыточно)
result = [int(x) for x in strings]

# ✅ map лучше для простых встроенных функций

# ========== ВЛОЖЕННЫЕ СТРУКТУРЫ ==========

# С map (сложно)
matrix = [[1, 2], [3, 4], [5, 6]]
result = list(map(lambda row: list(map(lambda x: x * 2, row)), matrix))

# С list comprehension (ЧИТАБЕЛЬНЕЕ!)
result = [[x * 2 for x in row] for row in matrix]

# ✅ List comprehension лучше для вложенных структур
```

## 🎯 Практические примеры

### Обработка строк

```python
# Очистка списка строк
texts = ['  hello  ', '  world  ', '  python  ']
cleaned = list(map(str.strip, texts))
# → ['hello', 'world', 'python']

# Преобразование в верхний регистр
words = ['apple', 'banana', 'cherry']
upper = list(map(str.upper, words))
# → ['APPLE', 'BANANA', 'CHERRY']

# Получить первый символ
words = ['apple', 'banana', 'cherry']
first_chars = list(map(lambda s: s[0], words))
# → ['a', 'b', 'c']

# Добавить префикс
items = ['apple', 'banana', 'cherry']
prefixed = list(map(lambda s: f"fruit: {s}", items))
# → ['fruit: apple', 'fruit: banana', 'fruit: cherry']
```

### Математические операции

```python
# Округление списка float
prices = [19.99, 29.95, 49.99]
rounded = list(map(round, prices))
# → [20, 30, 50]

# Округление до 2 знаков
prices = [19.999, 29.956, 49.991]
rounded = list(map(lambda x: round(x, 2), prices))
# → [20.0, 29.96, 49.99]

# Преобразование температур
celsius = [0, 10, 20, 30, 100]
fahrenheit = list(map(lambda c: c * 9/5 + 32, celsius))
# → [32.0, 50.0, 68.0, 86.0, 212.0]

# Вычисление процентов
values = [100, 200, 300]
with_tax = list(map(lambda x: x * 1.2, values))
# → [120.0, 240.0, 360.0]
```

### Работа со словарями

```python
# Извлечь значения по ключу
users = [
    {'name': 'Alice', 'age': 25},
    {'name': 'Bob', 'age': 30},
    {'name': 'Charlie', 'age': 35}
]

names = list(map(lambda user: user['name'], users))
# → ['Alice', 'Bob', 'Charlie']

ages = list(map(lambda user: user['age'], users))
# → [25, 30, 35]

# Обновить значения
updated = list(map(lambda user: {**user, 'age': user['age'] + 1}, users))
# → [{'name': 'Alice', 'age': 26}, ...]

# Создать строки
formatted = list(map(lambda u: f"{u['name']}: {u['age']} лет", users))
# → ['Alice: 25 лет', 'Bob: 30 лет', 'Charlie: 35 лет']
```

### Работа с файлами

```python
# Прочитать числа из файла
# Файл numbers.txt содержит:
# 1
# 2
# 3

with open('numbers.txt', 'r') as f:
    numbers = list(map(int, f.readlines()))
# → [1, 2, 3] (с учётом strip)

# Лучше с strip
with open('numbers.txt', 'r') as f:
    numbers = list(map(lambda line: int(line.strip()), f))

# Обработать строки файла
with open('data.txt', 'r') as f:
    lines = list(map(str.strip, f))

# Записать числа в файл
numbers = [1, 2, 3, 4, 5]
with open('output.txt', 'w') as f:
    f.writelines(map(lambda n: f"{n}\n", numbers))
```

### Преобразование типов данных

```python
# CSV строка в список чисел
csv_line = "1,2,3,4,5"
numbers = list(map(int, csv_line.split(',')))
# → [1, 2, 3, 4, 5]

# Список чисел в CSV строку
numbers = [1, 2, 3, 4, 5]
csv_line = ','.join(map(str, numbers))
# → "1,2,3,4,5"

# Список кортежей в список словарей
data = [('Alice', 25), ('Bob', 30), ('Charlie', 35)]
dicts = list(map(lambda t: {'name': t[0], 'age': t[1]}, data))
# → [{'name': 'Alice', 'age': 25}, ...]

# Преобразовать булевы значения
values = [True, False, True, False]
as_int = list(map(int, values))
# → [1, 0, 1, 0]
```

### Работа с вложенными структурами

```python
# Удвоить все числа в матрице
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

# С вложенным map
doubled = list(map(lambda row: list(map(lambda x: x * 2, row)), matrix))
# → [[2, 4, 6], [8, 10, 12], [14, 16, 18]]

# Лучше через list comprehension
doubled = [[x * 2 for x in row] for row in matrix]

# Получить длины всех подсписков
nested = [[1, 2], [3, 4, 5], [6]]
lengths = list(map(len, nested))
# → [2, 3, 1]

# Объединить строки в подсписках
words = [['hello', 'world'], ['python', 'is', 'cool']]
joined = list(map(' '.join, words))
# → ['hello world', 'python is cool']
```

## 🔍 Особенности map()

```python
# map() возвращает итератор (ленивые вычисления)
numbers = [1, 2, 3, 4, 5]
result = map(lambda x: x * 2, numbers)

print(result)                   # → <map object>
print(list(result))             # → [2, 4, 6, 8, 10]
print(list(result))             # → [] (итератор исчерпан!)

# Итератор можно использовать только один раз
result = map(lambda x: x * 2, numbers)
list1 = list(result)            # → [2, 4, 6, 8, 10]
list2 = list(result)            # → [] (пусто!)

# Чтобы использовать повторно, создайте список
result = list(map(lambda x: x * 2, numbers))
list1 = result                  # → [2, 4, 6, 8, 10]
list2 = result                  # → [2, 4, 6, 8, 10]

# Ленивые вычисления - функция не вызывается сразу
def show(x):
    print(f"Processing {x}")
    return x * 2

numbers = [1, 2, 3]
result = map(show, numbers)     # Ничего не печатается!
print("Map created")
list(result)                    # Теперь печатается
# → Processing 1
# → Processing 2
# → Processing 3
```

## ⚠️ Частые ошибки

```python
# ❌ Забыли преобразовать в список
numbers = [1, 2, 3]
result = map(lambda x: x * 2, numbers)
print(result)                   # → <map object> (не список!)

# ✅ Преобразуйте в список
result = list(map(lambda x: x * 2, numbers))
print(result)                   # → [2, 4, 6]

# ❌ Неправильное количество аргументов
list1 = [1, 2, 3]
list2 = [4, 5, 6]
result = map(lambda x: x * 2, list1, list2)  # lambda принимает 1 аргумент!
# TypeError!

# ✅ lambda должна принимать столько же аргументов
result = map(lambda x, y: x + y, list1, list2)

# ❌ Использование итератора дважды
result = map(int, ['1', '2', '3'])
list1 = list(result)
list2 = list(result)            # → [] (пусто!)

# ✅ Создайте список сразу
result = list(map(int, ['1', '2', '3']))
list1 = result
list2 = result                  # → [1, 2, 3]

# ⚠️ Неожиданная длина результата
list1 = [1, 2, 3, 4, 5]
list2 = [10, 20]
result = list(map(lambda x, y: x + y, list1, list2))
print(result)                   # → [11, 22] (только 2 элемента!)

# ✅ Используйте zip_longest для полной длины
from itertools import zip_longest
result = [x + y for x, y in zip_longest(list1, list2, fillvalue=0)]
```

## 🎯 Когда использовать map()

**✅ Используйте map() когда:**

- Применяете встроенную функцию ко всем элементам (`map(int, ...)`)
- Применяете метод строки ко всем элементам (`map(str.upper, ...)`)
- Простое преобразование без условий
- Работаете с несколькими итераторами одновременно
- Нужны ленивые вычисления

**✅ Используйте list comprehension когда:**

- Есть условия (if)
- Сложная логика преобразования
- Нужна читабельность
- Вложенные структуры
- Хотите избежать lambda

```python
# ✅ ХОРОШО для map
list(map(int, ['1', '2', '3']))
list(map(str.upper, ['a', 'b', 'c']))
list(map(abs, [-1, -2, 3]))

# ✅ ЛУЧШЕ list comprehension
[x * 2 for x in numbers if x > 5]
[x ** 2 if x % 2 == 0 else x ** 3 for x in numbers]
[[x * 2 for x in row] for row in matrix]
```

## 🔗 Связанные темы

- [[01 — 🎯 filter()]]
- [[06 — 🎯 reduce()]]
- [[02 — 🎯 sorted()]]
- [[🌊 Lambda функции]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[01 — Итераторы (Iterator)]]
- [[00 — 🎯 Встроенные функции Python]]
