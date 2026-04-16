---
tags:
  - python
  - тема/типы_данных
  - тема/коллекции
  - статус/завершён
уровень: начальный
изменяемый: true
хешируемый: false
dict_key: false
упорядоченный: false
итерируемый: true
индексируемый: false
---

# ⚡ Множества (set)

## 📌 Коротко

> Неупорядоченная коллекция уникальных элементов. Автоматически удаляет дубликаты, быстрый поиск O(1), математические операции над множествами.

---

```python
# ⚡ МНОЖЕСТВО (set)
{
    ключ: состояние_ячейки,  # ← легковесный флаг!
    ключ: состояние_ячейки
}
# Память: (hash, key, state) - минимально!

- Хранит: только ключи + флаги состояний
- Порядок: не сохраняется (нет связных списков)
- Память: меньше (только флаги вместо значений)
- Поиск: O(1) в среднем случае
- Состояния: UNUSED, ACTIVE, DUMMY
```

### 💡 Ключевые особенности

- **Уникальность**: Автоматически удаляет дубликаты
- **Неупорядоченность**: Порядок элементов не гарантируется
- **Изменяемость**: Можно добавлять и удалять элементы
- **Только хешируемые типы**: Нельзя хранить списки или словари
- **Быстрый поиск**: Проверка наличия элемента за O(1)

### 💡 Полезные советы

- Множества изменяемы: можно добавлять/удалять элементы
- Только неизменяемые элементы: числа, строки, кортежи (но не списки)
- Быстрый поиск: `x in set` работает за O(1)
- `frozenset`: неизменяемая версия множества
- Set comprehension: `{x for x in ...}`
- Порядок не сохраняется — нет индексации

---

## 🚀 Быстрая справка

| Задача               | Метод/Оператор                    | Сложность   | Пример             |
| -------------------- | --------------------------------- | ----------- | ------------------ |
| Добавить элемент     | `.add(x)`                         | O(1)        | `s.add(5)`         |
| Добавить несколько   | `.update(iter)`                   | O(k)        | `s.update([4, 5])` |
| Удалить (с ошибкой)  | `.remove(x)`                      | O(1)        | `s.remove(5)`      |
| Удалить (без ошибки) | `.discard(x)`                     | O(1)        | `s.discard(5)`     |
| Удалить случайный    | `.pop()`                          | O(1)        | `s.pop()`          |
| Очистить             | `.clear()`                        | O(n)        | `s.clear()`        |
| Длина                | `len()`                           | O(1)        | `len(s)`           |
| Проверить наличие    | `in`                              | O(1)        | `5 in s`           |
| Объединение          | `\|` или `.union()`               | O(n+m)      | `s1 \| s2`         |
| Пересечение          | `&` или `.intersection()`         | O(min(n,m)) | `s1 & s2`          |
| Разность             | `-` или `.difference()`           | O(n)        | `s1 - s2`          |
| Симм. разность       | `^` или `.symmetric_difference()` | O(n+m)      | `s1 ^ s2`          |
| Подмножество         | `<=` или `.issubset()`            | O(n)        | `s1 <= s2`         |
| Надмножество         | `>=` или `.issuperset()`          | O(m)        | `s1 >= s2`         |
| Копировать           | `.copy()`                         | O(n)        | `s.copy()`         |

---

## 📝 Создание множеств

```python
# Пустое множество (НЕ используйте {})
empty = set()                   # → set() правильно
# empty = {}                    # → dict() ❌ это словарь!

# С элементами
numbers = {1, 2, 3, 4, 5}
mixed = {1, "text", 3.14, True}
fruits = {"apple", "banana", "cherry"}

# Дубликаты автоматически удаляются
numbers = {1, 2, 2, 3, 3, 3}    # → {1, 2, 3}

# Из других последовательностей
from_list = set([1, 2, 3, 2])   # → {1, 2, 3}
from_string = set("hello")      # → {'h', 'e', 'l', 'o'}
from_tuple = set((1, 2, 3))     # → {1, 2, 3}
from_range = set(range(5))      # → {0, 1, 2, 3, 4}

# Set comprehension
squares = {x**2 for x in range(5)}          # → {0, 1, 4, 9, 16}
evens = {x for x in range(10) if x % 2 == 0}  # → {0, 2, 4, 6, 8}

# Неизменяемое множество
frozen = frozenset([1, 2, 3])   # → frozenset({1, 2, 3})
frozen = frozenset("abc")       # → frozenset({'a', 'b', 'c'})
```

## ➕ Добавление элементов

```python
fruits = {"apple", "banana"}

# Добавить один элемент
fruits.add("cherry")            # → {"apple", "banana", "cherry"}
fruits.add("apple")             # → без изменений (уже есть)

# Добавить несколько элементов
fruits.update(["mango", "kiwi"])        # → добавить из списка
fruits.update({"grape", "melon"})       # → добавить из множества
fruits.update("xy")                     # → {'x', 'y', ...}

# Альтернатива update
fruits |= {"orange", "lemon"}           # → то же что update

# Можно передать несколько аргументов
fruits.update([1, 2], {3, 4}, (5, 6))  # → добавит всё

# НЕ РАБОТАЕТ с неизменяемыми элементами
# fruits.add([1, 2])          # ❌ TypeError! список не хешируемый
fruits.add((1, 2))            # ✅ кортеж можно добавить
```

## ➖ Удаление элементов

```python
fruits = {"apple", "banana", "cherry", "date"}

# Удалить элемент (вызовет ошибку если нет)
fruits.remove("banana")         # → {"apple", "cherry", "date"}
# fruits.remove("xyz")          # ❌ KeyError!

# Удалить элемент (не вызовет ошибку)
fruits.discard("cherry")        # → {"apple", "date"}
fruits.discard("xyz")           # → без ошибки, ничего не делает

# Удалить и вернуть случайный элемент
item = fruits.pop()             # → "apple" (или любой другой)
# empty_set.pop()               # ❌ KeyError на пустом множестве

# Очистить всё множество
fruits.clear()                  # → set()

# Удалить с помощью разности
numbers = {1, 2, 3, 4, 5}
numbers -= {2, 4}               # → {1, 3, 5}
numbers.difference_update({3})  # → {1, 5}
```

## 🔍 Проверка и поиск

```python
fruits = {"apple", "banana", "cherry"}

# Проверить наличие элемента
"apple" in fruits               # → True
"orange" in fruits              # → False
"orange" not in fruits          # → True

# Длина множества
len(fruits)                     # → 3

# Проверка на пустоту
if fruits:                      # → True (не пустое)
    print("Есть фрукты")

empty = set()
if not empty:                   # → True (пустое)
    print("Множество пустое")

# Минимум и максимум
numbers = {3, 1, 4, 1, 5, 9}
min(numbers)                    # → 1
max(numbers)                    # → 9

# Сумма
sum(numbers)                    # → 23

# Любой/все элементы True
bool_set = {True, False}
any(bool_set)                   # → True
all(bool_set)                   # → False
```

## 🔄 Операции над множествами

### Объединение (Union) - все элементы из обоих множеств

```python
set1 = {1, 2, 3}
set2 = {3, 4, 5}

# Оператор |
set1 | set2                     # → {1, 2, 3, 4, 5}

# Метод union()
set1.union(set2)                # → {1, 2, 3, 4, 5}

# Несколько множеств
set3 = {5, 6, 7}
set1 | set2 | set3              # → {1, 2, 3, 4, 5, 6, 7}
set1.union(set2, set3)          # → то же самое

# Изменить на месте
set1 |= set2                    # → set1 = {1, 2, 3, 4, 5}
set1.update(set2)               # → то же самое
```

### Пересечение (Intersection) - только общие элементы

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Оператор &
set1 & set2                     # → {3, 4}

# Метод intersection()
set1.intersection(set2)         # → {3, 4}

# Несколько множеств
set3 = {2, 3, 4, 5}
set1 & set2 & set3              # → {3, 4}
set1.intersection(set2, set3)   # → то же самое

# Изменить на месте
set1 &= set2                    # → set1 = {3, 4}
set1.intersection_update(set2)  # → то же самое
```

### Разность (Difference) - элементы из первого, но не из второго

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Оператор -
set1 - set2                     # → {1, 2} (есть в set1, нет в set2)
set2 - set1                     # → {5, 6} (есть в set2, нет в set1)

# Метод difference()
set1.difference(set2)           # → {1, 2}

# Несколько множеств
set3 = {2, 3}
set1 - set2 - set3              # → {1}
set1.difference(set2, set3)     # → то же самое

# Изменить на месте
set1 -= set2                    # → set1 = {1, 2}
set1.difference_update(set2)    # → то же самое
```

### Симметричная разность (Symmetric Difference) - элементы не в обоих

```python
set1 = {1, 2, 3, 4}
set2 = {3, 4, 5, 6}

# Оператор ^
set1 ^ set2                     # → {1, 2, 5, 6}

# Метод symmetric_difference()
set1.symmetric_difference(set2) # → {1, 2, 5, 6}

# Изменить на месте
set1 ^= set2                    # → set1 = {1, 2, 5, 6}
set1.symmetric_difference_update(set2)  # → то же самое

# Эквивалентно
(set1 - set2) | (set2 - set1)   # → {1, 2, 5, 6}
```

## 📊 Отношения между множествами

```python
set1 = {1, 2, 3}
set2 = {1, 2, 3, 4, 5}
set3 = {6, 7, 8}
set4 = {1, 2, 3}

# Подмножество (subset) - все элементы первого есть во втором
set1 <= set2                    # → True (set1 подмножество set2)
set1.issubset(set2)             # → True
set2 <= set1                    # → False

# Строгое подмножество
set1 < set2                     # → True (подмножество и не равны)
set1 < set4                     # → False (равны)

# Надмножество (superset) - все элементы второго есть в первом
set2 >= set1                    # → True (set2 надмножество set1)
set2.issuperset(set1)           # → True
set1 >= set2                    # → False

# Строгое надмножество
set2 > set1                     # → True (надмножество и не равны)
set4 > set1                     # → False (равны)

# Непересекающиеся множества
set1.isdisjoint(set3)           # → True (нет общих элементов)
set1.isdisjoint(set2)           # → False (есть общие)

# Равенство
set1 == set4                    # → True
set1 == set2                    # → False
```

## 🔄 Итерация

```python
fruits = {"apple", "banana", "cherry"}

# Обычный цикл
for fruit in fruits:
    print(fruit)

# С enumerate (но порядок не гарантирован!)
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# Отсортированная итерация
for fruit in sorted(fruits):
    print(fruit)                # → apple, banana, cherry

# List comprehension из множества
upper_fruits = [fruit.upper() for fruit in fruits]

# Set comprehension
lengths = {len(fruit) for fruit in fruits}  # → {5, 6} длины слов
```

## 📋 Копирование множеств

```python
original = {1, 2, 3, 4, 5}

# Поверхностная копия
copy1 = original.copy()
copy2 = set(original)

# Важно! Присваивание НЕ создаёт копию
reference = original            # → та же ссылка!
reference.add(6)                # → изменит и original

# Для множеств с вложенными структурами (frozenset внутри)
nested = {frozenset([1, 2]), frozenset([3, 4])}
copy = nested.copy()            # → поверхностная копия достаточна
```

## 🎯 Полезные примеры

### Удаление дубликатов из списка

```python
numbers = [1, 2, 2, 3, 3, 3, 4, 4]
unique = list(set(numbers))     # → [1, 2, 3, 4] (порядок может измениться)

# С сохранением порядка (Python 3.7+)
unique = list(dict.fromkeys(numbers))  # → [1, 2, 3, 4]
```

### Поиск общих элементов

```python
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]

common = set(list1) & set(list2)        # → {4, 5}
common = list(set(list1) & set(list2))  # → [4, 5] как список
```

### Поиск уникальных элементов

```python
list1 = [1, 2, 3, 4, 5]
list2 = [4, 5, 6, 7, 8]

# Только в первом списке
only_in_first = set(list1) - set(list2)  # → {1, 2, 3}

# Только во втором списке
only_in_second = set(list2) - set(list1)  # → {6, 7, 8}

# Уникальные в каждом (симметричная разность)
unique_in_both = set(list1) ^ set(list2)  # → {1, 2, 3, 6, 7, 8}
```

### Проверка на дубликаты

```python
def has_duplicates(items):
    return len(items) != len(set(items))

has_duplicates([1, 2, 3, 4])    # → False
has_duplicates([1, 2, 2, 3])    # → True
```

### Подсчёт уникальных слов

```python
text = "hello world hello python world"
words = text.split()
unique_words = set(words)
print(f"Уникальных слов: {len(unique_words)}")  # → 3
```

### Фильтрация списка по другому списку

```python
all_items = [1, 2, 3, 4, 5, 6, 7, 8]
exclude = [2, 4, 6]

filtered = [x for x in all_items if x not in set(exclude)]
# → [1, 3, 5, 7, 8]
```

### Проверка всех элементов в допустимых значениях

```python
allowed_colors = {"red", "green", "blue"}
user_colors = ["red", "blue"]

all_valid = set(user_colors) <= allowed_colors  # → True

user_colors2 = ["red", "yellow"]
all_valid2 = set(user_colors2) <= allowed_colors  # → False
```

### Объединение нескольких списков без дубликатов

```python
list1 = [1, 2, 3]
list2 = [3, 4, 5]
list3 = [5, 6, 7]

combined = set(list1) | set(list2) | set(list3)  # → {1, 2, 3, 4, 5, 6, 7}
```

### Поиск недостающих элементов

```python
expected = set(range(1, 11))    # → {1, 2, 3, ..., 10}
actual = {1, 3, 5, 7, 9}

missing = expected - actual     # → {2, 4, 6, 8, 10}
extra = actual - expected       # → set() (нет лишних)
```

### Анаграммы

```python
def are_anagrams(word1, word2):
    return set(word1.lower()) == set(word2.lower())

are_anagrams("listen", "silent")  # → True
are_anagrams("hello", "world")    # → False
```

### Уникальные символы в строке

```python
text = "hello world"
unique_chars = set(text)        # → {'h', 'e', 'l', 'o', ' ', 'w', 'r', 'd'}
unique_chars = set(text) - {" "}  # → без пробелов

# Количество уникальных символов
count = len(set(text.replace(" ", "")))  # → 7
```

### Теги и категории

```python
post1_tags = {"python", "programming", "tutorial"}
post2_tags = {"python", "data-science", "tutorial"}

# Все теги
all_tags = post1_tags | post2_tags

# Общие теги
common_tags = post1_tags & post2_tags  # → {"python", "tutorial"}

# Уникальные теги первого поста
unique_to_post1 = post1_tags - post2_tags  # → {"programming"}
```

## ⚠️ Важные ограничения

```python
# ❌ Нельзя добавлять изменяемые объекты
# my_set = {[1, 2, 3]}        # TypeError! список не хешируемый
# my_set.add([4, 5])          # TypeError!
# my_set.add({1: 2})          # TypeError! словарь не хешируемый

# ✅ Можно использовать неизменяемые типы
my_set = {1, "text", 3.14, (1, 2), True}  # ✅
my_set.add(frozenset([1, 2]))  # ✅ frozenset неизменяемый

# ❌ Нельзя индексировать
numbers = {1, 2, 3}
# numbers[0]                  # TypeError! множества не поддерживают индексацию

# ✅ Можно преобразовать в список для индексации
numbers_list = list(numbers)
numbers_list[0]               # → работает, но порядок не гарантирован

# ❌ Порядок элементов НЕ гарантируется
my_set = {3, 1, 4, 1, 5, 9}
print(my_set)                 # → может быть {1, 3, 4, 5, 9} или другой порядок

# ✅ Используйте sorted() для упорядочивания
sorted_list = sorted(my_set)  # → [1, 3, 4, 5, 9]
```

## 🔷 frozenset - неизменяемое множество

```python
# Создание
frozen = frozenset([1, 2, 3])
frozen = frozenset("abc")
frozen = frozenset({1, 2, 3})

# Нельзя изменять
# frozen.add(4)               # ❌ AttributeError!
# frozen.remove(1)            # ❌ AttributeError!
# frozen.clear()              # ❌ AttributeError!

# Можно использовать как элемент множества
nested = {frozenset([1, 2]), frozenset([3, 4])}  # ✅

# Можно использовать как ключ словаря
my_dict = {
    frozenset([1, 2]): "pair 1",
    frozenset([3, 4]): "pair 2"
}

# Все операции чтения работают
frozen = frozenset([1, 2, 3, 4, 5])
len(frozen)                   # → 5
3 in frozen                   # → True
frozen | {6, 7}               # → frozenset({1, 2, 3, 4, 5, 6, 7})
frozen & {2, 3, 6}            # → frozenset({2, 3})

# Преобразование
regular_set = set(frozen)     # → {1, 2, 3, 4, 5}
frozen_again = frozenset(regular_set)  # → frozenset({1, 2, 3, 4, 5})
```

## 🎨 Set Comprehension

```python
# Базовое создание
squares = {x**2 for x in range(10)}  # → {0, 1, 4, 9, 16, 25, 36, 49, 64, 81}

# С условием
evens = {x for x in range(20) if x % 2 == 0}  # → {0, 2, 4, ..., 18}

# Из строки
vowels = {char.lower() for char in "Hello World" if char.lower() in "aeiou"}
# → {'e', 'o'}

# Множество длин слов
text = "the quick brown fox jumps over the lazy dog"
word_lengths = {len(word) for word in text.split()}  # → {3, 4, 5}

# Преобразование значений
numbers = [1, -2, 3, -4, 5, -6]
absolutes = {abs(x) for x in numbers}  # → {1, 2, 3, 4, 5, 6}

# Вложенные циклы
pairs = {(x, y) for x in range(3) for y in range(3) if x != y}
# → {(0,1), (0,2), (1,0), (1,2), (2,0), (2,1)}

# Уникальные символы из списка строк
words = ["hello", "world", "python"]
unique_chars = {char for word in words for char in word}
# → {'h', 'e', 'l', 'o', 'w', 'r', 'd', 'p', 'y', 't', 'n'}
```

## 🎯 Когда использовать множества

**✅ Используйте множества когда:**

- Нужно хранить только уникальные элементы
- Важна быстрая проверка наличия элемента (`in` работает за O(1))
- Нужны математические операции (объединение, пересечение, разность)
- Порядок элементов не важен
- Нужно удалить дубликаты из коллекции
- Работаете с тегами, категориями, правами доступа

**✅ Используйте списки когда:**

- Важен порядок элементов
- Нужны дубликаты
- Требуется индексация
- Нужно часто изменять элементы по индексу

**✅ Используйте словари когда:**

- Нужна связь ключ-значение
- Требуется быстрый доступ по ключу

---

## 🔗 Связанные темы

- [[03.1  ⤷ Устройство Множества]]
- [[01 — 📋 Cписки ( list ) 🔹]]
- [[04 — 📖 Словарь ( dict ) 🔹]]
- [[00 — 🗂️ Памятка Коллекции Python]]
- [[Все про Хеш-функции]]
- [[Тонкости сравнений]]

## ❓ Вопросы / Непонятное