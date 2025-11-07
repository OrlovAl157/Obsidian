
## Таблица основных функций

|Функция|Описание|Пример использования|
|:-:|:-:|:--|
|`dump(obj, file, protocol)`|Сериализовать объект в файл|`pickle.dump(data, file)`|
|`dumps(obj, protocol)`|Сериализовать объект в bytes|`bytes_data = pickle.dumps(data)`|
|`load(file)`|Десериализовать из файла|`data = pickle.load(file)`|
|`loads(bytes_object)`|Десериализовать из bytes|`data = pickle.loads(bytes_data)`|

---

## Что такое pickle?

**Pickle** — встроенный модуль Python для сериализации и десериализации объектов.

- **Сериализация (pickling)** — преобразование объекта Python в байтовый поток для сохранения или передачи
- **Десериализация (unpickling)** — восстановление объекта из байтового потока

### Что можно сериализовать?

✅ Числа, строки, булевы значения  
✅ Списки, кортежи, словари, множества  
✅ Функции и классы (определенные на верхнем уровне модуля)  
✅ Экземпляры классов  
✅ Вложенные структуры данных

### Что нельзя сериализовать?

❌ Открытые файлы  
❌ Сетевые соединения  
❌ Потоки выполнения (threads)  
❌ Лямбда-функции и вложенные функции  
❌ Объекты баз данных

---

## Параметры

### Протоколы сериализации

- **`protocol=0`** — ASCII формат (читаемый, совместим со старыми версиями)
- **`protocol=1`** — старый бинарный формат
- **`protocol=2`** — бинарный формат (Python 2.3+)
- **`protocol=3`** — бинарный формат (Python 3.0+)
- **`protocol=4`** — бинарный формат (Python 3.4+, поддержка больших объектов)
- **`protocol=5`** — бинарный формат (Python 3.8+, быстрее для больших данных)
- **`pickle.HIGHEST_PROTOCOL`** — использовать максимально доступный протокол
- **`pickle.DEFAULT_PROTOCOL`** — протокол по умолчанию (обычно 4 или 5)

---

## Основные операции

### 1. Сохранение в файл (dump)

```python
import pickle

data = {'name': 'Alice', 'age': 30, 'scores': [85, 90, 95]}

# Сохранение в файл (ВСЕГДА в бинарном режиме 'wb')
with open('data.pkl', 'wb') as file:
    pickle.dump(data, file)

# С указанием протокола
with open('data.pkl', 'wb') as file:
    pickle.dump(data, file, protocol=pickle.HIGHEST_PROTOCOL)
```

### 2. Загрузка из файла (load)

```python
import pickle

# Загрузка из файла (ВСЕГДА в бинарном режиме 'rb')
with open('data.pkl', 'rb') as file:
    data = pickle.load(file)
    print(data)  # {'name': 'Alice', 'age': 30, 'scores': [85, 90, 95]}
```

### 3. Сериализация в строку байтов (dumps)

```python
import pickle

data = [1, 2, 3, 4, 5]

# Преобразование в bytes
bytes_data = pickle.dumps(data)
print(type(bytes_data))  # <class 'bytes'>
print(bytes_data)        # b'\x80\x04\x95\x0f\x00\x00\x00...'

# С протоколом
bytes_data = pickle.dumps(data, protocol=4)
```

### 4. Десериализация из bytes (loads)

```python
import pickle

bytes_data = b'\x80\x04\x95\x0f\x00\x00\x00\x00\x00\x00\x00]\x94(K\x01K\x02K\x03K\x04K\x05e.'

# Восстановление объекта
data = pickle.loads(bytes_data)
print(data)  # [1, 2, 3, 4, 5]
```

### 5. Сохранение и загрузка нескольких объектов

```python
import pickle

# Сохранение нескольких объектов
with open('multiple.pkl', 'wb') as file:
    pickle.dump([1, 2, 3], file)
    pickle.dump({'key': 'value'}, file)
    pickle.dump('Hello', file)

# Загрузка в том же порядке
with open('multiple.pkl', 'rb') as file:
    list_data = pickle.load(file)
    dict_data = pickle.load(file)
    str_data = pickle.load(file)
    print(list_data, dict_data, str_data)
```

### 6. Работа с классами

```python
import pickle

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __repr__(self):
        return f'Person({self.name}, {self.age})'

# Сериализация объекта класса
person = Person('Bob', 25)
with open('person.pkl', 'wb') as file:
    pickle.dump(person, file)

# Десериализация
with open('person.pkl', 'rb') as file:
    loaded_person = pickle.load(file)
    print(loaded_person)  # Person(Bob, 25)
```

---

## Практические примеры

### Сохранение состояния игры

```python
import pickle

game_state = {
    'level': 5,
    'score': 12500,
    'inventory': ['sword', 'shield', 'potion'],
    'position': (10, 25)
}

# Сохранение
with open('savegame.pkl', 'wb') as file:
    pickle.dump(game_state, file)

# Загрузка
with open('savegame.pkl', 'rb') as file:
    loaded_state = pickle.load(file)
    print(f"Уровень: {loaded_state['level']}")
    print(f"Очки: {loaded_state['score']}")
```

### Кэширование результатов вычислений

```python
import pickle
import os

def expensive_calculation(n):
    return sum(i**2 for i in range(n))

def get_cached_result(n, cache_file='cache.pkl'):
    # Попытка загрузить из кэша
    if os.path.exists(cache_file):
        with open(cache_file, 'rb') as file:
            cache = pickle.load(file)
            if n in cache:
                print('Загружено из кэша')
                return cache[n]
    else:
        cache = {}
    
    # Вычисление и сохранение
    result = expensive_calculation(n)
    cache[n] = result
    
    with open(cache_file, 'wb') as file:
        pickle.dump(cache, file)
    
    return result

print(get_cached_result(1000000))  # Вычисляет
print(get_cached_result(1000000))  # Загружает из кэша
```

### Копирование сложных объектов

```python
import pickle
import copy

data = {'a': [1, 2, 3], 'b': {'x': 10}}

# Глубокое копирование через pickle
data_copy = pickle.loads(pickle.dumps(data))
data_copy['a'].append(4)

print(data)       # {'a': [1, 2, 3], 'b': {'x': 10}}
print(data_copy)  # {'a': [1, 2, 3, 4], 'b': {'x': 10}}
```

---

## ⚠️ Безопасность

**ВАЖНО:** Никогда не загружайте pickle-файлы из ненадежных источников!

```python
# ❌ ОПАСНО! Не делайте так с файлами из интернета
with open('untrusted.pkl', 'rb') as file:
    data = pickle.load(file)  # Может выполнить вредоносный код!
```

**Почему опасно?**

- Pickle может выполнять произвольный Python-код при десериализации
- Злоумышленник может создать вредоносный pickle-файл
- Используйте pickle только для доверенных данных

**Альтернативы для обмена данными:**

- JSON (для простых данных)
- XML, YAML (для структурированных данных)
- Protocol Buffers, MessagePack (для бинарных данных)

---

## Обработка ошибок

```python
import pickle

try:
    with open('data.pkl', 'rb') as file:
        data = pickle.load(file)
except FileNotFoundError:
    print('Файл не найден')
except pickle.UnpicklingError:
    print('Ошибка десериализации (поврежденный файл)')
except EOFError:
    print('Файл пуст или неполный')
except Exception as e:
    print(f'Неизвестная ошибка: {e}')
```

---

## Полезные советы

1. **Всегда используйте бинарный режим** (`'wb'` и `'rb'`) при работе с файлами
2. **Используйте `with`** для автоматического закрытия файлов
3. **Указывайте протокол** для совместимости между версиями Python
4. **Не храните пароли** и конфиденциальные данные в pickle (они не зашифрованы)
5. **Для обмена данными** между программами предпочтительнее JSON
6. **Для долговременного хранения** данных рассмотрите использование баз данных
7. **Проверяйте версии** — pickle-файлы могут быть несовместимы между версиями Python

---

## Сравнение pickle и JSON

|Характеристика|pickle|JSON|
|:-:|:-:|:-:|
|Формат|Бинарный|Текстовый|
|Читаемость|Нет|Да|
|Скорость|Быстрее|Медленнее|
|Размер файла|Меньше|Больше|
|Типы данных|Почти все Python объекты|Базовые типы|
|Безопасность|⚠️ Небезопасен|✅ Безопасен|
|Кросс-языковая поддержка|Только Python|Все языки|
|Применение|Внутреннее хранение|Обмен данными|