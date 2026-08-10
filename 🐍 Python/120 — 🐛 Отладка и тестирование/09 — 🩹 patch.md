---
tags: [python, тестирование, mock, patch, unittest]
difficulty: advanced
---

# 🩹 patch — памятка

> `patch` — декоратор/контекстный менеджер из `unittest.mock` который временно заменяет реальный объект на мок внутри указанной области кода, а после выхода возвращает всё как было. Основной способ подключения моков в тестах.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 patch как декоратор|patch как декоратор]]
- [[#🔵 patch как контекстный менеджер|patch как контекстный менеджер]]
- [[#🔴 patch.object — без строкового пути|patch.object]]
- [[#🟡 patch.multiple — несколько объектов|patch.multiple]]
- [[#🟣 mock_open — подмена open()|mock_open]]
- [[#⚙️ Несколько декораторов patch|Несколько декораторов]]
- [[#🎯 Практические примеры|Практические примеры]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Способ | Синтаксис | Когда использовать |
|---|---|---|
| Декоратор | `@patch('module.object')` | заменить во всей функции/методе |
| Контекстный менеджер | `with patch('module.object') as m:` | заменить только в части кода |
| `patch.object` | `patch.object(Class, 'method')` | объект уже импортирован, нет строки |
| `patch.multiple` | `patch.multiple(obj, a=Mock(), b=Mock())` | несколько замен одним вызовом |
| `mock_open` | `patch('builtins.open', mock_open(...))` | подмена `open()` |

---

## 📊 Общая схема

```
Правило: патчить нужно там, где объект ИСПОЛЬЗУЕТСЯ,
         а не там, где он ОПРЕДЕЛЁН

# my_module.py
import os

def remove_file(path):
    os.remove(path)            ← os.remove используется здесь

# test_my_module.py
@patch('my_module.os.remove')  ← патчим в my_module, не в os!
def test(mock_remove):
    remove_file('test.txt')
    mock_remove.assert_called_with('test.txt')

Порядок параметров при нескольких @patch (снизу вверх!):
    @patch('module.A')   ← второй параметр
    @patch('module.B')   ← первый параметр
    def test(self, mock_b, mock_a):
```

---

## 🟢 patch как декоратор

```python
from unittest.mock import patch
import unittest


def find_max(seq):
    if seq:
        return max(seq)
    return -1


class TestFindMax(unittest.TestCase):

    @patch('__main__.max')             # патчим max в текущем модуле
    def test_with_sequence(self, mock_max):
        mock_max.return_value = 99
        result = find_max([1, 2, 3])

        mock_max.assert_called_once_with([1, 2, 3])
        self.assertEqual(result, 99)

    @patch('__main__.max')
    def test_empty_sequence(self, mock_max):
        result = find_max([])
        mock_max.assert_not_called()   # max не должен вызываться на пустом списке
        self.assertEqual(result, -1)
```

**Декоратор на классе** — замена применяется ко **всем** тестовым методам класса:

```python
@patch('my_module.requests.get')
class TestAPI(unittest.TestCase):
    def test_success(self, mock_get):   # mock_get приходит в каждый метод
        mock_get.return_value.status_code = 200
        ...

    def test_error(self, mock_get):
        mock_get.side_effect = ConnectionError
        ...
```

**Важное:** декоратор на классе НЕ применяется к `setUp`/`tearDown` — там объект остаётся настоящим.

---

## 🔵 patch как контекстный менеджер

Когда замена нужна только в части функции:

```python
def test_partial():
    result1 = max([1, 2, 3])     # реальная функция → 3

    with patch('__main__.max') as mock_max:
        mock_max.return_value = 99
        result2 = max([1, 2, 3]) # мок → 99
        print(mock_max.called)   # True

    result3 = max([1, 2, 3])     # снова реальная → 3

    assert result1 == 3
    assert result2 == 99
    assert result3 == 3
```

В тестовом классе:

```python
class TestSomething(unittest.TestCase):

    def test_with_context(self):
        with patch('my_module.requests.get') as mock_get:
            mock_get.return_value.json.return_value = {'key': 'value'}
            result = my_module.fetch_data()
            mock_get.assert_called_once()
        self.assertEqual(result['key'], 'value')
```

---

## 🔴 patch.object — без строкового пути

Когда класс/объект уже импортирован и не хочется писать строку с полным путём:

```python
from unittest.mock import patch
import my_module


class Cat:
    def meow(self):
        return 'Мяу'

    def eat(self, food):
        return f'Ем {food}'


cat = Cat('Кемаль')

# Патчим метод класса
with patch.object(Cat, 'meow') as mock_meow:
    mock_meow.return_value = 'Тихо...'
    print(cat.meow())      # → 'Тихо...'

print(cat.meow())          # → 'Мяу' — восстановлено

# Патчим метод объекта
with patch.object(cat, 'eat', return_value='Ем что дали') as mock_eat:
    print(cat.eat('рыбу')) # → 'Ем что дали'
    mock_eat.assert_called_with('рыбу')
```

---

## 🟡 patch.multiple — несколько объектов

Заменяет несколько атрибутов одного объекта за один раз:

```python
from unittest.mock import patch, DEFAULT

# Как декоратор
@patch.multiple('my_module', requests=DEFAULT, datetime=DEFAULT)
def test(requests, datetime):
    requests.get.return_value.status_code = 200
    datetime.now.return_value = '2025-07-15'
    ...

# Как контекстный менеджер
with patch.multiple('my_module', requests=DEFAULT, datetime=DEFAULT) as mocks:
    mocks['requests'].get.return_value.status_code = 200
    mocks['datetime'].now.return_value = '2025-07-15'
    ...
```

---

## 🟣 mock_open — подмена open()

Стандартный `Mock` не имитирует поведение файлового объекта. `mock_open` создаёт мок который корректно работает и с прямым вызовом и с `with open(...) as f`:

```python
from unittest.mock import patch, mock_open
import unittest


def read_file(path):
    with open(path) as f:
        return f.read()


class TestReadFile(unittest.TestCase):

    def test_read(self):
        fake_content = 'Hello, World!'
        m = mock_open(read_data=fake_content)

        with patch('builtins.open', m):
            result = read_file('test.txt')

        self.assertEqual(result, fake_content)
        m.assert_called_once_with('test.txt')

    def test_write(self):
        m = mock_open()

        with patch('builtins.open', m):
            with open('output.txt', 'w') as f:
                f.write('data')

        m.assert_called_once_with('output.txt', 'w')
        m().write.assert_called_once_with('data')
```

---

## ⚙️ Несколько декораторов patch

При нескольких `@patch` параметры передаются **снизу вверх** — нижний декоратор → первый параметр:

```python
@patch('my_module.os.remove')      # ← второй параметр mock_remove
@patch('my_module.requests.get')   # ← первый параметр mock_get
def test(self, mock_get, mock_remove):  # снизу вверх!
    mock_get.return_value.status_code = 200
    mock_remove.return_value = None
    ...
```

```
Декораторы:     Параметры:
@patch('A')     mock_b  ← A (верхний) → второй параметр
@patch('B')     mock_a  ← B (нижний)  → первый параметр
def test(self, mock_a, mock_b):
```

---

## 🎯 Практические примеры

**Подмена datetime.now():**

```python
import unittest
from unittest.mock import patch
from datetime import datetime


def is_weekend():
    return datetime.now().weekday() >= 5


class TestIsWeekend(unittest.TestCase):

    @patch('__main__.datetime')
    def test_saturday(self, mock_dt):
        mock_dt.now.return_value = datetime(2025, 7, 19)  # суббота
        self.assertTrue(is_weekend())

    @patch('__main__.datetime')
    def test_monday(self, mock_dt):
        mock_dt.now.return_value = datetime(2025, 7, 14)  # понедельник
        self.assertFalse(is_weekend())
```

**Подмена os.remove — проверяем вызов без реального удаления:**

```python
import os
import unittest
from unittest.mock import patch


def rm(filename):
    os.remove(filename)


class TestRm(unittest.TestCase):

    @patch('my_module.os.remove')     # патчим в том модуле где используется!
    def test_rm_called(self, mock_remove):
        rm('test.txt')
        mock_remove.assert_called_once_with('test.txt')

    @patch('my_module.os.remove', side_effect=FileNotFoundError)
    def test_rm_file_not_found(self, mock_remove):
        with self.assertRaises(FileNotFoundError):
            rm('nonexistent.txt')
```

**Подмена requests.get — имитация HTTP:**

```python
import requests
import unittest
from unittest.mock import patch, Mock


def get_status(url):
    response = requests.get(url)
    return response.status_code


class TestGetStatus(unittest.TestCase):

    @patch('my_module.requests.get')
    def test_200(self, mock_get):
        mock_get.return_value = Mock(status_code=200)
        self.assertEqual(get_status('https://example.com'), 200)

    @patch('my_module.requests.get')
    def test_404(self, mock_get):
        mock_get.return_value = Mock(status_code=404)
        self.assertEqual(get_status('https://example.com/missing'), 404)

    @patch('my_module.requests.get', side_effect=requests.Timeout)
    def test_timeout(self, mock_get):
        with self.assertRaises(requests.Timeout):
            get_status('https://slow.example.com')
```

---

## ⚡ Быстрые примеры

```python
from unittest.mock import patch, Mock, mock_open

# Декоратор
@patch('module.ClassName')
def test(mock_cls):
    mock_cls.return_value.method.return_value = 42
    ...

# Контекстный менеджер
with patch('module.function') as mock_fn:
    mock_fn.return_value = 'result'
    ...

# patch.object
with patch.object(MyClass, 'method', return_value=42) as mock:
    obj = MyClass()
    obj.method()
    mock.assert_called_once()

# side_effect прямо в декораторе
@patch('module.func', side_effect=ValueError('ошибка'))
def test(mock_func):
    with assertRaises(ValueError):
        module.func()

# mock_open
with patch('builtins.open', mock_open(read_data='content')):
    with open('file.txt') as f:
        assert f.read() == 'content'
```

---

## 💡 Практические замечания

- Патчить нужно там где объект **используется**, а не там где определён
- `@patch` на классе применяется ко всем тестовым методам, но не к `setUp/tearDown`
- `patch.object` удобен когда нет строки пути — объект уже импортирован
- `mock_open` — всегда для `open()`, обычный `Mock` не имитирует файловый объект корректно
- Встроенные типы (`datetime.datetime`) патчатся целиком, не по атрибуту
- При нескольких `@patch` помни: нижний → первый параметр (снизу вверх)

---

## ⚠️ Частые ошибки

**❌ Патчишь там где определён, а не там где используется:**
```python
# my_module.py использует: from os import remove
@patch('os.remove')         # ❌ не сработает!
@patch('my_module.remove')  # ✅ патчим в том модуле где используется
def test(mock_remove): ...
```

**❌ Забыл параметр при @patch:**
```python
@patch('module.func')
def test(self):              # ❌ TypeError — нет параметра для мока!
    ...

@patch('module.func')
def test(self, mock_func):   # ✅
    ...
```

**❌ Неправильный порядок параметров при нескольких @patch:**
```python
@patch('module.A')   # второй параметр
@patch('module.B')   # первый параметр
def test(self, mock_a, mock_b):  # ❌ перепутан порядок!
    ...
def test(self, mock_b, mock_a):  # ✅ нижний декоратор → первый параметр
    ...
```

**❌ Используешь обычный Mock для open():**
```python
with patch('builtins.open', Mock()):   # ❌ не работает с 'with open() as f'
    with open('file.txt') as f:
        f.read()  # AttributeError

with patch('builtins.open', mock_open(read_data='content')):  # ✅
    with open('file.txt') as f:
        f.read()
```

---

## ✅ Главные правила

✅ Патчить там где **используется**, а не там где определён  
✅ `@patch` — заменяет во всей функции, нужен доп. параметр  
✅ `with patch(...) as mock:` — заменяет только внутри блока  
✅ Несколько `@patch` — параметры снизу вверх  
✅ `patch.object(Class, 'method')` — когда объект уже импортирован  
✅ `mock_open()` — для подмены `open()`, не обычный `Mock`  
✅ `@patch` на классе — применяется ко всем методам, но не к `setUp/tearDown`  
✅ `side_effect` можно задать прямо в `@patch('module.func', side_effect=...)`  

---

## 🔗 Связанные темы

- [[07 — 🎭 Mock — основы]]
- [[08 — 🔎 Mock — отслеживание вызовов]]
- [[03 — 🧩 unittest — основы]]

---

#python/тестирование #mock #patch #unittest
