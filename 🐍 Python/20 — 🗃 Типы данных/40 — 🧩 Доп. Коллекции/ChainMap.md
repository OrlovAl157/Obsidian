---
tags:
  - python
  - тема/типы_данных
  - коллекции
  - статус/готово
---

## Пример для быстрой справки

```python
from collections import ChainMap

# Объединяем несколько словарей
numbers = {'one': 1, 'two': 2}
letters = {'a': 'A', 'b': 'B'}

chain = ChainMap(numbers, letters)

print(chain)
# ChainMap({'one': 1, 'two': 2}, {'a': 'A', 'b': 'B'})

print(chain['one'])  # 1 (из первого словаря)
print(chain['a'])    # 'A' (из второго словаря)
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from collections import ChainMap`|Подключение класса|
|**Создание**|`ChainMap(dict1, dict2, ...)`|Объединение словарей|
|**Пустой**|`ChainMap()`|Пустой ChainMap|
|**Из ключей**|`ChainMap.fromkeys(keys, value)`|Создание из списка ключей|
|**Доступ к элементу**|`chain['key']`|Поиск в первом подходящем словаре|
|**Список словарей**|`chain.maps`|Список всех словарей|
|**Добавить словарь**|`chain.new_child(dict)`|Новый ChainMap с dict впереди|
|**Убрать первый**|`chain.parents`|Новый ChainMap без первого словаря|
|**Добавление элемента**|`chain['key'] = value`|Добавляет только в первый словарь|
|**Обновление**|`chain.update(dict)`|Обновляет только первый словарь|
|**Удаление**|`del chain['key']`|Удаляет только из первого словаря|
|**Итерирование**|`for k in chain`|От последнего словаря к первому|

## Что такое ChainMap?

**ChainMap** — это класс из модуля `collections`, который группирует несколько словарей вместе и **позволяет рассматривать их как единое целое**.

### Основная идея

```python
from collections import ChainMap

# Вместо объединения словарей
combined = {}
combined.update(dict1)
combined.update(dict2)  # Значения перезапишутся!

# Используем ChainMap
chain = ChainMap(dict1, dict2)  # Сохраняет все значения
```

**Преимущества ChainMap:**

- 🔗 Не создает копии данных
- 🔍 Сохраняет доступ ко всем значениям (даже с одинаковыми ключами)
- ⚡ Быстрое объединение (не копирует данные)
- 🔄 Изменения в исходных словарях отражаются в ChainMap
- 📦 Полезен для работы с контекстами и областями видимости

## Создание ChainMap

### Различные способы создания

```python
from collections import ChainMap

# 1. Из нескольких словарей
dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3, 'd': 4}
chain = ChainMap(dict1, dict2)
# ChainMap({'a': 1, 'b': 2}, {'c': 3, 'd': 4})

# 2. Пустой ChainMap
empty_chain = ChainMap()
# ChainMap({})

# 3. С помощью fromkeys()
chain = ChainMap.fromkeys(['a', 'b', 'c'])
# ChainMap({'a': None, 'b': None, 'c': None})

chain = ChainMap.fromkeys(['x', 'y', 'z'], 0)
# ChainMap({'x': 0, 'y': 0, 'z': 0})

# 4. Из списка словарей
dicts = [{'a': 1}, {'b': 2}, {'c': 3}]
chain = ChainMap(*dicts)
# ChainMap({'a': 1}, {'b': 2}, {'c': 3})
```

### Создание с пустым первым словарем

Часто полезно добавить пустой словарь в начало, чтобы изменения не затрагивали исходные словари:

```python
from collections import ChainMap

numbers = {'one': 1, 'two': 2}
letters = {'a': 'A', 'b': 'B'}

# Изменения будут в пустом словаре, исходные не тронуты
chain = ChainMap({}, numbers, letters)

chain['new'] = 'NEW'
print(chain)
# ChainMap({'new': 'NEW'}, {'one': 1, 'two': 2}, {'a': 'A', 'b': 'B'})

print(numbers)  # {'one': 1, 'two': 2} — не изменился!
```

## Доступ к элементам

### Поиск по ключу

ChainMap ищет ключ **слева направо** и останавливается при первом найденном:

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'a': 10, 'c': 3}

chain = ChainMap(dict1, dict2)

print(chain['a'])  # 1 (из dict1, хотя в dict2 тоже есть 'a')
print(chain['b'])  # 2 (из dict1)
print(chain['c'])  # 3 (из dict2)

# С помощью get()
print(chain.get('a'))     # 1
print(chain.get('z'))     # None
print(chain.get('z', 0))  # 0
```

### Повторяющиеся ключи

```python
from collections import ChainMap

for_adoption = {'dogs': 15, 'cats': 8, 'pythons': 9}
vet_treatment = {'dogs': 7, 'cats': 2, 'tigers': 3}

pets = ChainMap(for_adoption, vet_treatment)

print(pets['dogs'])    # 15 (из первого словаря)
print(pets['cats'])    # 8 (из первого словаря)
print(pets['tigers'])  # 3 (только во втором словаре)
```

**Важно:** При повторяющихся ключах возвращается значение из **первого** словаря, где ключ найден.

## Итерирование

### Порядок итерации

Итерирование происходит **от последнего словаря к первому**, но без дубликатов:

```python
from collections import ChainMap

numbers = {'one': 1, 'two': 2}
letters = {'a': 'A', 'b': 'B'}

chain = ChainMap(numbers, letters)

# Итерация по ключам
for key in chain:
    print(key, '->', chain[key])
# a -> A
# b -> B
# one -> 1
# two -> 2
```

### Методы итерирования

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3, 'd': 4}

chain = ChainMap(dict1, dict2)

# keys()
for key in chain.keys():
    print(key)  # d, c, b, a (от последнего к первому)

# values()
for value in chain.values():
    print(value)  # 4, 3, 2, 1

# items()
for key, value in chain.items():
    print(f"{key}: {value}")
# d: 4, c: 3, b: 2, a: 1
```

### Повторяющиеся ключи при итерации

```python
from collections import ChainMap

for_adoption = {'dogs': 15, 'cats': 8, 'pythons': 9}
vet_treatment = {'dogs': 7, 'cats': 2, 'tigers': 3}

pets = ChainMap(for_adoption, vet_treatment)

for key in pets:
    print(key, '->', pets[key])
# tigers -> 3 (только во втором)
# dogs -> 15 (первое значение)
# cats -> 8 (первое значение)
# pythons -> 9 (только в первом)
```

Ключи `'dogs'` и `'cats'` встречаются только **один раз**, берется значение из первого словаря.

## Изменение ChainMap

### Все изменения — только в первом словаре!

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)

# Добавление
chain['c'] = 3
print(chain)
# ChainMap({'a': 1, 'c': 3}, {'b': 2})

# Обновление
chain['a'] = 10
print(chain)
# ChainMap({'a': 10, 'c': 3}, {'b': 2})

# Попытка обновить ключ из второго словаря
chain['b'] = 20  # Создает НОВЫЙ ключ в первом словаре!
print(chain)
# ChainMap({'a': 10, 'c': 3, 'b': 20}, {'b': 2})
# Теперь в обоих словарях есть 'b'!
```

### Удаление элементов

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3}

chain = ChainMap(dict1, dict2)

# Удаление из первого словаря
del chain['a']
print(chain)
# ChainMap({'b': 2}, {'c': 3})

# ❌ Попытка удалить ключ не из первого словаря
try:
    del chain['c']  # KeyError!
except KeyError:
    print("Можно удалять только из первого словаря")
```

### Методы изменения

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)

# update() — только первый словарь
chain.update({'c': 3, 'd': 4})
print(chain)
# ChainMap({'a': 1, 'c': 3, 'd': 4}, {'b': 2})

# pop() — только первый словарь
value = chain.pop('a')
print(value)  # 1
print(chain)
# ChainMap({'c': 3, 'd': 4}, {'b': 2})

# clear() — только первый словарь
chain.clear()
print(chain)
# ChainMap({}, {'b': 2})
```

## Атрибут `maps`

Атрибут `maps` содержит список всех словарей ChainMap:

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}
dict3 = {'c': 3}

chain = ChainMap(dict1, dict2, dict3)

print(chain.maps)
# [{'a': 1}, {'b': 2}, {'c': 3}]

print(type(chain.maps))
# <class 'list'>
```

### Изменение списка словарей

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)
print(chain)
# ChainMap({'a': 1}, {'b': 2})

# Разворачиваем порядок словарей
chain.maps.reverse()
print(chain)
# ChainMap({'b': 2}, {'a': 1})

# Добавляем новый словарь
chain.maps.append({'c': 3})
print(chain)
# ChainMap({'b': 2}, {'a': 1}, {'c': 3})

# Удаляем словарь
chain.maps.pop(0)
print(chain)
# ChainMap({'a': 1}, {'c': 3})
```

### Доступ ко всем значениям

С помощью `maps` можно обойти все словари и получить **все** значения (даже повторяющиеся):

```python
from collections import ChainMap

for_adoption = {'dogs': 15, 'cats': 8}
vet_treatment = {'dogs': 7, 'cats': 2}

pets = ChainMap(for_adoption, vet_treatment)

# Обычный доступ — только первые значения
print(pets['dogs'])  # 15

# Через maps — все значения
for animals_dict in pets.maps:
    for key, value in animals_dict.items():
        print(f"{key}: {value}")
# dogs: 15
# cats: 8
# dogs: 7
# cats: 2
```

## Метод `new_child(m=None)`

Создает **новый** ChainMap с добавленным словарем в начало:

```python
from collections import ChainMap

dad = {'name': 'Timur', 'age': 29}
mom = {'name': 'Rosaly', 'age': 28}

old_family = ChainMap(dad, mom)
print(old_family)
# ChainMap({'name': 'Timur', 'age': 29}, {'name': 'Rosaly', 'age': 28})

# Добавляем нового члена семьи
son = {'name': 'Soslan', 'age': 0}
new_family = old_family.new_child(son)

print(new_family)
# ChainMap({'name': 'Soslan', 'age': 0}, 
#          {'name': 'Timur', 'age': 29}, 
#          {'name': 'Rosaly', 'age': 28})

# Старый объект не изменился
print(old_family)
# ChainMap({'name': 'Timur', 'age': 29}, {'name': 'Rosaly', 'age': 28})
```

### Без аргументов

Если не передать словарь, добавляется пустой словарь:

```python
from collections import ChainMap

config = ChainMap({'debug': False, 'port': 8080})

# Добавляем пустой словарь для временных настроек
temp_config = config.new_child()

print(temp_config)
# ChainMap({}, {'debug': False, 'port': 8080})

# Теперь изменения не затронут оригинал
temp_config['debug'] = True
print(temp_config)
# ChainMap({'debug': True}, {'debug': False, 'port': 8080})

print(config)
# ChainMap({'debug': False, 'port': 8080}) — не изменился
```

**Эквивалентно:**

```python
chain.new_child(m) == ChainMap(m, *chain.maps)
chain.new_child() == ChainMap({}, *chain.maps)
```

## Атрибут `parents`

Возвращает **новый** ChainMap без первого словаря:

```python
from collections import ChainMap

son = {'name': 'Soslan', 'age': 0}
dad = {'name': 'Timur', 'age': 29}
mom = {'name': 'Rosaly', 'age': 28}

family = ChainMap(son, dad, mom)
print(family)
# ChainMap({'name': 'Soslan', 'age': 0}, 
#          {'name': 'Timur', 'age': 29}, 
#          {'name': 'Rosaly', 'age': 28})

# Получаем родителей (без первого словаря)
parents = family.parents
print(parents)
# ChainMap({'name': 'Timur', 'age': 29}, {'name': 'Rosaly', 'age': 28})

print(type(parents))
# <class 'collections.ChainMap'>
```

**Эквивалентно:**

```python
chain.parents == ChainMap(*chain.maps[1:])
```

### Связь new_child() и parents

```python
from collections import ChainMap

base = ChainMap({'a': 1}, {'b': 2})

# new_child() добавляет словарь
extended = base.new_child({'c': 3})
print(extended)
# ChainMap({'c': 3}, {'a': 1}, {'b': 2})

# parents убирает первый словарь
back_to_base = extended.parents
print(back_to_base)
# ChainMap({'a': 1}, {'b': 2})

# Вернулись к оригиналу
print(back_to_base.maps == base.maps)  # True
```

## Практические примеры

### Пример 1: Конфигурация приложения (каскадные настройки)

```python
from collections import ChainMap

# Дефолтные настройки
defaults = {
    'debug': False,
    'port': 8080,
    'host': 'localhost',
    'timeout': 30
}

# Настройки окружения
env_config = {
    'port': 3000,
    'debug': True
}

# Настройки пользователя
user_config = {
    'timeout': 60
}

# Каскадная конфигурация: user > env > defaults
config = ChainMap(user_config, env_config, defaults)

print(config['debug'])    # True (из env_config)
print(config['port'])     # 3000 (из env_config)
print(config['timeout'])  # 60 (из user_config)
print(config['host'])     # 'localhost' (из defaults)
```

### Пример 2: Области видимости переменных

```python
from collections import ChainMap

# Глобальная область
global_scope = {'x': 100, 'y': 200}

def outer():
    # Локальная область outer
    outer_scope = {'x': 10, 'z': 30}
    scope = ChainMap(outer_scope, global_scope)
    
    def inner():
        # Локальная область inner
        inner_scope = {'x': 1, 'w': 40}
        scope = ChainMap(inner_scope, outer_scope, global_scope)
        
        print(f"x = {scope['x']}")  # 1 (из inner)
        print(f"y = {scope['y']}")  # 200 (из global)
        print(f"z = {scope['z']}")  # 30 (из outer)
        print(f"w = {scope['w']}")  # 40 (из inner)
    
    inner()

outer()
```

### Пример 3: Системы шаблонов

```python
from collections import ChainMap

# Базовые переменные шаблона
base_context = {
    'site_name': 'My Site',
    'year': 2024
}

# Контекст страницы
page_context = {
    'title': 'Home Page',
    'description': 'Welcome to our site'
}

# Пользовательские данные
user_context = {
    'username': 'John',
    'is_authenticated': True
}

# Полный контекст для рендеринга
template_context = ChainMap(user_context, page_context, base_context)

# Рендеринг
template = "{title} - {site_name} | User: {username} | Year: {year}"
print(template.format(**dict(template_context)))
# "Home Page - My Site | User: John | Year: 2024"
```

### Пример 4: Параметры командной строки

```python
from collections import ChainMap

# Дефолтные параметры
defaults = {
    'verbose': False,
    'output': 'output.txt',
    'format': 'json'
}

# Параметры из конфиг-файла
config_file = {
    'output': 'data.txt',
    'verbose': True
}

# Параметры командной строки (имеют наивысший приоритет)
cli_args = {
    'format': 'xml'
}

# Приоритет: CLI > config > defaults
options = ChainMap(cli_args, config_file, defaults)

print(f"Output: {options['output']}")    # 'data.txt' (из config)
print(f"Format: {options['format']}")    # 'xml' (из CLI)
print(f"Verbose: {options['verbose']}")  # True (из config)
```

### Пример 5: Многоуровневые словари оценок

```python
from collections import ChainMap

# Оценки по предметам для разных классов
math_scores = {'Alice': 90, 'Bob': 85, 'Charlie': 78}
physics_scores = {'Alice': 88, 'Bob': 92}
chemistry_scores = {'Charlie': 95, 'Diana': 87}

# Объединяем все оценки
all_scores = ChainMap(math_scores, physics_scores, chemistry_scores)

# Поиск оценки студента (первое найденное значение)
print(all_scores['Alice'])    # 90 (из math)
print(all_scores['Bob'])      # 85 (из math)
print(all_scores['Charlie'])  # 78 (из math)
print(all_scores['Diana'])    # 87 (из chemistry)

# Получение всех оценок студента
def get_all_scores(student, scores_chain):
    result = {}
    for subject_scores in scores_chain.maps:
        for name, score in subject_scores.items():
            if name == student:
                # Определяем предмет по позиции в maps
                subject = ['Math', 'Physics', 'Chemistry'][scores_chain.maps.index(subject_scores)]
                result[subject] = score
    return result

print(get_all_scores('Alice', all_scores))
# {'Math': 90, 'Physics': 88}
```

### Пример 6: Работа с различными типами словарей

```python
from collections import ChainMap, defaultdict, OrderedDict, Counter

# Разные типы словарей
numbers = OrderedDict(one=1, two=2)
letters = defaultdict(str, a='A', b='B')
counts = Counter('aabbbcccc')

chain = ChainMap(numbers, letters, counts)

print(chain['one'])   # 1 (OrderedDict)
print(chain['a'])     # 'A' (defaultdict)
print(chain['c'])     # 4 (Counter)

# Обращение к несуществующему ключу
print(chain.get('z'))  # None (из ChainMap)

# Но если бы мы обращались напрямую:
# letters['z'] вернул бы '' (defaultdict behavior)
```

### Пример 7: Временные изменения с сохранением исходных данных

```python
from collections import ChainMap

# Оригинальные данные
original_settings = {'theme': 'dark', 'language': 'en', 'notifications': True}

# Создаем временную конфигурацию
temp_settings = ChainMap({}, original_settings).new_child()

# Изменяем временные настройки
temp_settings['theme'] = 'light'
temp_settings['font_size'] = 14

print("Временные настройки:", dict(temp_settings))
# {'font_size': 14, 'theme': 'light', 'language': 'en', 'notifications': True}

print("Оригинальные настройки:", original_settings)
# {'theme': 'dark', 'language': 'en', 'notifications': True}
```

## Сравнение ChainMap

Два ChainMap равны, если их словарное представление одинаково:

```python
from collections import ChainMap

chain1 = ChainMap({'a': 1, 'b': 2})
chain2 = ChainMap({'a': 1, 'b': 2})

print(chain1 == chain2)  # True

# Даже если структура разная, но итоговые данные одинаковые
chain3 = ChainMap({'a': 1, 'b': 2}, {'c': 3})
chain4 = ChainMap({'a': 1, 'b': 2})

print(chain3 == chain4)  # True (c не видно при итерации)
```

## Длина ChainMap

`len()` возвращает количество **уникальных** ключей:

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'a': 10, 'c': 3}

chain = ChainMap(dict1, dict2)

print(len(chain))  # 3 (a, b, c — без дубликатов)
```

## ChainMap vs dict.update()

### С dict.update() — потеря данных

```python
# ❌ С dict.update()
for_adoption = {'dogs': 15, 'cats': 8}
vet_treatment = {'dogs': 7, 'tigers': 3}

pets = {}
pets.update(for_adoption)
pets.update(vet_treatment)

print(pets)
# {'dogs': 7, 'cats': 8, 'tigers': 3}
# Потеряли dogs=15!
```

### С ChainMap — сохранение всех данных

```python
# ✅ С ChainMap
from collections import ChainMap

for_adoption = {'dogs': 15, 'cats': 8}
vet_treatment = {'dogs': 7, 'tigers': 3}

pets = ChainMap(for_adoption, vet_treatment)

print(pets['dogs'])  # 15 (сохранили первое значение)

# Можем получить все значения через maps
for d in pets.maps:
    if 'dogs' in d:
        print(f"dogs: {d['dogs']}")
# dogs: 15
# dogs: 7
```

## Связь с исходными словарями

ChainMap **не копирует** данные, а хранит ссылки:

```python
from collections import ChainMap

dict1 = {'a': 1, 'b': 2}
dict2 = {'c': 3}

chain = ChainMap(dict1, dict2)

# Изменяем исходный словарь
dict1['a'] = 100

# ChainMap тоже изменился
print(chain['a'])  # 100

# И наоборот
chain['b'] = 200

# Исходный словарь изменился
print(dict1)  # {'a': 100, 'b': 200}
```

## Когда использовать ChainMap?

### ✅ Используйте ChainMap когда:

1. **Каскадные конфигурации**
    
    ```python
    config = ChainMap(user_config, env_config, defaults)
    ```
    
2. **Области видимости переменных**
    
    ```python
    scope = ChainMap(local, global)
    ```
    
3. **Нужен доступ ко всем значениям повторяющихся ключей**
    
    ```python
    # Можно получить все значения через .maps
    ```
    
4. **Временные изменения без модификации оригинала**
    
    ```python
    temp = chain.new_child({})
    ```
    
5. **Объединение словарей без копирования**
    
    ```python
    # Эффективно по памяти
    ```
    
6. **Работа с контекстами**
    
    ```python
    # Шаблоны, интерпретаторы
    ```
    

### ❌ Не используйте ChainMap когда:

1. **Нужен единый словарь без дубликатов**
    
    ```python
    # Используйте dict.update() или |
    combined = dict1 | dict2  # Python 3.9+
    ```
    
2. **Важен порядок ключей**
    
    ```python
    # Итерация в обратном порядке может быть неожиданной
    ```
    
3. **Нужно часто изменять все словари**
    
    ```python
    # Изменения только в первом словаре
    ```
    
4. **Требуется простая семантика**
    
    ```python
    # ChainMap может быть избыточным
    ```
    

## Распространенные ошибки

### ❌ Ошибка 1: Ожидание изменения всех словарей

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)

# Неправильно — изменится только dict1
chain['b'] = 20  # Создает новый ключ в dict1!

print(chain)
# ChainMap({'a': 1, 'b': 20}, {'b': 2})

# Правильно — измените напрямую
dict2['b'] = 20
```

### ❌ Ошибка 2: Удаление ключа не из первого словаря

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)

# Неправильно
try:
    del chain['b']  # KeyError!
except KeyError:
    print("Ошибка: 'b' не в первом словаре")

# Правильно
del dict2['b']
# или
chain.maps[1].pop('b')
```

### ❌ Ошибка 3: Забыли про пустой словарь в начале

```python
from collections import ChainMap

config = {'debug': False}

# Плохо — изменения затронут config
chain = ChainMap(config)
chain['temp'] = True
print(config)  # {'debug': False, 'temp': True} — изменился!

# Хорошо — добавляем пустой словарь
chain = ChainMap({}, config)
chain['temp'] = True
print(config)  # {'debug': False} — не изменился
```

### ❌ Ошибка 4: Путаница с порядком итерации

```python
from collections import ChainMap

dict1 = {'a': 1}
dict2 = {'b': 2}

chain = ChainMap(dict1, dict2)

# Итерация идет от последнего к первому!
for key in chain:
    print(key)
# b (из dict2)
# a (из dict1)
```

## Производительность

ChainMap эффективен по памяти, так как не копирует данные:

```python
import sys
from collections import ChainMap

dict1 = {f'key{i}': i for i in range(1000)}
dict2 = {f'key{i}': i for i in range(1000, 2000)}

# С dict.update() — копирование
combined = {}
combined.update(dict1)
combined.update(dict2)

# С ChainMap — без копирования
chain = ChainMap(dict1, dict2)

print(f"dict size: {sys.getsizeof(combined)} bytes")
print(f"ChainMap size: {sys.getsizeof(chain)} bytes")
# ChainMap значительно меньше
```

**Особенности:**

- ✅ Создание: O(1) — очень быстро
- ✅ Память: не копирует данные
- ⚠️ Поиск: O(n) где n — количество словарей
- ⚠️ Итерация: медленнее чем у dict

## Полезные ссылки

- [Официальная документация ChainMap (EN)](https://docs.python.org/3/library/collections.html#collections.ChainMap)
- [PEP 3132 — Extended Iterable Unpacking](https://www.python.org/dev/peps/pep-3132/)
- [Исходный код ChainMap](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py)