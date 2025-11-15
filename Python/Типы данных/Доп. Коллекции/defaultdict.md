## Пример для быстрой справки

```python
from collections import defaultdict

# Создаем defaultdict с int (значение по умолчанию 0)
counts = defaultdict(int)

counts['apple'] = 5
counts['banana'] = 3
print(counts['orange'])  # 0 (автоматически создается)

print(counts)
# defaultdict(<class 'int'>, {'apple': 5, 'banana': 3, 'orange': 0})
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from collections import defaultdict`|Подключение класса|
|**Создание с int**|`d = defaultdict(int)`|Значение по умолчанию: 0|
|**Создание с list**|`d = defaultdict(list)`|Значение по умолчанию: []|
|**Создание с dict**|`d = defaultdict(dict)`|Значение по умолчанию: {}|
|**Создание с функцией**|`d = defaultdict(lambda: 'Unknown')`|Кастомное значение|
|**С начальными данными**|`d = defaultdict(int, {'a': 1, 'b': 2})`|С существующим словарем|
|**Доступ к несущ. ключу**|`d['new_key']`|Автоматически создает значение|
|**Изменение factory**|`d.default_factory = list`|Смена функции по умолчанию|
|**Проверка factory**|`d.default_factory`|Возвращает функцию или None|
|**Все методы dict**|`d.keys()`, `d.values()`, `d.items()`|Наследует от dict|

## Что такое defaultdict?

**defaultdict** — это подкласс встроенного словаря `dict`, который автоматически создает значение по умолчанию для несуществующих ключей, избавляя от ошибки `KeyError`.

### Проблема с обычным dict

```python
# ❌ С обычным словарем
regular_dict = {}
print(regular_dict['missing_key'])  # KeyError!

# Нужны проверки
if 'missing_key' not in regular_dict:
    regular_dict['missing_key'] = 0
```

### Решение с defaultdict

```python
# ✅ С defaultdict
from collections import defaultdict

dd = defaultdict(int)
print(dd['missing_key'])  # 0 (создается автоматически)
```

## Создание defaultdict

### Базовый синтаксис

```python
from collections import defaultdict

# Сигнатура
defaultdict(default_factory=None, **kwargs)
```

### С встроенными типами

```python
from collections import defaultdict

# int - значение по умолчанию: 0
d_int = defaultdict(int)
print(d_int['key'])  # 0

# float - значение по умолчанию: 0.0
d_float = defaultdict(float)
print(d_float['key'])  # 0.0

# bool - значение по умолчанию: False
d_bool = defaultdict(bool)
print(d_bool['key'])  # False

# str - значение по умолчанию: ''
d_str = defaultdict(str)
print(d_str['key'])  # ''

# list - значение по умолчанию: []
d_list = defaultdict(list)
print(d_list['key'])  # []

# tuple - значение по умолчанию: ()
d_tuple = defaultdict(tuple)
print(d_tuple['key'])  # ()

# set - значение по умолчанию: set()
d_set = defaultdict(set)
print(d_set['key'])  # set()

# dict - значение по умолчанию: {}
d_dict = defaultdict(dict)
print(d_dict['key'])  # {}
```

### С начальными данными

```python
from collections import defaultdict

# Из словаря
d1 = defaultdict(int, {'a': 1, 'b': 2})

# Из именованных аргументов
d2 = defaultdict(int, a=1, b=2)

# Из списка кортежей
d3 = defaultdict(int, [('a', 1), ('b', 2)])

# Комбинированный способ
d4 = defaultdict(int, {'a': 1}, b=2, c=3)

print(d1)  # defaultdict(<class 'int'>, {'a': 1, 'b': 2})
```

### С пользовательской функцией

```python
from collections import defaultdict

# С lambda
d1 = defaultdict(lambda: 'N/A')
print(d1['name'])  # 'N/A'

# С обычной функцией
def get_default_value():
    return 100

d2 = defaultdict(get_default_value)
print(d2['score'])  # 100

# С более сложной логикой
def get_empty_user():
    return {'name': 'Guest', 'role': 'viewer'}

d3 = defaultdict(get_empty_user)
print(d3['user1'])  # {'name': 'Guest', 'role': 'viewer'}
```

**⚠️ Важно:** Функция `default_factory` **не должна принимать аргументы**.

```python
# ❌ Неправильно
def bad_factory(x):
    return x * 2

d = defaultdict(bad_factory)  # TypeError при обращении к ключу

# ✅ Правильно
d = defaultdict(lambda: 42)
```

## Атрибуты и методы

### Атрибут `default_factory`

Функция, которая вызывается для создания значений по умолчанию:

```python
from collections import defaultdict

d = defaultdict(int)
print(d.default_factory)  # <class 'int'>

# Изменение default_factory
d.default_factory = list
print(d['new_key'])  # []

# Проверка на None
d2 = defaultdict()
print(d2.default_factory)  # None
```

### Динамическое изменение default_factory

```python
from collections import defaultdict

data = defaultdict(int)
print(data['count'])  # 0

# Меняем на list
data.default_factory = list
print(data['items'])  # []

# Меняем на lambda
data.default_factory = lambda: 'Unknown'
print(data['status'])  # 'Unknown'

print(data)
# defaultdict(<function <lambda>>, {'count': 0, 'items': [], 'status': 'Unknown'})
```

### Отключение default_factory

```python
from collections import defaultdict

d = defaultdict(int, {'a': 1, 'b': 2})
print(d['c'])  # 0 (создается автоматически)

# Отключаем автоматическое создание
d.default_factory = None

try:
    print(d['d'])  # KeyError! Теперь ведет себя как обычный dict
except KeyError:
    print("Ключ не найден")
```

### Все методы dict доступны

defaultdict наследуется от dict, поэтому все методы словаря работают:

```python
from collections import defaultdict

d = defaultdict(int, {'a': 1, 'b': 2, 'c': 3})

# Стандартные методы dict
print(d.keys())        # dict_keys(['a', 'b', 'c'])
print(d.values())      # dict_values([1, 2, 3])
print(d.items())       # dict_items([('a', 1), ('b', 2), ('c', 3)])
print(d.get('d', 0))   # 0
print(d.pop('a'))      # 1
d.update({'d': 4})
d.clear()

# Проверка наследования
print(isinstance(d, dict))  # True
```

## Практические примеры

### Пример 1: Подсчет элементов

```python
from collections import defaultdict

# ❌ С обычным словарем
words = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple']
counts = {}

for word in words:
    if word not in counts:
        counts[word] = 0
    counts[word] += 1

# ✅ С defaultdict
words = ['apple', 'banana', 'apple', 'orange', 'banana', 'apple']
counts = defaultdict(int)

for word in words:
    counts[word] += 1

print(counts)
# defaultdict(<class 'int'>, {'apple': 3, 'banana': 2, 'orange': 1})
```

### Пример 2: Группировка данных

```python
from collections import defaultdict

# Группировка студентов по классам
students = [
    ('Иван', '10А'),
    ('Мария', '10Б'),
    ('Петр', '10А'),
    ('Анна', '10Б'),
    ('Сергей', '10А')
]

# ❌ С обычным словарем
classes = {}
for name, class_name in students:
    if class_name not in classes:
        classes[class_name] = []
    classes[class_name].append(name)

# ✅ С defaultdict
classes = defaultdict(list)
for name, class_name in students:
    classes[class_name].append(name)

print(classes)
# defaultdict(<class 'list'>, {
#     '10А': ['Иван', 'Петр', 'Сергей'],
#     '10Б': ['Мария', 'Анна']
# })
```

### Пример 3: Построение графа

```python
from collections import defaultdict

# Граф как список смежности
graph = defaultdict(list)

# Добавление ребер
edges = [('A', 'B'), ('A', 'C'), ('B', 'C'), ('C', 'D')]

for src, dst in edges:
    graph[src].append(dst)

print(graph)
# defaultdict(<class 'list'>, {
#     'A': ['B', 'C'],
#     'B': ['C'],
#     'C': ['D']
# })

# Проверка соседей (даже для несуществующих вершин)
print(graph['E'])  # [] (пустой список автоматически)
```

### Пример 4: Вложенные словари

```python
from collections import defaultdict

# Создание вложенной структуры
nested = defaultdict(lambda: defaultdict(int))

# Подсчет по двум критериям
data = [
    ('apple', 'red', 5),
    ('banana', 'yellow', 3),
    ('apple', 'green', 2),
    ('banana', 'yellow', 4),
]

for fruit, color, count in data:
    nested[fruit][color] += count

print(nested)
# defaultdict(<function <lambda>>, {
#     'apple': defaultdict(<class 'int'>, {'red': 5, 'green': 2}),
#     'banana': defaultdict(<class 'int'>, {'yellow': 7})
# })

# Доступ безопасен даже для несуществующих ключей
print(nested['orange']['purple'])  # 0
```

### Пример 5: Инвертирование словаря с дубликатами

```python
from collections import defaultdict

# Исходный словарь
original = {'a': 1, 'b': 2, 'c': 1, 'd': 3, 'e': 2}

# Инвертирование: значение -> список ключей
inverted = defaultdict(list)

for key, value in original.items():
    inverted[value].append(key)

print(inverted)
# defaultdict(<class 'list'>, {
#     1: ['a', 'c'],
#     2: ['b', 'e'],
#     3: ['d']
# })
```

### Пример 6: Накопление значений

```python
from collections import defaultdict

# Суммирование продаж по дням
sales = [
    ('2024-01-01', 100),
    ('2024-01-01', 150),
    ('2024-01-02', 200),
    ('2024-01-01', 50),
    ('2024-01-02', 75),
]

daily_sales = defaultdict(int)

for date, amount in sales:
    daily_sales[date] += amount

print(daily_sales)
# defaultdict(<class 'int'>, {
#     '2024-01-01': 300,
#     '2024-01-02': 275
# })
```

### Пример 7: Множества значений

```python
from collections import defaultdict

# Уникальные теги для каждой статьи
articles = [
    ('Python', 'programming'),
    ('Python', 'tutorial'),
    ('Java', 'programming'),
    ('Python', 'programming'),  # дубликат
    ('Java', 'enterprise'),
]

tags = defaultdict(set)

for article, tag in articles:
    tags[article].add(tag)

print(tags)
# defaultdict(<class 'set'>, {
#     'Python': {'programming', 'tutorial'},
#     'Java': {'programming', 'enterprise'}
# })
```

### Пример 8: Счетчик с пользовательским стартом

```python
from collections import defaultdict

# Начинаем счет с 100
counter = defaultdict(lambda: 100)

products = ['apple', 'banana', 'apple', 'orange']

for product in products:
    counter[product] += 1

print(counter)
# defaultdict(<function <lambda>>, {
#     'apple': 102,
#     'banana': 101,
#     'orange': 101
# })
```

## Сравнение подходов

### Задача: подсчет частоты слов

```python
text = "apple banana apple orange banana apple"
words = text.split()

# 1️⃣ С проверкой in
counts1 = {}
for word in words:
    if word not in counts1:
        counts1[word] = 0
    counts1[word] += 1

# 2️⃣ С методом get()
counts2 = {}
for word in words:
    counts2[word] = counts2.get(word, 0) + 1

# 3️⃣ С методом setdefault()
counts3 = {}
for word in words:
    counts3.setdefault(word, 0)
    counts3[word] += 1

# 4️⃣ С defaultdict (самый простой и быстрый)
from collections import defaultdict
counts4 = defaultdict(int)
for word in words:
    counts4[word] += 1

# 5️⃣ С Counter (еще проще, специализированный)
from collections import Counter
counts5 = Counter(words)
```

**Рекомендация:** Для подсчета используйте `Counter`, для других случаев — `defaultdict`.

## Когда использовать defaultdict?

### ✅ Используйте когда:

1. **Частое обращение к несуществующим ключам**
    
    - Группировка данных
    - Построение графов
    - Накопление значений
2. **Нужна инициализация значений по умолчанию**
    
    - Счетчики (defaultdict(int))
    - Списки элементов (defaultdict(list))
    - Множества (defaultdict(set))
3. **Агрегация и накопление данных**
    
    - Суммирование
    - Группировка
    - Подсчет
4. **Вложенные структуры**
    
    - `defaultdict(lambda: defaultdict(list))`

### ❌ Не используйте когда:

1. **Нужен обычный словарь без автосоздания ключей**
    
    - Используйте `dict`
2. **Нужен только подсчет частоты**
    
    - Используйте `Counter` (специализирован для этого)
3. **Нужен словарь с гарантированным порядком** (до Python 3.7)
    
    - Используйте `OrderedDict`
    - С Python 3.7+ обычный `dict` сохраняет порядок
4. **Работа с JSON или сериализацией**
    
    - defaultdict не сериализуется напрямую в JSON
    - Конвертируйте в dict: `dict(my_defaultdict)`

## Преобразование в обычный dict

```python
from collections import defaultdict
import json

dd = defaultdict(int, {'a': 1, 'b': 2})
dd['c']  # Создает 'c': 0

# Преобразование в dict
regular_dict = dict(dd)
print(regular_dict)  # {'a': 1, 'b': 2, 'c': 0}

# Для JSON
json_string = json.dumps(dict(dd))
print(json_string)  # {"a": 1, "b": 2, "c": 0}
```

## Сравнение с обычным dict

```python
from collections import defaultdict

# Можно сравнивать
d1 = {'a': 1, 'b': 2}
d2 = defaultdict(int, {'a': 1, 'b': 2})

print(d1 == d2)  # True

# Проверка типа
print(isinstance(d2, dict))  # True (defaultdict наследуется от dict)
```

## Производительность

defaultdict **быстрее**, чем:

- Проверка `if key in dict`
- Метод `dict.get(key, default)`
- Метод `dict.setdefault(key, default)`

```python
# Benchmark (приблизительно)
# defaultdict:     1.0x (базовая линия)
# dict.get():      1.5x медленнее
# dict.setdefault: 2.0x медленнее
# if key in dict:  1.3x медленнее
```

## Распространенные ошибки

### ❌ Ошибка 1: Функция с аргументами

```python
# Неправильно
def factory(value):
    return value

d = defaultdict(factory)  # TypeError при обращении

# Правильно
d = defaultdict(lambda: 42)
```

### ❌ Ошибка 2: Забыли указать default_factory

```python
from collections import defaultdict

# Неправильно - создание из данных без factory
d = defaultdict({'a': 1, 'b': 2})  # словарь стал factory!

# Правильно
d = defaultdict(int, {'a': 1, 'b': 2})
```

### ❌ Ошибка 3: Попытка сериализовать напрямую

```python
import json
from collections import defaultdict

d = defaultdict(int, {'a': 1})

# Неправильно
# json.dumps(d)  # TypeError

# Правильно
json.dumps(dict(d))
```

### ❌ Ошибка 4: Забыли про default_factory = None

```python
from collections import defaultdict

d = defaultdict(int, {'a': 1})
d.default_factory = None  # Отключили автосоздание

# Теперь KeyError
try:
    print(d['b'])
except KeyError:
    print("Ключ не найден")
```

## Полезные ссылки

- [Официальная документация](https://docs.python.org/3/library/collections.html#collections.defaultdict)
- [PEP 3106](https://www.python.org/dev/peps/pep-3106/) — Revamping dict.keys(), .values() and .items()
- [Исходный код defaultdict](https://github.com/python/cpython/blob/main/Modules/_collectionsmodule.c)