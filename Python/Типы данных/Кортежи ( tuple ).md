💡 Полезные советы

- **Кортежи неизменяемы**: нельзя изменить, добавить или удалить элементы после создания
- **Быстрее списков**: благодаря неизменяемости работают быстрее
- **Можно использовать как ключи**: в словарях (в отличие от списков)
- **Индексация с 0**: первый элемент имеет индекс 0
- **Отрицательные индексы**: `tuple[-1]` - последний элемент
- **Скобки необязательны**: `1, 2, 3` это тоже кортеж
- **Один элемент**: обязательна запятая - `(5,)` не `(5)`
- **Распаковка**: удобный способ присвоения нескольких переменных

## 🚀 Быстрая справка

|Задача|Метод/Оператор|Пример|
|---|---|---|
|Создать кортеж|`()` или `tuple()`|`(1, 2, 3)`|
|Длина|`len()`|`len(my_tuple)`|
|Найти элемент|`.index()`|`my_tuple.index(5)`|
|Посчитать элементы|`.count()`|`my_tuple.count(5)`|
|Проверить наличие|`in`|`5 in my_tuple`|
|Минимум|`min()`|`min(my_tuple)`|
|Максимум|`max()`|`max(my_tuple)`|
|Сумма|`sum()`|`sum(my_tuple)`|
|Срез|`[start:end:step]`|`my_tuple[1:5:2]`|
|Конкатенация|`+`|`tuple1 + tuple2`|
|Повторение|`*`|`my_tuple * 3`|
|Преобразовать в список|`list()`|`list(my_tuple)`|

## 📝 Создание кортежей

```python
# Пустой кортеж
empty = ()
empty = tuple()

# С элементами
numbers = (1, 2, 3, 4, 5)
mixed = (1, "text", True, 3.14)

# Без скобок (tuple packing)
coordinates = 10, 20, 30        # → (10, 20, 30)

# Один элемент (запятая обязательна!)
single = (5,)                   # → (5,) - кортеж
not_tuple = (5)                 # → 5 - просто число

# Из других последовательностей
from_list = tuple([1, 2, 3])   # → (1, 2, 3)
from_string = tuple("hello")    # → ('h', 'e', 'l', 'l', 'o')
from_range = tuple(range(5))    # → (0, 1, 2, 3, 4)

# Вложенные кортежи
nested = ((1, 2), (3, 4), (5, 6))
matrix = (
    (1, 2, 3),
    (4, 5, 6),
    (7, 8, 9)
)
```

## 🔍 Доступ к элементам

```python
fruits = ("apple", "banana", "cherry", "date", "elderberry")

# Индексация
fruits[0]                       # → "apple"
fruits[2]                       # → "cherry"
fruits[-1]                      # → "elderberry" (последний)
fruits[-2]                      # → "date"

# Срезы (slicing)
fruits[1:3]                     # → ("banana", "cherry")
fruits[:3]                      # → ("apple", "banana", "cherry")
fruits[2:]                      # → ("cherry", "date", "elderberry")
fruits[::2]                     # → ("apple", "cherry", "elderberry")
fruits[::-1]                    # → обратный порядок

# Вложенные кортежи
nested = ((1, 2), (3, 4))
nested[0]                       # → (1, 2)
nested[0][1]                    # → 2
nested[1][0]                    # → 3
```

## 🔍 Поиск и проверка

```python
numbers = (1, 2, 3, 2, 4, 2, 5)

# Найти индекс первого вхождения
numbers.index(3)                # → 2
numbers.index(2)                # → 1 (первое вхождение)

# Поиск с начальной позиции
numbers.index(2, 2)             # → 3 (поиск начиная с индекса 2)
numbers.index(2, 4, 7)          # → 5 (поиск в диапазоне [4:7])

# Подсчитать количество вхождений
numbers.count(2)                # → 3
numbers.count(10)               # → 0 (нет таких элементов)

# Проверить наличие элемента
3 in numbers                    # → True
10 in numbers                   # → False
10 not in numbers               # → True

# Длина кортежа
len(numbers)                    # → 7

# Проверка на пустоту
if numbers:                     # → True (кортеж не пустой)
    print("Есть элементы")

empty = ()
if not empty:                   # → True (кортеж пустой)
    print("Кортеж пустой")
```

## 🔢 Математические операции

```python
numbers = (1, 2, 3, 4, 5)

# Минимум и максимум
min(numbers)                    # → 1
max(numbers)                    # → 5

# Сумма
sum(numbers)                    # → 15

# Среднее значение
sum(numbers) / len(numbers)     # → 3.0

# Любой/все элементы True
bool_tuple = (True, True, False)
any(bool_tuple)                 # → True (хотя бы один True)
all(bool_tuple)                 # → False (не все True)

# Сортировка (создаёт новый отсортированный кортеж)
unsorted = (3, 1, 4, 1, 5, 9)
sorted_tuple = tuple(sorted(unsorted))  # → (1, 1, 3, 4, 5, 9)
```

## ➕ Операции с кортежами

```python
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)

# Конкатенация (объединение)
combined = tuple1 + tuple2      # → (1, 2, 3, 4, 5, 6)

# Повторение
repeated = (0,) * 5             # → (0, 0, 0, 0, 0)
repeated = ("ha",) * 3          # → ("ha", "ha", "ha")

# Сравнение кортежей (лексикографическое)
(1, 2, 3) < (1, 2, 4)          # → True
(1, 2, 3) == (1, 2, 3)         # → True
(1, 2) < (1, 2, 0)             # → True (короче, но равные части)
```

## 🎯 Распаковка кортежей

```python
# Базовая распаковка
point = (10, 20, 30)
x, y, z = point                 # → x=10, y=20, z=30

# Обмен значений
a, b = 5, 10
a, b = b, a                     # → a=10, b=5

# Распаковка с остатком (Python 3.5+)
numbers = (1, 2, 3, 4, 5)
first, *rest = numbers          # → first=1, rest=[2,3,4,5]
first, *middle, last = numbers  # → first=1, middle=[2,3,4], last=5
*start, last = numbers          # → start=[1,2,3,4], last=5

# Игнорирование значений
person = ("John", 25, "Engineer")
name, _, profession = person    # → игнорируем возраст

# Вложенная распаковка
nested = ((1, 2), (3, 4))
(a, b), (c, d) = nested        # → a=1, b=2, c=3, d=4

# Возврат нескольких значений из функции
def get_coordinates():
    return 10, 20, 30           # → возвращает кортеж

x, y, z = get_coordinates()     # → распаковка результата
```

## 🔄 Итерация

```python
fruits = ("apple", "banana", "cherry")

# Обычный цикл
for fruit in fruits:
    print(fruit)

# С индексом
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")      # → 0: apple, 1: banana, ...

# С индексом от 1
for i, fruit in enumerate(fruits, start=1):
    print(f"{i}: {fruit}")      # → 1: apple, 2: banana, ...

# Две последовательности одновременно
prices = (100, 200, 150)
for fruit, price in zip(fruits, prices):
    print(f"{fruit}: {price}₽")

# Вложенные кортежи
pairs = ((1, 2), (3, 4), (5, 6))
for a, b in pairs:
    print(f"a={a}, b={b}")
```

## 🔄 Преобразование типов

```python
# Кортеж → Список
my_tuple = (1, 2, 3, 4, 5)
my_list = list(my_tuple)        # → [1, 2, 3, 4, 5]

# Список → Кортеж
my_list = [1, 2, 3, 4, 5]
my_tuple = tuple(my_list)       # → (1, 2, 3, 4, 5)

# Строка → Кортеж
text = "hello"
chars = tuple(text)             # → ('h', 'e', 'l', 'l', 'o')

# Множество → Кортеж
my_set = {3, 1, 2}
my_tuple = tuple(my_set)        # → (1, 2, 3) - отсортировано

# Словарь → Кортеж (только ключи)
my_dict = {"a": 1, "b": 2}
keys = tuple(my_dict)           # → ("a", "b")
keys = tuple(my_dict.keys())    # → то же самое
values = tuple(my_dict.values())  # → (1, 2)
items = tuple(my_dict.items())    # → (("a", 1), ("b", 2))
```

## 🎯 Полезные примеры

### Именованные кортежи (namedtuple)

```python
from collections import namedtuple

# Создание именованного кортежа
Point = namedtuple('Point', ['x', 'y', 'z'])
p = Point(10, 20, 30)

# Доступ по имени
p.x                             # → 10
p.y                             # → 20

# Всё ещё кортеж
p[0]                            # → 10
x, y, z = p                     # → распаковка работает

# Создание нового с изменением полей
p2 = p._replace(x=100)          # → Point(x=100, y=20, z=30)

# Преобразование в словарь
p._asdict()                     # → {'x': 10, 'y': 20, 'z': 30}
```

### Координаты и точки

```python
# 2D координаты
point = (10, 20)
x, y = point

# 3D координаты
point3d = (10, 20, 30)
x, y, z = point3d

# RGB цвета
red = (255, 0, 0)
green = (0, 255, 0)
blue = (0, 0, 255)
```

### Возврат нескольких значений

```python
def divide_with_remainder(a, b):
    quotient = a // b
    remainder = a % b
    return quotient, remainder      # → возвращает кортеж

result = divide_with_remainder(17, 5)  # → (3, 2)
q, r = divide_with_remainder(17, 5)    # → q=3, r=2
```

### Неизменяемые данные

```python
# Конфигурация (не должна меняться)
CONFIG = (
    "localhost",
    8080,
    "/api/v1"
)

HOST, PORT, API_PATH = CONFIG

# Константы
DAYS_OF_WEEK = ("Mon", "Tue", "Wed", "Thu", "Fri", "Sat", "Sun")
RGB_COLORS = (
    (255, 0, 0),    # Red
    (0, 255, 0),    # Green
    (0, 0, 255)     # Blue
)
```

### Кортежи как ключи словаря

```python
# Координаты как ключи
locations = {
    (0, 0): "Origin",
    (1, 0): "East",
    (0, 1): "North",
    (-1, 0): "West",
    (0, -1): "South"
}

print(locations[(1, 0)])        # → "East"

# Составные ключи
grades = {
    ("Alice", "Math"): 90,
    ("Alice", "Physics"): 85,
    ("Bob", "Math"): 95,
    ("Bob", "Physics"): 88
}

print(grades[("Alice", "Math")])  # → 90
```

### Фильтрация и преобразование

```python
numbers = (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

# Фильтрация (создаёт кортеж)
evens = tuple(x for x in numbers if x % 2 == 0)     # → (2, 4, 6, 8, 10)

# Преобразование
squares = tuple(x**2 for x in numbers)              # → (1, 4, 9, 16, ...)
doubled = tuple(x * 2 for x in numbers)             # → (2, 4, 6, 8, ...)

# Через map и filter
evens = tuple(filter(lambda x: x % 2 == 0, numbers))
squares = tuple(map(lambda x: x**2, numbers))
```

### Сравнение версий

```python
version1 = (1, 2, 3)
version2 = (1, 2, 4)
version3 = (1, 3, 0)

version1 < version2             # → True
version2 < version3             # → True

# Функция сравнения версий
def compare_versions(v1, v2):
    if v1 < v2:
        return "старше"
    elif v1 > v2:
        return "новее"
    else:
        return "равны"
```

## ⚠️ Важные отличия от списков

```python
# Кортеж - НЕИЗМЕНЯЕМЫЙ
my_tuple = (1, 2, 3)
# my_tuple[0] = 10            # ❌ TypeError!
# my_tuple.append(4)          # ❌ AttributeError!
# my_tuple.remove(1)          # ❌ AttributeError!

# Список - ИЗМЕНЯЕМЫЙ
my_list = [1, 2, 3]
my_list[0] = 10               # ✅ Работает
my_list.append(4)             # ✅ Работает
my_list.remove(1)             # ✅ Работает

# Чтобы "изменить" кортеж, нужно создать новый
my_tuple = (1, 2, 3)
my_tuple = my_tuple + (4,)    # → (1, 2, 3, 4)
my_tuple = (0,) + my_tuple    # → (0, 1, 2, 3, 4)

# Или преобразовать в список и обратно
my_tuple = (1, 2, 3)
temp_list = list(my_tuple)
temp_list.append(4)
my_tuple = tuple(temp_list)   # → (1, 2, 3, 4)
```

## 🎯 Когда использовать кортежи

**Используйте кортежи когда:**

- Данные не должны изменяться (координаты, RGB цвета, конфигурация)
- Нужно использовать как ключ в словаре
- Возвращаете несколько значений из функции
- Нужна небольшая оптимизация по памяти и скорости
- Хотите защитить данные от случайного изменения

**Используйте списки когда:**

- Нужно добавлять/удалять элементы
- Данные часто изменяются
- Нужны методы вроде `.append()`, `.sort()`, `.remove()`
- Работаете с коллекцией одинаковых элементов

## 🔍 Методы кортежей (всего 2!)

```python
my_tuple = (1, 2, 3, 2, 4, 2, 5)

# Только 2 метода:
my_tuple.count(2)               # → 3 (количество вхождений)
my_tuple.index(3)               # → 2 (индекс первого вхождения)

# Всё остальное - встроенные функции:
len(my_tuple)                   # → 7
min(my_tuple)                   # → 1
max(my_tuple)                   # → 5
sum(my_tuple)                   # → 19
sorted(my_tuple)                # → [1, 2, 2, 2, 3, 4, 5] (список!)
```