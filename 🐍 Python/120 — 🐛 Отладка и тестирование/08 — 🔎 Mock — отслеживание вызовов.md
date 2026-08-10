---
tags: [python, тестирование, mock, unittest]
difficulty: advanced
---

# 🔎 Mock — отслеживание вызовов — памятка

> Моки автоматически запоминают факт, количество и аргументы вызовов. Это позволяет проверять не только результат функции но и **сам факт вызова** — например что определённая ветка `if` действительно выполнилась.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Атрибуты отслеживания|Атрибуты отслеживания]]
- [[#🔵 Assert-методы|Assert-методы]]
- [[#🔴 call_args — детали аргументов|call_args]]
- [[#🟡 mock_calls vs call_args_list|mock_calls vs call_args_list]]
- [[#🟣 reset_mock — сброс истории|reset_mock]]
- [[#🎯 Практический пример|Практический пример]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

**Атрибуты отслеживания:**

| Атрибут | Что показывает |
|---|---|
| `mock.called` | вызывался ли хоть раз (`True`/`False`) |
| `mock.call_count` | сколько раз вызывался |
| `mock.call_args` | аргументы **последнего** вызова (или `None`) |
| `mock.call_args_list` | аргументы **всех** вызовов (список) |
| `mock.mock_calls` | все вызовы включая вложенные через атрибуты |

**Assert-методы:**

| Метод | Проверяет |
|---|---|
| `assert_called()` | вызывался хотя бы раз |
| `assert_called_once()` | вызывался ровно один раз |
| `assert_not_called()` | не вызывался вообще |
| `assert_called_with(*args, **kwargs)` | **последний** вызов с такими аргументами |
| `assert_any_call(*args, **kwargs)` | **хотя бы один** вызов с такими аргументами |
| `assert_called_once_with(*args, **kwargs)` | один раз и именно с такими аргументами |

---

## 📊 Общая схема

```
mock = Mock()

mock(1, 2)          ┐
mock(3, 4)          ├── вызовы запоминаются автоматически
mock.method(5)      ┘

mock.called          → True
mock.call_count      → 2         (только прямые вызовы mock(...))
mock.call_args       → call(3,4) (последний)
mock.call_args_list  → [call(1,2), call(3,4)]
mock.mock_calls      → [call(1,2), call(3,4), call.method(5)]
                                               ↑ вызовы через атрибуты тоже!

mock.method.called   → True      (у каждого атрибута свои счётчики!)
mock.method.call_count → 1
```

---

## 🟢 Атрибуты отслеживания

```python
from unittest.mock import Mock

mock = Mock(name='requests')

# Делаем несколько вызовов
mock.get('https://api.example.com/users')
mock.get('https://api.example.com/posts')
mock.post('https://api.example.com/users', data={'name': 'Alice'})

# called и call_count — у каждого метода свои
print(mock.get.called)           # True
print(mock.get.call_count)       # 2
print(mock.post.called)          # True
print(mock.post.call_count)      # 1
print(mock.called)               # False — mock() напрямую не вызывали!

# call_args — только последний вызов
print(mock.get.call_args)
# call('https://api.example.com/posts')

# call_args_list — все вызовы
print(mock.get.call_args_list)
# [call('https://api.example.com/users'),
#  call('https://api.example.com/posts')]
```

---

## 🔵 Assert-методы

Assert-методы возбуждают `AssertionError` если условие не выполнено — удобно использовать прямо в тестах:

```python
from unittest.mock import Mock

mock = Mock()

# assert_called — вызывался хотя бы раз
mock(1, 2)
mock.assert_called()             # ✅ вызывался
mock.assert_called_once()        # ✅ вызывался один раз

mock(3, 4)
mock.assert_called()             # ✅ всё ещё вызывался
mock.assert_called_once()        # ❌ AssertionError — уже два раза!

# assert_not_called
other = Mock()
other.assert_not_called()        # ✅ не вызывался

# assert_called_with — проверяет ПОСЛЕДНИЙ вызов
mock = Mock()
mock(1, 2, key='value')
mock(3, 4)                       # последний вызов
mock.assert_called_with(3, 4)    # ✅ последний был (3, 4)
mock.assert_called_with(1, 2, key='value')  # ❌ это не последний!

# assert_any_call — хотя бы один вызов с такими аргументами
mock.assert_any_call(1, 2, key='value')  # ✅ такой вызов был

# assert_called_once_with — один раз и именно с этими аргументами
mock = Mock()
mock(42)
mock.assert_called_once_with(42)  # ✅

mock(42)
mock.assert_called_once_with(42)  # ❌ вызван дважды!
```

---

## 🔴 call_args — детали аргументов

`call_args` содержит объект `call` с отдельным доступом к позиционным и именованным аргументам:

```python
from unittest.mock import Mock, call

mock = Mock()
mock(1, 2, name='Alice', age=25)

# Доступ к аргументам
print(mock.call_args)                # call(1, 2, name='Alice', age=25)
print(mock.call_args.args)           # (1, 2)
print(mock.call_args.kwargs)         # {'name': 'Alice', 'age': 25}

# Сравнение с call()
mock.call_args == call(1, 2, name='Alice', age=25)  # True

# Из call_args_list
mock(10, 20)
mock(30, key='val')

for c in mock.call_args_list:
    print(c.args, c.kwargs)
# (1, 2) {'name': 'Alice', 'age': 25}
# (10, 20) {}
# (30,) {'key': 'val'}
```

**Строгость сравнения — позиционные vs именованные:**

```python
mock = Mock()
mock(1, 2, 3)

mock.assert_called_with(1, 2, 3)    # ✅
mock.assert_called_with(1, 2, b=3)  # ❌ AssertionError — позиционный vs именованный!
```

---

## 🟡 mock_calls vs call_args_list

```python
from unittest.mock import Mock

mock = Mock()
mock(1, 2)
mock.method(3)
mock.method.nested(4)

# call_args_list — только прямые вызовы mock(...)
print(mock.call_args_list)
# [call(1, 2)]

# mock_calls — всё: и прямые и через атрибуты
print(mock.mock_calls)
# [call(1, 2), call.method(3), call.method.nested(4)]

# Проверка полной цепочки вызовов
from unittest.mock import call

expected = [call(1, 2), call.method(3), call.method.nested(4)]
assert mock.mock_calls == expected   # ✅
```

---

## 🟣 reset_mock — сброс истории

```python
from unittest.mock import Mock

mock = Mock(return_value=42)
mock(1, 2)
mock(3, 4)

print(mock.call_count)    # 2

# Сброс только истории вызовов
mock.reset_mock()
print(mock.call_count)    # 0
print(mock.return_value)  # 42 — return_value сохранился!

# Полный сброс включая return_value и side_effect
mock.reset_mock(return_value=True, side_effect=True)
```

---

## 🎯 Практический пример

Тестируем что функция вызывает нужные методы с правильными аргументами:

```python
# my_module.py
import smtplib


def send_notification(email, message):
    server = smtplib.SMTP('smtp.gmail.com', 587)
    server.sendmail('from@example.com', email, message)
    server.quit()
```

```python
# test_my_module.py
import unittest
from unittest.mock import Mock, patch
import my_module


class TestSendNotification(unittest.TestCase):

    def test_sendmail_called(self):
        mock_server = Mock()
        mock_smtp = Mock(return_value=mock_server)

        with patch('my_module.smtplib.SMTP', mock_smtp):
            my_module.send_notification('user@test.com', 'Hello!')

        # Проверяем что SMTP создан с правильными параметрами
        mock_smtp.assert_called_once_with('smtp.gmail.com', 587)

        # Проверяем что sendmail вызван с правильными аргументами
        mock_server.sendmail.assert_called_once_with(
            'from@example.com', 'user@test.com', 'Hello!'
        )

        # Проверяем что соединение закрыто
        mock_server.quit.assert_called_once()

    def test_sendmail_not_called_on_error(self):
        mock_server = Mock()
        mock_server.sendmail.side_effect = smtplib.SMTPException

        mock_smtp = Mock(return_value=mock_server)

        with patch('my_module.smtplib.SMTP', mock_smtp):
            try:
                my_module.send_notification('user@test.com', 'Hello!')
            except smtplib.SMTPException:
                pass

        # quit не должен вызываться если sendmail упал
        mock_server.quit.assert_not_called()
```

---

## ⚡ Быстрые примеры

```python
from unittest.mock import Mock, call

mock = Mock()

# Базовое отслеживание
mock(1, 2, 3)
print(mock.called)          # True
print(mock.call_count)      # 1
print(mock.call_args)       # call(1, 2, 3)
print(mock.call_args.args)  # (1, 2, 3)
print(mock.call_args.kwargs) # {}

# Assert-методы
mock.assert_called()
mock.assert_called_once()
mock.assert_called_with(1, 2, 3)
mock.assert_called_once_with(1, 2, 3)

# Несколько вызовов
mock(4, 5)
mock.assert_any_call(1, 2, 3)   # был такой вызов
mock.assert_called_with(4, 5)   # последний был этот

# Проверка всех вызовов
assert mock.call_args_list == [call(1, 2, 3), call(4, 5)]

# Сброс
mock.reset_mock()
mock.assert_not_called()
```

---

## ⚠️ Частые ошибки

**❌ Путаешь assert_called_with и assert_any_call:**
```python
mock(1, 2)
mock(3, 4)

mock.assert_called_with(1, 2)    # ❌ это не последний вызов!
mock.assert_any_call(1, 2)       # ✅ хотя бы один такой вызов был
mock.assert_called_with(3, 4)    # ✅ последний был именно этот
```

**❌ Позиционные и именованные аргументы не взаимозаменяемы:**
```python
mock(1, 2, c=3)
mock.assert_called_with(1, 2, 3)    # ❌ AssertionError
mock.assert_called_with(1, 2, c=3)  # ✅
```

**❌ Думаешь что mock.method.call_count = mock.call_count:**
```python
mock = Mock()
mock.get('url')   # вызван mock.get, а не mock напрямую

print(mock.called)           # False!
print(mock.get.called)       # True ✅
print(mock.get.call_count)   # 1 ✅
```

**❌ reset_mock не сбрасывает return_value:**
```python
mock = Mock(return_value=42)
mock()
mock.reset_mock()
print(mock.return_value)   # 42 — не сбросился!
mock.reset_mock(return_value=True)  # ← явный сброс
```

---

## ✅ Главные правила

✅ `called` / `call_count` — базовая проверка факта вызова  
✅ `assert_called_with` — проверяет **последний** вызов  
✅ `assert_any_call` — проверяет хотя бы один вызов  
✅ `assert_called_once_with` — один раз и именно с этими аргументами  
✅ `call_args.args` / `call_args.kwargs` — раздельный доступ к аргументам  
✅ `mock_calls` включает вызовы через атрибуты, `call_args_list` — только прямые  
✅ Позиционные и именованные аргументы не взаимозаменяемы при проверке  
✅ `reset_mock()` сбрасывает историю, но не `return_value` / `side_effect`  

---

## 🔗 Связанные темы

- [[07 — 🎭 Mock — основы]]
- [[09 — 🩹 patch]]
- [[03 — 🧩 unittest — основы]]

---

#python/тестирование #mock #unittest
