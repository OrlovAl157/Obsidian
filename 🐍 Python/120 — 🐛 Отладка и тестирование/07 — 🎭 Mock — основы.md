---
tags: [python, тестирование, mock, unittest]
difficulty: advanced
---

# 🎭 Mock — основы — памятка

> Mock-объекты имитируют реальные объекты в тестах — позволяют управлять их поведением и отслеживать вызовы без выполнения реальной логики (БД, сеть, время, файлы). Класс `Mock` из `unittest.mock`.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Создание Mock|Создание Mock]]
- [[#🔵 return_value — что вернуть|return_value]]
- [[#🔴 side_effect — побочный эффект|side_effect]]
- [[#🟡 MagicMock и spec|MagicMock и spec]]
- [[#🟣 configure_mock — массовая настройка|configure_mock]]
- [[#🎯 Практический пример|Практический пример]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Инструмент | Назначение |
|---|---|
| `Mock()` | базовый мок — имитирует любые атрибуты и методы |
| `MagicMock()` | расширенный мок — поддерживает магические методы |
| `mock.return_value` | что вернуть при вызове мока |
| `mock.side_effect` | функция / исключение / итерируемый — управляет побочным эффектом |
| `Mock(spec=Class)` | мок ограничен интерфейсом реального класса |
| `Mock(name='...')` | имя для читаемого вывода в отчёте |
| `mock.configure_mock(**kwargs)` | массовая настройка атрибутов |
| `mock.reset_mock()` | сбросить счётчики вызовов |

---

## 📊 Общая схема

```
Без мока:
  test → function → datetime.now()  ← реальное время, меняется каждый день
                  → БД              ← реальная БД, медленно, побочные эффекты
                  → HTTP запрос     ← реальная сеть, может упасть

С моком:
  test → Mock()   → return_value = dt  ← фиксированное время, всегда одно
              └──→ side_effect = exc   ← имитация ошибки
              └──→ side_effect = [v1, v2]  ← разные ответы по порядку

Когда нужен мок:
  ├── Функция зависит от текущего времени
  ├── Функция делает HTTP-запрос
  ├── Функция работает с БД
  ├── Функция читает/пишет файл
  └── Функция зависит от случайных чисел
```

---

## 🟢 Создание Mock

```python
from unittest.mock import Mock

# Базовый мок — имитирует любые атрибуты и методы
mock = Mock()

# Мок имитирует произвольную вложенность — "на лету"
mock.a                    # → <Mock name='mock.a'>
mock.a.b.c                # → <Mock name='mock.a.b.c'>
mock.get()                # → <Mock name='mock.get()'>
mock.get().max()          # → <Mock name='mock.get()().max()'>

# С именем — для читаемого вывода в отчёте
mock = Mock(name='requests')
mock.get.return_value     # → <Mock name='requests.get()'>

# Вызов мока — возвращает другой мок по умолчанию
result = mock()           # → <Mock name='mock()'>
type(result)              # → <class 'unittest.mock.Mock'>
```

---

## 🔵 return_value — что вернуть при вызове

По умолчанию вызов мока возвращает другой мок. Заменяем на нужное значение:

```python
from unittest.mock import Mock
from datetime import datetime

# Простое значение
mock = Mock()
mock.return_value = 42
mock()            # → 42
mock()            # → 42 (всегда одно и то же)

# Возвращаемое значение метода
mock.get.return_value = {'status': 200, 'data': 'ok'}
mock.get()        # → {'status': 200, 'data': 'ok'}

# Вложенный return_value
mock.get.return_value.json.return_value = {'key': 'value'}
mock.get().json()  # → {'key': 'value'}
```

**Практический пример — подмена datetime:**

```python
from unittest.mock import Mock
from datetime import datetime


def is_weekend():
    return datetime.now().weekday() >= 5   # 5=суббота, 6=воскресенье


# В тесте подменяем datetime
datetime_mock = Mock(name='datetime')
datetime_mock.now.return_value = datetime(2025, 7, 19)  # суббота

# Теперь результат предсказуем
import my_module
my_module.datetime = datetime_mock
assert is_weekend() == True
```

---

## 🔴 side_effect — побочный эффект

`side_effect` имеет **приоритет** над `return_value`. Принимает три вида значений:

**1. Вызываемый объект — функция:**

```python
mock = Mock()

def my_side_effect(a, b):
    return a + b

mock.side_effect = my_side_effect
mock(1, 2)    # → 3  (вызвана функция, return_value игнорируется)
mock(10, 20)  # → 30
```

**2. Исключение — тип или объект:**

```python
mock = Mock()
mock.side_effect = ValueError('Сервер недоступен')
mock()   # → ValueError: Сервер недоступен

# Тип исключения (без аргументов)
mock.side_effect = ConnectionError
mock()   # → ConnectionError
```

**3. Итерируемый объект — разные ответы по порядку:**

```python
mock = Mock()
mock.side_effect = [1, 2, 3]
mock()   # → 1
mock()   # → 2
mock()   # → 3
mock()   # → StopIteration

# Можно смешивать значения и исключения
mock.side_effect = [42, ValueError('ошибка'), 99]
mock()   # → 42
mock()   # → ValueError: ошибка
mock()   # → 99
```

**Сброс side_effect:**

```python
mock.side_effect = None   # отключить side_effect, вернуться к return_value
```

---

## 🟡 MagicMock и spec

**MagicMock** — расширение Mock с поддержкой магических методов (`__len__`, `__iter__`, `__str__` и т.д.):

```python
from unittest.mock import Mock, MagicMock

# Mock — магические методы не работают
mock = Mock()
len(mock)          # → TypeError

# MagicMock — магические методы работают
magic = MagicMock()
len(magic)         # → 0 (по умолчанию)
magic.__len__.return_value = 5
len(magic)         # → 5

# Итерация
magic.__iter__.return_value = iter([1, 2, 3])
list(magic)        # → [1, 2, 3]

# Контекстный менеджер
with magic as m:
    pass           # работает!
```

**spec — ограничение интерфейса:**

```python
class Cat:
    def meow(self):
        return 'Мяу'

    def eat(self, food):
        return f'Ем {food}'

# Без spec — можно вызывать несуществующие методы
mock = Mock()
mock.fly()         # работает (хотя у Cat нет fly)

# С spec — только реальные методы
mock = Mock(spec=Cat)
mock.meow()        # ✅ работает
mock.fly()         # ❌ AttributeError — fly нет в Cat
mock.eat('рыбу')   # ✅ работает
```

---

## 🟣 configure_mock — массовая настройка

Настроить несколько атрибутов одним вызовом:

```python
from unittest.mock import Mock

mock = Mock()
mock.configure_mock(
    return_value=42,
    **{'get.return_value': 'ok',
       'get.side_effect': None}
)

# Или при создании
mock = Mock(**{
    'return_value': 42,
    'get.return_value': 'response',
    'post.side_effect': ConnectionError
})

mock()           # → 42
mock.get()       # → 'response'
mock.post()      # → ConnectionError
```

---

## 🎯 Практический пример

Тестируем функцию которая делает HTTP-запрос:

```python
# my_module.py
import requests


def get_user(user_id):
    response = requests.get(f'https://api.example.com/users/{user_id}')
    if response.status_code == 200:
        return response.json()
    return None
```

```python
# test_my_module.py
import unittest
from unittest.mock import Mock
import my_module


class TestGetUser(unittest.TestCase):

    def test_success(self):
        # Создаём мок для requests
        mock_response = Mock()
        mock_response.status_code = 200
        mock_response.json.return_value = {'id': 1, 'name': 'Alice'}

        mock_requests = Mock()
        mock_requests.get.return_value = mock_response

        # Подменяем requests в модуле
        my_module.requests = mock_requests

        result = my_module.get_user(1)
        self.assertEqual(result, {'id': 1, 'name': 'Alice'})

    def test_not_found(self):
        mock_response = Mock()
        mock_response.status_code = 404

        mock_requests = Mock()
        mock_requests.get.return_value = mock_response

        my_module.requests = mock_requests

        result = my_module.get_user(999)
        self.assertIsNone(result)

    def test_connection_error(self):
        mock_requests = Mock()
        mock_requests.get.side_effect = ConnectionError('Сервер недоступен')

        my_module.requests = mock_requests

        with self.assertRaises(ConnectionError):
            my_module.get_user(1)
```

---

## ⚡ Быстрые примеры

```python
from unittest.mock import Mock, MagicMock

# Простой return_value
mock = Mock(return_value=42)
mock()               # → 42

# Цепочка return_value
mock = Mock()
mock.a.b.return_value = 'deep'
mock.a.b()           # → 'deep'

# side_effect — функция
mock = Mock(side_effect=lambda x: x * 2)
mock(5)              # → 10

# side_effect — исключение
mock = Mock(side_effect=TypeError('неверный тип'))
mock()               # → TypeError

# side_effect — список
mock = Mock(side_effect=[1, 2, ValueError('стоп')])
mock()               # → 1
mock()               # → 2
mock()               # → ValueError

# MagicMock с len
magic = MagicMock()
magic.__len__.return_value = 10
len(magic)           # → 10

# spec
mock = Mock(spec=list)
mock.append(1)       # ✅
mock.fly()           # ❌ AttributeError

# reset
mock = Mock(return_value=5)
mock()
mock.reset_mock()    # сброс счётчиков вызовов
```

---

## 💡 Практические замечания

- `side_effect` имеет приоритет над `return_value` — если оба заданы, `side_effect` победит
- `MagicMock` — предпочтительный выбор когда объект используется как контекстный менеджер или итерируемый
- `Mock(spec=Class)` помогает поймать ошибки — ты не вызовешь несуществующий метод
- Не подменяй мок напрямую на модуль — используй `patch` (см. файл 09)
- `mock.reset_mock()` — сброс вызовов, но не `return_value` и `side_effect`

---

## ⚠️ Частые ошибки

**❌ Думаешь что side_effect и return_value работают вместе:**
```python
mock = Mock(return_value=42, side_effect=lambda: 99)
mock()   # → 99  (side_effect победил, return_value игнорируется)
```

**❌ Обращаешься к несуществующему атрибуту без spec:**
```python
mock = Mock()
mock.nonexistent_method()   # ✅ не ошибка! Mock создаёт любые атрибуты
# Используй spec чтобы это поймать:
mock = Mock(spec=RealClass)
mock.nonexistent_method()   # ❌ AttributeError — правильно!
```

**❌ Забыл что side_effect со списком исчерпывается:**
```python
mock = Mock(side_effect=[1, 2, 3])
mock()  # 1
mock()  # 2
mock()  # 3
mock()  # StopIteration — неожиданно!
```

**❌ Используешь Mock вместо MagicMock для контекстного менеджера:**
```python
mock = Mock()
with mock:   # ❌ AttributeError: __enter__ не определён
    pass

magic = MagicMock()
with magic:  # ✅
    pass
```

---

## ✅ Главные правила

✅ `Mock()` — имитирует любые атрибуты и методы без ограничений  
✅ `MagicMock()` — когда нужны магические методы (`len`, `iter`, `with`)  
✅ `Mock(spec=Class)` — ограничивает интерфейс реальным классом  
✅ `return_value` — что вернуть при вызове  
✅ `side_effect` — функция, исключение или список; имеет приоритет над `return_value`  
✅ `side_effect=None` — отключить side_effect  
✅ Для подмены объекта в модуле — используй `patch` (файл 09), не прямое присваивание  

---

## 🔗 Связанные темы

- [[08 — 🔎 Mock — отслеживание вызовов]]
- [[09 — 🩹 patch]]
- [[03 — 🧩 unittest — основы]]

---

#python/тестирование #mock #unittest
