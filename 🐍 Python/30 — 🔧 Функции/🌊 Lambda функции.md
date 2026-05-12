---
tags:
  - python
  - тема/функции
  - статус/завершён
уровень: начальный
---

# Памятка по лямбда-функциям в Python

## Пример для быстрой справки

```python
# Обычная функция
def square(x):
    return x ** 2

# Лямбда-функция (то же самое)
square = lambda x: x ** 2

print(square(5))  # 25
```

## Быстрая справка

|Конструкция                  |Синтаксис                  |Описание             |
|-----------------------------|---------------------------|---------------------|
|**Без параметров**           |`lambda: выражение`        |Возвращает значение  |
|**Один параметр**            |`lambda x: выражение`      |Один аргумент        |
|**Несколько параметров**     |`lambda x, y: выражение`   |Через запятую        |
|**С значениями по умолчанию**|`lambda x, y=10: выражение`|Как в обычной функции|
|**Немедленный вызов**        |`(lambda x: x * 2)(5)`     |Результат: 10        |

## Что такое лямбда-функция?

**Лямбда-функция (анонимная функция)** — это компактный способ создания функции в одну строку с помощью ключевого слова `lambda`.

### Синтаксис

```python
lambda параметры: выражение
```

- **Параметры** — через запятую, без скобок
- **Выражение** — одно выражение (не несколько строк)
- **Возврат** — автоматически возвращает результат выражения

### Сравнение с обычной функцией

```python
# Обычная функция
def add(x, y):
    return x + y

# Лямбда-функция
add = lambda x, y: x + y

print(add(3, 5))  # 8 (оба варианта)
```

## Основные примеры

### Без параметров

```python
f = lambda: 17
print(f())  # 17
```

### Один параметр

```python
square = lambda x: x ** 2
print(square(5))  # 25

double = lambda x: x * 2
print(double(7))  # 14
```

### Два параметра

```python
add = lambda x, y: x + y
print(add(3, 5))  # 8

power = lambda x, y: x ** y
print(power(2, 3))  # 8
```

### Три параметра

```python
volume = lambda x, y, z: x * y * z
print(volume(2, 3, 4))  # 24
```

### Значения по умолчанию

```python
multiply = lambda x, y=10: x * y

print(multiply(5))      # 50 (5 * 10)
print(multiply(5, 3))   # 15 (5 * 3)
```

## Практические примеры

### С функцией sorted()

```python
# Сортировка по длине строки
words = ['Python', 'is', 'awesome']
sorted_words = sorted(words, key=lambda x: len(x))
print(sorted_words)  # ['is', 'Python', 'awesome']

# Сортировка списка кортежей по второму элементу
pairs = [(1, 5), (3, 2), (2, 8)]
sorted_pairs = sorted(pairs, key=lambda x: x[1])
print(sorted_pairs)  # [(3, 2), (1, 5), (2, 8)]

# Сортировка словарей по значению
students = [
    {'name': 'John', 'grade': 85},
    {'name': 'Jane', 'grade': 92},
    {'name': 'Dave', 'grade': 78}
]
sorted_students = sorted(students, key=lambda x: x['grade'], reverse=True)
print(sorted_students[0])  # {'name': 'Jane', 'grade': 92}
```

### С функцией map()

```python
numbers = [1, 2, 3, 4, 5]

# Возведение в квадрат
squares = list(map(lambda x: x ** 2, numbers))
print(squares)  # [1, 4, 9, 16, 25]

# Умножение на 10
tens = list(map(lambda x: x * 10, numbers))
print(tens)  # [10, 20, 30, 40, 50]

# Два списка - сложение элементов
list1 = [1, 2, 3]
list2 = [10, 20, 30]
result = list(map(lambda x, y: x + y, list1, list2))
print(result)  # [11, 22, 33]
```

### С функцией filter()

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Только четные числа
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4, 6, 8, 10]

# Числа больше 5
greater = list(filter(lambda x: x > 5, numbers))
print(greater)  # [6, 7, 8, 9, 10]

# Строки длиннее 3 символов
words = ['hi', 'hello', 'bye', 'world']
long_words = list(filter(lambda x: len(x) > 3, words))
print(long_words)  # ['hello', 'world']
```

### С функцией reduce()

```python
from functools import reduce

numbers = [1, 2, 3, 4, 5]

# Сумма всех чисел
total = reduce(lambda x, y: x + y, numbers)
print(total)  # 15

# Произведение всех чисел
product = reduce(lambda x, y: x * y, numbers)
print(product)  # 120

# Максимальное значение
maximum = reduce(lambda x, y: x if x > y else y, numbers)
print(maximum)  # 5
```

### С функцией max() и min()

```python
numbers = [5, 2, 8, 1, 9, 3]

# Максимальное по модулю
max_abs = max(numbers, key=lambda x: abs(x - 5))
print(max_abs)  # 1 (дальше всего от 5)

# Строка с максимальной длиной
words = ['hi', 'hello', 'world']
longest = max(words, key=lambda x: len(x))
print(longest)  # 'hello'
```

## Немедленный вызов (IIFE)

```python
# Вызываем лямбду сразу после создания
result = (lambda x, y: x + y)(3, 5)
print(result)  # 8

# Более сложный пример
result = (lambda x: x ** 2 if x > 0 else 0)(-5)
print(result)  # 0
```

## Условные выражения в лямбда

```python
# Тернарный оператор
absolute = lambda x: x if x >= 0 else -x
print(absolute(-5))   # 5
print(absolute(3))    # 3

# Проверка четности
is_even = lambda x: "Четное" if x % 2 == 0 else "Нечетное"
print(is_even(4))  # Четное
print(is_even(7))  # Нечетное

# Максимум из двух чисел
maximum = lambda x, y: x if x > y else y
print(maximum(10, 5))  # 10
```

## Лямбда в списковых выражениях

```python
# Список лямбда-функций
operations = [
    lambda x: x + 1,
    lambda x: x * 2,
    lambda x: x ** 2
]

number = 5
for op in operations:
    print(op(number))
# 6
# 10
# 25
```

## Лямбда как аргумент функции

```python
def apply_operation(x, y, operation):
    """Применяет операцию к двум числам"""
    return operation(x, y)

# Используем разные операции
print(apply_operation(10, 5, lambda x, y: x + y))   # 15
print(apply_operation(10, 5, lambda x, y: x - y))   # 5
print(apply_operation(10, 5, lambda x, y: x * y))   # 50
print(apply_operation(10, 5, lambda x, y: x / y))   # 2.0
```

## Лямбда как возвращаемое значение

```python
def create_multiplier(n):
    """Возвращает функцию, умножающую на n"""
    return lambda x: x * n

# Создаем функции
double = create_multiplier(2)
triple = create_multiplier(3)

print(double(5))  # 10
print(triple(5))  # 15

# Или сразу
print(create_multiplier(4)(5))  # 20
```

## Когда использовать лямбда?

### ✅ Используйте когда:

1. **Однократное использование функции**
   
   ```python
   sorted(words, key=lambda x: len(x))
   ```
2. **Простая логика в одно выражение**
   
   ```python
   double = lambda x: x * 2
   ```
3. **Передача функции как аргумента**
   
   ```python
   map(lambda x: x ** 2, numbers)
   ```
4. **Функция возвращает функцию**
   
   ```python
   return lambda x: x * n
   ```

### ❌ Не используйте когда:

1. **Сложная логика**
   
   ```python
   # ❌ Плохо
   complex = lambda x: x * 2 if x > 0 else -x if x < 0 else 0
   
   # ✅ Хорошо
   def complex(x):
       if x > 0:
           return x * 2
       elif x < 0:
           return -x
       else:
           return 0
   ```
2. **Нужна документация**
   
   ```python
   # ❌ Плохо - нет docstring
   calculate = lambda x, y: x * y + x - y
   
   # ✅ Хорошо
   def calculate(x, y):
       """Вычисляет x*y + x - y"""
       return x * y + x - y
   ```
3. **Многократное использование**
   
   ```python
   # ❌ Плохо
   for i in range(100):
       result = (lambda x: x ** 2)(i)
   
   # ✅ Хорошо
   def square(x):
       return x ** 2
   
   for i in range(100):
       result = square(i)
   ```

## Ограничения лямбда-функций

### ❌ Нельзя использовать:

1. **Несколько выражений**
   
   ```python
   # ❌ Не работает
   f = lambda x: print(x); return x * 2
   ```
2. **Инструкции (if, for, while как блоки)**
   
   ```python
   # ❌ Не работает
   f = lambda x:
       if x > 0:
           return x
   ```
3. **Присваивания**
   
   ```python
   # ❌ Не работает
   f = lambda x: y = x * 2; y
   ```
4. **Аннотации типов**
   
   ```python
   # ❌ Не работает
   f = lambda x: int -> int: x * 2
   ```

### ✅ Можно использовать:

1. **Тернарный оператор**
   
   ```python
   f = lambda x: x if x > 0 else 0
   ```
2. **Вызовы функций**
   
   ```python
   f = lambda x: abs(x) + round(x)
   ```
3. **Логические операции**
   
   ```python
   f = lambda x, y: x and y or x
   ```

## Сравнение: def vs lambda

|Критерий          |def          |lambda       |
|------------------|-------------|-------------|
|**Синтаксис**     |Многострочный|Однострочный |
|**Имя**           |Обязательно  |Необязательно|
|**return**        |Явный        |Неявный      |
|**Документация**  |Docstring    |Нет          |
|**Сложная логика**|✅ Да         |❌ Нет        |
|**Выражение**     |Много        |Одно         |
|**Читаемость**    |✅ Высокая    |⚠️ Средняя    |
|**Отладка**       |✅ Удобно     |⚠️ Сложнее    |

## Распространенные ошибки

### ❌ Ошибка 1: Сложная лямбда

```python
# ❌ Плохо - нечитаемо
result = lambda x: x * 2 if x > 0 else x / 2 if x < 0 else 0

# ✅ Хорошо - обычная функция
def process(x):
    if x > 0:
        return x * 2
    elif x < 0:
        return x / 2
    else:
        return 0
```

### ❌ Ошибка 2: Лямбда в цикле с замыканием

```python
# ❌ Плохо - все функции вернут 4
funcs = []
for i in range(5):
    funcs.append(lambda: i)

print([f() for f in funcs])  # [4, 4, 4, 4, 4]

# ✅ Хорошо - используем значение по умолчанию
funcs = []
for i in range(5):
    funcs.append(lambda x=i: x)

print([f() for f in funcs])  # [0, 1, 2, 3, 4]
```

### ❌ Ошибка 3: Присваивание лямбде имени без причины

```python
# ❌ Плохо - зачем лямбда?
square = lambda x: x ** 2

# ✅ Хорошо - обычная функция
def square(x):
    return x ** 2
```

## Полезные советы

1. ✅ **Используйте для простых операций**
   
   ```python
   sorted(data, key=lambda x: x['age'])
   ```
2. ✅ **Читаемость важнее краткости**
   
   ```python
   # Если непонятно — используйте def
   ```
3. ✅ **Лямбда для функций высшего порядка**
   
   ```python
   map(lambda x: x * 2, numbers)
   filter(lambda x: x > 0, numbers)
   ```
4. ❌ **Не злоупотребляйте**
   
   ```python
   # Если сложно — def лучше
   ```

## Реальные примеры

### Обработка данных

```python
users = [
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25},
    {'name': 'Charlie', 'age': 35}
]

# Сортировка по возрасту
sorted_users = sorted(users, key=lambda x: x['age'])

# Фильтрация
adults = list(filter(lambda x: x['age'] >= 30, users))

# Извлечение имен
names = list(map(lambda x: x['name'], users))
```

### Математические операции

```python
numbers = [1, 2, 3, 4, 5]

# Сумма квадратов
sum_of_squares = sum(map(lambda x: x ** 2, numbers))

# Четные числа
evens = list(filter(lambda x: x % 2 == 0, numbers))
```

## Полезные ссылки

- [Документация: Lambda expressions](https://docs.python.org/3/reference/expressions.html#lambda)
- [PEP 8 — Lambda](https://www.python.org/dev/peps/pep-0008/#programming-recommendations)

## 🔗 Связанные темы

- [[00 — 🎯 map()]]
- [[01 — 🎯 filter()]]
- [[02 — 🎯 sorted()]]
- [[00 — Декоратор]]
- [[00 — Все про замыкание]]
- [[01 — Чистая функция]]
