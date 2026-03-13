---
tags:
  - python
  - тема/типы_данных
  - тема/коллекции
  - тема/collections
  - статус/завершён
уровень: средний
изменяемый: true
хешируемый: false
dict_key: false
итерируемый: true
упорядоченный: true
стандартная_библиотека: true
---

## Пример для быстрой справки

```python
from collections import OrderedDict

# Создаем упорядоченный словарь
numbers = OrderedDict()
numbers['one'] = 1
numbers['two'] = 2
numbers['three'] = 3

print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3})  # Python 3.12+
# OrderedDict([('one', 1), ('two', 2), ('three', 3)])  # Python 3.11-
```

Все примеры в таблице ниже основаны на этом коде ⬇️

## Быстрая справка

|Операция|Синтаксис|Описание|
|---|---|---|
|**Импорт**|`from collections import OrderedDict`|Подключение класса|
|**Создание пустого**|`d = OrderedDict()`|Пустой упорядоченный словарь|
|**Из словаря**|`d = OrderedDict({'a': 1, 'b': 2})`|С начальными данными|
|**Из списка кортежей**|`d = OrderedDict([('a', 1), ('b', 2)])`|Гарантирует порядок|
|**Из kwargs**|`d = OrderedDict(a=1, b=2)`|Именованные аргументы|
|**Добавление элемента**|`d['key'] = value`|Добавляется в конец|
|**Перемещение в конец**|`d.move_to_end('key')`|Перемещает ключ в конец|
|**Перемещение в начало**|`d.move_to_end('key', last=False)`|Перемещает ключ в начало|
|**Удаление с конца**|`d.popitem()`|LIFO (последний вошел)|
|**Удаление с начала**|`d.popitem(last=False)`|FIFO (первый вошел)|
|**Обратный перебор**|`for k in reversed(d)`|С Python 3.5+|
|**Объединение**|`d1 \| d2`|С Python 3.9+|
|**Все методы dict**|`d.keys()`, `d.values()`, `d.items()`|Наследует от dict|

## Что такое OrderedDict?

**OrderedDict** — это подкласс встроенного словаря `dict`, который **гарантирует сохранение порядка вставки элементов**. Это был важный тип данных до Python 3.7, когда обычные словари не гарантировали порядок.

### Историческая справка

|Версия Python|Событие|
|---|---|
|**Python 3.1 (2008)**|Добавлен OrderedDict в модуль collections|
|**Python 3.6 (2016)**|dict переработан, порядок сохраняется (деталь реализации)|
|**Python 3.7 (2018)**|dict **официально** гарантирует порядок вставки|
|**Python 3.8 (2019)**|dict поддерживает `reversed()`|
|**Python 3.9 (2020)**|Операторы `\|` и `\|=` для объединения словарей|
|**Python 3.12 (2023)**|Новое строковое представление OrderedDict|

### Зачем нужен OrderedDict сегодня?

После Python 3.7 обычные `dict` тоже сохраняют порядок. Но OrderedDict все еще полезен:

1. ✅ **Явное указание намерения** — порядок важен для алгоритма
2. ✅ **Метод `move_to_end()`** — изменение порядка элементов
3. ✅ **Метод `popitem(last=False)`** — удаление с начала
4. ✅ **Сравнение с учетом порядка** — два OrderedDict равны только если порядок одинаков
5. ✅ **Атрибут `__dict__`** — динамическое добавление атрибутов
6. ✅ **Обратная совместимость** — код работает с Python < 3.7

## Создание OrderedDict

### Все способы создания

```python
from collections import OrderedDict

# 1. Пустой словарь
d1 = OrderedDict()

# 2. Из словаря
d2 = OrderedDict({'one': 1, 'two': 2, 'three': 3})

# 3. Из списка кортежей (гарантирует порядок)
d3 = OrderedDict([('one', 1), ('two', 2), ('three', 3)])

# 4. Из именованных аргументов
d4 = OrderedDict(one=1, two=2, three=3)

# 5. Комбинированный способ
d5 = OrderedDict([('one', 1)], two=2, three=3)

# 6. С помощью fromkeys()
keys = ['a', 'b', 'c']
d6 = OrderedDict.fromkeys(keys, 0)
# OrderedDict({'a': 0, 'b': 0, 'c': 0})
```

## Изменение OrderedDict

### Добавление элементов

Новые элементы добавляются **в конец**:

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)
print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3})

numbers['four'] = 4
print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3, 'four': 4})
```

### Обновление значений

При обновлении существующего ключа **порядок сохраняется**:

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

# Через квадратные скобки
numbers['one'] = 1.0
print(numbers)
# OrderedDict({'one': 1.0, 'two': 2, 'three': 3})

# Через метод update()
numbers.update(two=2.0)
print(numbers)
# OrderedDict({'one': 1.0, 'two': 2.0, 'three': 3})
```

### Удаление и повторная вставка

При удалении и повторной вставке элемент добавляется **в конец**:

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)
print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3})

del numbers['one']
print(numbers)
# OrderedDict({'two': 2, 'three': 3})

numbers['one'] = 1
print(numbers)
# OrderedDict({'two': 2, 'three': 3, 'one': 1})  # 'one' теперь в конце!
```

## Специальные методы OrderedDict

### Метод `move_to_end(key, last=True)`

Перемещает существующий элемент в начало или конец словаря.

**⚙️ Параметры:**

- `key` — ключ перемещаемого элемента (обязательный)
- `last` — куда перемещать: `True` (по умолчанию) = в конец, `False` = в начало

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)
print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3})

# Перемещаем 'one' в конец
numbers.move_to_end('one')
print(numbers)
# OrderedDict({'two': 2, 'three': 3, 'one': 1})

# Перемещаем 'three' в начало
numbers.move_to_end('three', last=False)
print(numbers)
# OrderedDict({'three': 3, 'two': 2, 'one': 1})
```

**⚠️ Важно:** Если ключ отсутствует, будет выброшена ошибка `KeyError`:

```python
numbers = OrderedDict(one=1, two=2)

try:
    numbers.move_to_end('four')
except KeyError as e:
    print(f"Ошибка: {e}")  # Ошибка: 'four'
```

### Метод `popitem(last=True)`

Удаляет и возвращает элемент с конца (LIFO) или начала (FIFO).

**⚙️ Параметры:**

- `last` — откуда удалять: `True` (по умолчанию) = с конца, `False` = с начала

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

# Удаление с конца (LIFO - Last In First Out)
item = numbers.popitem()
print(item)      # ('three', 3)
print(numbers)   # OrderedDict({'one': 1, 'two': 2})

# Удаление с начала (FIFO - First In First Out)
item = numbers.popitem(last=False)
print(item)      # ('one', 1)
print(numbers)   # OrderedDict({'two': 2})
```

**⚖️ Сравнение с обычным dict:**

```python
# dict.popitem() — только с конца
regular_dict = {'a': 1, 'b': 2, 'c': 3}
regular_dict.popitem()  # ('c', 3) — только LIFO

# OrderedDict.popitem() — с конца ИЛИ с начала
ordered_dict = OrderedDict(a=1, b=2, c=3)
ordered_dict.popitem(last=False)  # ('a', 1) — можем FIFO!
```

## Итерирование по OrderedDict

### Прямой порядок

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

# Перебор ключей напрямую
for key in numbers:
    print(key, '->', numbers[key])

# Перебор через items()
for key, value in numbers.items():
    print(key, '->', value)

# Перебор через keys()
for key in numbers.keys():
    print(key)

# Перебор через values()
for value in numbers.values():
    print(value)
```

### Обратный порядок

OrderedDict поддерживает `reversed()` начиная с Python 3.5:

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

# Обратный перебор ключей
for key in reversed(numbers):
    print(key, '->', numbers[key])
# three -> 3
# two -> 2
# one -> 1

# Обратный перебор items()
for key, value in reversed(numbers.items()):
    print(key, '->', value)

# Обратный перебор keys()
for key in reversed(numbers.keys()):
    print(key)

# Обратный перебор values()
for value in reversed(numbers.values()):
    print(value)
```

**Примечание:** Обычные `dict` поддерживают `reversed()` с Python 3.8+.

## Сравнение словарей

### OrderedDict vs OrderedDict

При сравнении OrderedDict **порядок элементов важен**:

```python
from collections import OrderedDict

# Одинаковые элементы, одинаковый порядок
d1 = OrderedDict(a=1, b=2, c=3)
d2 = OrderedDict([('a', 1), ('b', 2), ('c', 3)])
print(d1 == d2)  # True

# Одинаковые элементы, РАЗНЫЙ порядок
d3 = OrderedDict(b=2, a=1, c=3)
print(d1 == d3)  # False — порядок отличается!
```

### dict vs dict

При сравнении обычных dict **порядок не важен**:

```python
d1 = {'a': 1, 'b': 2, 'c': 3}
d2 = {'b': 2, 'a': 1, 'c': 3}
print(d1 == d2)  # True — порядок игнорируется
```

### OrderedDict vs dict

При сравнении OrderedDict с dict **порядок не важен**:

```python
from collections import OrderedDict

d1 = OrderedDict(a=1, b=2, c=3)
d2 = {'b': 2, 'a': 1, 'c': 3}
print(d1 == d2)  # True — порядок игнорируется при сравнении разных типов
```

## Практические примеры

### Пример 1: Сортировка словаря по ключам

```python
from collections import OrderedDict

letters = OrderedDict(d=4, b=2, a=1, c=3)
print(letters)
# OrderedDict({'d': 4, 'b': 2, 'a': 1, 'c': 3})

# Сортируем по ключам
for key in sorted(letters):
    letters.move_to_end(key)

print(letters)
# OrderedDict({'a': 1, 'b': 2, 'c': 3, 'd': 4})
```

### Пример 2: Сортировка по значениям

```python
from collections import OrderedDict

# Исходный словарь
scores = OrderedDict(Alice=85, Bob=92, Charlie=78, Diana=95)

# Сортируем по значениям (убывание)
sorted_scores = OrderedDict(
    sorted(scores.items(), key=lambda x: x[1], reverse=True)
)

print(sorted_scores)
# OrderedDict({'Diana': 95, 'Bob': 92, 'Alice': 85, 'Charlie': 78})
```

### Пример 3: LRU Cache (Least Recently Used)

```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key):
        if key not in self.cache:
            return -1
        # Перемещаем в конец (недавно использованный)
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key, value):
        if key in self.cache:
            # Обновляем и перемещаем в конец
            self.cache.move_to_end(key)
        self.cache[key] = value
        
        # Если превышен лимит, удаляем самый старый
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

# Использование
cache = LRUCache(3)
cache.put('a', 1)
cache.put('b', 2)
cache.put('c', 3)
print(cache.cache)  # OrderedDict({'a': 1, 'b': 2, 'c': 3})

cache.get('a')  # 'a' перемещается в конец
print(cache.cache)  # OrderedDict({'b': 2, 'c': 3, 'a': 1})

cache.put('d', 4)  # 'b' удаляется (самый старый)
print(cache.cache)  # OrderedDict({'c': 3, 'a': 1, 'd': 4})
```

### Пример 4: Очередь задач с приоритетами

```python
from collections import OrderedDict

class TaskQueue:
    def __init__(self):
        self.queue = OrderedDict()
    
    def add_task(self, task_id, priority='normal'):
        self.queue[task_id] = priority
        
        # Высокий приоритет — перемещаем в начало
        if priority == 'high':
            self.queue.move_to_end(task_id, last=False)
    
    def process_next(self):
        if self.queue:
            return self.queue.popitem(last=False)
        return None

# Использование
tasks = TaskQueue()
tasks.add_task('task1', 'normal')
tasks.add_task('task2', 'normal')
tasks.add_task('task3', 'high')  # Высокий приоритет

print(tasks.queue)
# OrderedDict({'task3': 'high', 'task1': 'normal', 'task2': 'normal'})

print(tasks.process_next())  # ('task3', 'high')
print(tasks.process_next())  # ('task1', 'normal')
```

### Пример 5: История просмотров

```python
from collections import OrderedDict

class BrowserHistory:
    def __init__(self, max_history=10):
        self.history = OrderedDict()
        self.max_history = max_history
    
    def visit(self, url):
        # Если URL уже есть, перемещаем в конец
        if url in self.history:
            self.history.move_to_end(url)
        else:
            self.history[url] = True
        
        # Ограничиваем размер истории
        if len(self.history) > self.max_history:
            self.history.popitem(last=False)
    
    def get_recent(self, n=5):
        items = list(self.history.keys())
        return items[-n:][::-1]  # Последние n в обратном порядке

# Использование
browser = BrowserHistory(max_history=5)
browser.visit('google.com')
browser.visit('github.com')
browser.visit('python.org')
browser.visit('google.com')  # Повторный визит

print(browser.get_recent(3))
# ['google.com', 'python.org', 'github.com']
```

### Пример 6: Объединение словарей (Python 3.9+)

```python
from collections import OrderedDict

# Оператор | для объединения
physicists = OrderedDict(newton='1642-1726', einstein='1879-1955')
biologists = OrderedDict(darwin='1809-1882', mendel='1822-1884')

scientists = physicists | biologists
print(scientists)
# OrderedDict({'newton': '1642-1726', 'einstein': '1879-1955',
#              'darwin': '1809-1882', 'mendel': '1822-1884'})

# Оператор |= для обновления
physicists |= OrderedDict(hawking='1942-2018')
print(physicists)
# OrderedDict({'newton': '1642-1726', 'einstein': '1879-1955',
#              'hawking': '1942-2018'})
```

## Динамические атрибуты

OrderedDict имеет атрибут `__dict__`, которого нет у обычного dict. Это позволяет добавлять пользовательские атрибуты:

```python
from collections import OrderedDict

letters = OrderedDict(b=2, d=4, a=1, c=3)

# Добавляем пользовательский метод
letters.sorted_keys = lambda: sorted(letters.keys())

print(letters.sorted_keys())  # ['a', 'b', 'c', 'd']

# Можно добавлять через __dict__
letters.__dict__['custom_attr'] = 'значение'
print(letters.__dict__)
# {'sorted_keys': <function>, 'custom_attr': 'значение'}

# Обычный dict не поддерживает это
regular_dict = {'a': 1}
try:
    regular_dict.custom = 'value'
except AttributeError as e:
    print(e)  # 'dict' object has no attribute 'custom'
```

### Практическое применение

```python
from collections import OrderedDict

class ConfigDict(OrderedDict):
    """Словарь с дополнительными методами"""
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        # Добавляем метод для получения обязательных ключей
        self.required_keys = []
    
    def validate(self):
        """Проверка наличия обязательных ключей"""
        missing = [k for k in self.required_keys if k not in self]
        if missing:
            raise ValueError(f"Отсутствуют ключи: {missing}")
        return True

# Использование
config = ConfigDict(host='localhost', port=8080)
config.required_keys = ['host', 'port', 'database']

try:
    config.validate()
except ValueError as e:
    print(e)  # Отсутствуют ключи: ['database']
```

## OrderedDict vs dict: Сравнительная таблица

|Функционал|OrderedDict|dict|
|---|---|---|
|**Сохранение порядка вставки**|✅ С Python 3.1|✅ С Python 3.7 (официально)|
|**Явное указание намерения**|✅ Высокая читаемость|⚠️ Неявно|
|**Изменение порядка элементов**|✅ `move_to_end()`|❌ Нет|
|**Удаление с начала**|✅ `popitem(last=False)`|❌ Только с конца|
|**Учет порядка при сравнении**|✅ Да|❌ Нет|
|**Обратный перебор**|✅ С Python 3.5|✅ С Python 3.8|
|**Динамические атрибуты**|✅ `__dict__`|❌ Нет|
|**Операторы `\|` и `\|=`**|✅ С Python 3.9|✅ С Python 3.9|
|**Производительность**|⚠️ ~40% медленнее|✅ Быстрее|
|**Потребление памяти**|⚠️ ~50% больше|✅ Меньше|
|**Реализация**|C (двусвязный список)|C (хеш-таблица)|

## Когда использовать OrderedDict?

### ✅ Используйте OrderedDict когда:

1. **Порядок критически важен для алгоритма**
    
    ```python
    # LRU Cache, очереди, история
    ```
    
2. **Нужно изменять порядок элементов**
    
    ```python
    cache.move_to_end('recent_item')
    ```
    
3. **Нужно удалять с начала**
    
    ```python
    queue.popitem(last=False)  # FIFO
    ```
    
4. **Сравнение с учетом порядка**
    
    ```python
    config1 == config2  # Порядок важен
    ```
    
5. **Явная сигнализация о важности порядка**
    
    ```python
    # Код самодокументируется
    ordered_config = OrderedDict(...)
    ```
    
6. **Обратная совместимость с Python < 3.7**
    
7. **Нужны динамические атрибуты**
    
    ```python
    d.custom_method = lambda: ...
    ```
    

### ❌ Не используйте OrderedDict когда:

1. **Порядок не важен**
    
    ```python
    # Используйте обычный dict
    ```
    
2. **Нужна максимальная производительность**
    
    ```python
    # dict на 40% быстрее
    ```
    
3. **Критична экономия памяти**
    
    ```python
    # dict использует на 50% меньше памяти
    ```
    
4. **Работаете только с Python 3.7+**
    
    ```python
    # dict уже упорядочен
    ```
    
5. **Не нужны специальные методы**
    
    ```python
    # move_to_end(), popitem(last=False)
    ```
    

## Производительность

```python
import timeit
from collections import OrderedDict

# Создание
dict_time = timeit.timeit('d = dict.fromkeys(range(1000))', number=10000)
ordered_time = timeit.timeit(
    'from collections import OrderedDict; d = OrderedDict.fromkeys(range(1000))',
    number=10000
)

print(f"dict: {dict_time:.4f}s")
print(f"OrderedDict: {ordered_time:.4f}s")
print(f"OrderedDict медленнее в {ordered_time/dict_time:.2f}x раз")
```

**💻 Типичные результаты:**

- OrderedDict ~40% медленнее при создании
- OrderedDict ~50% больше потребляет памяти
- `move_to_end()` и `popitem(last=False)` работают за O(1)

## Изменения в Python 3.12

В Python 3.12 изменилось строковое представление OrderedDict:

```python
from collections import OrderedDict

numbers = OrderedDict(one=1, two=2, three=3)

# Python 3.11 и ранее
print(numbers)
# OrderedDict([('one', 1), ('two', 2), ('three', 3)])

# Python 3.12+
print(numbers)
# OrderedDict({'one': 1, 'two': 2, 'three': 3})
```

Новый формат более компактный и читаемый.

## Распространенные ошибки

### ❌ Ошибка 1: Забыли про KeyError в move_to_end()

```python
from collections import OrderedDict

d = OrderedDict(a=1, b=2)

# Неправильно
try:
    d.move_to_end('c')  # KeyError!
except KeyError:
    print("Ключ не найден")

# Правильно
if 'c' in d:
    d.move_to_end('c')
```

### ❌ Ошибка 2: Использование OrderedDict без причины

```python
# Плохо (Python 3.7+)
from collections import OrderedDict
d = OrderedDict(a=1, b=2, c=3)
# Порядок не используется, нет специальных методов

# Хорошо
d = {'a': 1, 'b': 2, 'c': 3}
# dict быстрее и экономичнее
```

### ❌ Ошибка 3: Ожидание упорядоченности kwargs

```python
# Порядок именованных аргументов не гарантирован в старых версиях
d = OrderedDict(c=3, a=1, b=2)
# Может быть не в порядке c, a, b

# Правильно — используйте список кортежей
d = OrderedDict([('c', 3), ('a', 1), ('b', 2)])
# Гарантирует порядок: c, a, b
```

### ❌ Ошибка 4: Путаница с popitem()

```python
from collections import OrderedDict

d = OrderedDict(a=1, b=2, c=3)

# dict.popitem() — только LIFO (с конца)
# OrderedDict.popitem(last=False) — можно FIFO (с начала)

d.popitem()            # ('c', 3) — с конца
d.popitem(last=False)  # ('a', 1) — с начала
```

## Полезные ссылки

- [Официальная документация OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict)
- [PEP 468 — Preserving Keyword Argument Order](https://www.python.org/dev/peps/pep-0468/)
- [PEP 372 — Adding an ordered dictionary to collections](https://www.python.org/dev/peps/pep-0372/)
- [Исходный код OrderedDict](https://github.com/python/cpython/blob/main/Lib/collections/__init__.py)

## 🔗 Связанные темы

- [[04 — 📖 Словарь ( dict ) 🔹]]
- [[04.2  ⤷ Упорядоченность и хранение словаря]]
