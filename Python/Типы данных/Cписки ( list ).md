## 💡 Полезные советы

- **Списки изменяемы**: методы вроде `append()`, `sort()` меняют сам список
- **Методы vs функции**: `list.sort()` меняет список, `sorted(list)` создаёт новый
- **Индексация с 0**: первый элемент имеет индекс 0
- **Отрицательные индексы**: `list[-1]` - последний элемент
- **Срезы создают копии**: `list[:]` создаёт поверхностную копию
- **Проверка пустоты**: используйте `if list:` вместо `if len(list) > 0`
- **List comprehension**: быстрее и читабельнее циклов для простых операций

## 🚀 Быстрая справка

| Задача              | Метод/Оператор      | Пример              |
| ------------------- | ------------------- | ------------------- |
| Добавить элемент    | `.append()`         | `list.append(5)`    |
| Вставить элемент    | `.insert()`         | `list.insert(0, 5)` |
| Удалить по значению | `.remove()`         | `list.remove(5)`    |
| Удалить по индексу  | `.pop()`            | `list.pop(0)`       |
| Найти элемент       | `.index()`          | `list.index(5)`     |
| Посчитать элементы  | `.count()`          | `list.count(5)`     |
| Отсортировать       | `.sort()`           | `list.sort()`       |
| Перевернуть         | `.reverse()`        | `list.reverse()`    |
| Длина списка        | `len()`             | `len(list)`         |
| Копировать          | `.copy()` или `[:]` | `list.copy()`       |
| Очистить            | `.clear()`          | `list.clear()`      |
| Срез                | `[start:end:step]`  | `list[1:5:2]`       |

## 📝 Создание списков

```python
# Пустой список
empty = []
empty = list()

# С элементами
numbers = [1, 2, 3, 4, 5]
mixed = [1, "text", True, 3.14]

# Через range
numbers = list(range(5))        # → [0, 1, 2, 3, 4]
numbers = list(range(1, 6))     # → [1, 2, 3, 4, 5]

# List comprehension
squares = [x**2 for x in range(5)]      # → [0, 1, 4, 9, 16]
evens = [x for x in range(10) if x % 2 == 0]  # → [0, 2, 4, 6, 8]
```

## ➕ Добавление элементов

```python
fruits = ["apple", "banana"]

# Добавить в конец
fruits.append("cherry")         # → ["apple", "banana", "cherry"]

# Вставить по индексу
fruits.insert(1, "orange")      # → ["apple", "orange", "banana", "cherry"]

# Добавить несколько элементов
fruits.extend(["mango", "kiwi"])        # → [..., "mango", "kiwi"]
fruits += ["grape", "melon"]            # → то же самое

# Объединение списков
list1 = [1, 2, 3]
list2 = [4, 5, 6]
combined = list1 + list2                # → [1, 2, 3, 4, 5, 6]
```

## ➖ Удаление элементов

```python
fruits = ["apple", "banana", "cherry", "banana"]

# Удалить по значению (первое вхождение)
fruits.remove("banana")         # → ["apple", "cherry", "banana"]

# Удалить по индексу
fruits.pop(0)                   # → "apple" (возвращает элемент)
fruits.pop()                    # → удаляет последний элемент

# Удалить по индексу без возврата
del fruits[0]
del fruits[1:3]                 # → удалить срез

# Очистить весь список
fruits.clear()                  # → []
fruits = []                     # → альтернатива
```

## 🔍 Поиск и проверка

```python
numbers = [1, 2, 3, 2, 4, 2, 5]

# Найти индекс
numbers.index(3)                # → 2 (первое вхождение)
numbers.index(2, 2)             # → 3 (поиск начиная с индекса 2)

# Подсчитать вхождения
numbers.count(2)                # → 3

# Проверить наличие
3 in numbers                    # → True
10 in numbers                   # → False
10 not in numbers               # → True

# Длина списка
len(numbers)                    # → 7

# Проверка пустоты
if numbers:                     # → True (список не пустой)
    print("Есть элементы")
```

## 🔄 Сортировка и изменение порядка

```python
numbers = [3, 1, 4, 1, 5, 9, 2]

# Сортировка на месте
numbers.sort()                  # → [1, 1, 2, 3, 4, 5, 9]
numbers.sort(reverse=True)      # → [9, 5, 4, 3, 2, 1, 1]

# Сортировка с созданием нового списка
sorted_nums = sorted(numbers)   # → новый отсортированный список
original_nums = numbers         # → исходный не изменился

# Реверс на месте
numbers.reverse()               # → [2, 9, 5, 1, 4, 1, 3]

# Реверс с созданием нового списка
reversed_nums = numbers[::-1]   # → новый список в обратном порядке
reversed_nums = list(reversed(numbers))  # → альтернатива

# Сортировка по ключу
words = ["banana", "pie", "Washington", "book"]
words.sort(key=len)             # → по длине: ["pie", "book", "banana", "Washington"]
words.sort(key=str.lower)       # → по алфавиту без учёта регистра
```

## 📊 Копирование списков

```python
original = [1, 2, 3, 4, 5]

# Поверхностная копия
copy1 = original.copy()
copy2 = original[:]
copy3 = list(original)

# Важно! Присваивание НЕ создаёт копию
reference = original            # → та же ссылка, не копия!
reference.append(6)             # → изменит и original

# Глубокая копия (для вложенных списков)
import copy
nested = [[1, 2], [3, 4]]
deep = copy.deepcopy(nested)
```

## 🎯 Срезы (Slicing)

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

# Базовые срезы
numbers[2:5]                    # → [2, 3, 4]
numbers[:3]                     # → [0, 1, 2] (с начала)
numbers[7:]                     # → [7, 8, 9] (до конца)
numbers[:]                      # → копия всего списка

# С шагом
numbers[::2]                    # → [0, 2, 4, 6, 8] (каждый второй)
numbers[1::2]                   # → [1, 3, 5, 7, 9]
numbers[::-1]                   # → [9, 8, 7, ...] (реверс)

# Отрицательные индексы
numbers[-3:]                    # → [7, 8, 9] (последние 3)
numbers[:-2]                    # → [0, 1, 2, ..., 7] (без последних 2)

# Изменение через срезы
numbers[2:5] = [20, 30, 40]     # → заменить элементы
numbers[2:5] = []               # → удалить элементы
```

## 🔢 Математические операции

```python
numbers = [1, 2, 3, 4, 5]

# Минимум и максимум
min(numbers)                    # → 1
max(numbers)                    # → 5

# Сумма
sum(numbers)                    # → 15

# Любой/все элементы True
bool_list = [True, True, False]
any(bool_list)                  # → True (хотя бы один True)
all(bool_list)                  # → False (не все True)

# Умножение списка
[0] * 5                         # → [0, 0, 0, 0, 0]
["ha"] * 3                      # → ["ha", "ha", "ha"]
```

## 🔄 Итерация и преобразование

```python
fruits = ["apple", "banana", "cherry"]

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
prices = [100, 200, 150]
for fruit, price in zip(fruits, prices):
    print(f"{fruit}: {price}₽")

# Map и filter
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x*2, numbers))     # → [2, 4, 6, 8, 10]
evens = list(filter(lambda x: x%2==0, numbers)) # → [2, 4]
```

## 📦 Вложенные списки (Матрицы)

```python
# Создание матрицы 3x3
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Доступ к элементам
matrix[0][0]                    # → 1 (первая строка, первый столбец)
matrix[1][2]                    # → 6

# Создание через comprehension
matrix = [[0 for _ in range(3)] for _ in range(3)]  # → 3x3 нулей

# НЕПРАВИЛЬНО! (все строки - одна ссылка)
wrong_matrix = [[0] * 3] * 3    # → НЕ ДЕЛАЙТЕ ТАК!

# Перебор
for row in matrix:
    for item in row:
        print(item, end=" ")
    print()
```

## 🎯 Распаковка списков

```python
numbers = [1, 2, 3, 4, 5]

# Базовая распаковка
a, b, c, d, e = numbers         # → a=1, b=2, c=3, d=4, e=5

# С остатком
first, *rest = numbers          # → first=1, rest=[2,3,4,5]
first, *middle, last = numbers  # → first=1, middle=[2,3,4], last=5
*start, last = numbers          # → start=[1,2,3,4], last=5

# Обмен значений
a, b = 1, 2
a, b = b, a                     # → a=2, b=1
```

## 🔧 Полезные примеры

### Удаление дубликатов

```python
numbers = [1, 2, 2, 3, 3, 3, 4]
unique = list(set(numbers))             # → [1, 2, 3, 4] (порядок не сохранён)

# С сохранением порядка
unique = list(dict.fromkeys(numbers))   # → [1, 2, 3, 4]
```

### Сглаживание вложенных списков

```python
nested = [[1, 2], [3, 4], [5, 6]]
flat = [item for sublist in nested for item in sublist]  # → [1,2,3,4,5,6]
```

### Разделение списка на части

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
chunk_size = 3
chunks = [numbers[i:i+chunk_size] for i in range(0, len(numbers), chunk_size)]
# → [[1,2,3], [4,5,6], [7,8,9]]
```

### Фильтрация None и пустых значений

```python
mixed = [1, None, 2, "", 3, 0, False, 4]
filtered = [x for x in mixed if x]              # → [1, 2, 3, 4]
no_none = [x for x in mixed if x is not None]   # → [1, 2, '', 3, 0, False, 4]
```

### Поиск индексов всех вхождений

```python
numbers = [1, 2, 3, 2, 4, 2, 5]
indices = [i for i, x in enumerate(numbers) if x == 2]  # → [1, 3, 5]
```

### Транспонирование матрицы

```python
matrix = [[1, 2, 3], [4, 5, 6]]
transposed = list(zip(*matrix))         # → [(1,4), (2,5), (3,6)]
transposed = [list(row) for row in zip(*matrix)]  # → [[1,4], [2,5], [3,6]]
```

## ⚡ Цепочки операций

```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Сложная обработка
result = [x**2 for x in numbers if x % 2 == 0]  # → [4, 16, 36, 64, 100]

# Множественные преобразования
data = ["  hello  ", "  WORLD  ", "  Python  "]
cleaned = [s.strip().lower().capitalize() for s in data]
# → ["Hello", "World", "Python"]
```


```
# Пример 1: Вставка в середину
letters = ['a', 'b', 'c']
letters[2:2] = ['d', 'e', 'f']
print(letters)  # ['a', 'b', 'd', 'e', 'f', 'c']

# Пример 2: Вставка в начало
letters = ['a', 'b', 'c']
letters[0:0] = ['d', 'e', 'f']
print(letters)  # ['d', 'e', 'f', 'a', 'b', 'c']

# Пример 3: Ошибка с append
numbers = [1, 2, 3, 4]
numbers = numbers.append(5)  # ⚠️ ОШИБКА!
print(numbers)  # None

# Пример 4: Правильное использование append
numbers = [1, 2, 3, 4]
numbers.append(5)
print(numbers)  # [1, 2, 3, 4, 5]

# Пример 5: Замена элемента на список
letters = ['a', 'b', 'c', 'd']
letters[3] = ['d', 'e', 'f', 'g']
print(letters)  # ['a', 'b', 'c', ['d', 'e', 'f', 'g']]

# Пример 6: Вложенные списки
letters = ['a', 'b', 'c']
numbers = [1, 2, 3]
combine = [letters, numbers]
print(combine[1])  # [1, 2, 3]

# Пример 7: Удаление через срез
letters = ['a', 'b', 'c', 'd']
letters[1:3] = []
print(letters)  # ['a', 'd']

# Пример 8: Замена одного элемента на несколько
letters = ['a', 'b', 'c']
letters[1:2] = ['x', 'y', 'z']
print(letters)  # ['a', 'x', 'y', 'z', 'c']

# Пример 9: Очистка списка
letters = ['a', 'b', 'c']
letters[:] = []
print(letters)  # []

# Пример 10: Удвоение списка
letters = ['a', 'b', 'c']
letters *= 2
print(letters)  # ['a', 'b', 'c', 'a', 'b', 'c']

# Пример 11: Склейка списков (новый список)
letters = ['a', 'b']
numbers = [1, 2]
result = letters + numbers
print(result)  # ['a', 'b', 1, 2]

# Пример 12: Добавление списка на месте (extend)
letters = ['a', 'b']
numbers = [1, 2]
letters.extend(numbers)
print(letters)  # ['a', 'b', 1, 2]

# Пример 13: Добавление через + (создаётся новый список)
letters = ['a', 'b']
letters = letters + ['c']
print(letters)  # ['a', 'b', 'c']

# Пример 14: Добавление через += (изменяется тот же список)
letters = ['a', 'b']
letters += ['c']
print(letters)  # ['a', 'b', 'c']

# Пример 15: Повтор элементов
numbers = [1, 2]
result = numbers * 3
print(result)  # [1, 2, 1, 2, 1, 2]

# Пример 16: Замена последнего элемента
letters = ['a', 'b', 'c']
letters[-1:] = ['x', 'y']
print(letters)  # ['a', 'b', 'x', 'y']

# Пример 17: Вставка в конец через срез
letters = ['a', 'b', 'c']
letters[3:3] = ['d', 'e']
print(letters)  # ['a', 'b', 'c', 'd', 'e']

# Пример 18: Замена нескольких элементов на один
letters = ['a', 'b', 'c', 'd']
letters[1:3] = ['x']
print(letters)  # ['a', 'x', 'd']
```