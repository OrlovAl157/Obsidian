---
tags: [python, тестирование, pytest]
difficulty: advanced
---

# 🐍 pytest — основы — памятка

> `pytest` — сторонний фреймворк тестирования (`pip install pytest`). Упрощает и расширяет `unittest`: обычный `assert` вместо десятков методов, меньше шаблонного кода, более подробные отчёты. Умеет запускать и `unittest`-тесты.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Минимальный пример|Минимальный пример]]
- [[#🔵 Проверка исключений|Проверка исключений]]
- [[#🔴 Фикстуры — @pytest.fixture|Фикстуры]]
- [[#🟡 Уровни фикстур — scope|Уровни фикстур]]
- [[#🟣 conftest.py — глобальные фикстуры|conftest.py]]
- [[#⚙️ Запуск тестов|Запуск тестов]]
- [[#🎯 pytest vs unittest|pytest vs unittest]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Требование | Правило |
|---|---|
| Имя функции-теста | префикс `test_` или `test` |
| Имя файла с тестами | префикс `test_` или суффикс `_test` |
| Имя тестового класса | префикс `Test`, **без наследования** |
| Проверка | обычный `assert выражение` |
| Проверка исключения | `with pytest.raises(Тип):` |
| Установка | `pip install pytest` |
| Запуск | `pytest` в терминале |

| Флаг | Что делает |
|---|---|
| `pytest` | запустить все тесты |
| `pytest -v` | подробный вывод |
| `pytest -s` | показывать print() в тестах |
| `pytest test_file.py` | только этот файл |
| `pytest test_file.py::TestClass::test_method` | конкретный тест |
| `pytest -k 'keyword'` | тесты содержащие keyword в имени |
| `pytest -m marker` | тесты с определённым маркером |
| `pytest --doctest-modules` | запустить doctest |
| `pytest -x` | остановиться на первом провале |
| `pytest --tb=short` | краткий traceback |

---

## 📊 Общая схема

```
unittest                        pytest
────────────────────────────    ──────────────────────────────
import unittest                 import pytest

class TestFact(TestCase):       def test_factorial_one():
    def test_one(self):             assert factorial(1) == 1
        self.assertEqual(       
            factorial(1), 1)    def test_factorial_five():
                                    assert factorial(5) == 120

Отчёт об ошибке:
unittest: AssertionError: 120 != 121

pytest:   E       assert 120 == 121
          E        +  where 120 = factorial(5)
          ← развёртывает выражение целиком!
```

---

## 🟢 Минимальный пример

```python
# test_factorial.py
import pytest


def factorial(n):
    if not isinstance(n, int) or n < 0:
        raise ValueError('Некорректный аргумент')
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result


# Функциональный стиль — без класса
def test_zero():
    assert factorial(0) == 1

def test_one():
    assert factorial(1) == 1

def test_positive():
    assert factorial(5) == 120

def test_large():
    assert factorial(7) == 5040
```

Вывод `pytest -v`:
```
test_factorial.py::test_zero PASSED
test_factorial.py::test_one PASSED
test_factorial.py::test_positive PASSED
test_factorial.py::test_large PASSED

4 passed in 0.01s
```

**Группировка в класс** — для организации, без наследования:

```python
class TestFactorial:
    def test_zero(self):
        assert factorial(0) == 1

    def test_positive(self):
        assert factorial(5) == 120

    def test_large(self):
        assert factorial(7) == 5040
```

**Сообщение при провале** — pytest развёртывает выражение:

```
E       assert 120 == 121
E        +  where 120 = factorial(5)
```

Можно добавить своё сообщение:
```python
assert factorial(5) == 120, f'Ожидали 120, получили {factorial(5)}'
```

---

## 🔵 Проверка исключений

```python
import pytest


def test_negative_raises():
    with pytest.raises(ValueError):
        factorial(-1)

def test_float_raises():
    with pytest.raises(TypeError):
        factorial(2.5)

# Проверка текста сообщения — через match (regex)
def test_error_message():
    with pytest.raises(ValueError, match=r'Некорректный аргумент'):
        factorial(-1)

# Доступ к объекту исключения через excinfo
def test_error_type():
    with pytest.raises(ValueError) as excinfo:
        factorial(-1)
    assert str(excinfo.value) == 'Некорректный аргумент'
    assert excinfo.type == ValueError
```

---

## 🔴 Фикстуры — @pytest.fixture

**Назначение:** подготовить данные или ресурс для теста. Тестовая функция получает фикстуру через параметр с тем же именем.

```python
import pytest


class Cat:
    def __init__(self, name):
        self.name = name

    def meow(self):
        return f'{self.name} говорит: Мяу!'


@pytest.fixture
def cat():
    return Cat('Кемаль')       # pytest сам найдёт и вызовет фикстуру

def test_cat_name(cat):         # параметр cat → pytest подставит фикстуру cat()
    assert cat.name == 'Кемаль'

def test_cat_meow(cat):
    assert 'Мяу' in cat.meow()
```

**yield — подготовка и очистка ресурсов:**

```python
import sqlite3
import pytest


@pytest.fixture
def db_conn():
    conn = sqlite3.connect(':memory:')
    conn.execute('CREATE TABLE users (id INTEGER, name TEXT)')
    yield conn          # ← setUp: всё до yield доступно тесту
    conn.close()        # ← tearDown: выполняется после теста (даже если упал)

def test_insert(db_conn):
    db_conn.execute("INSERT INTO users VALUES (1, 'Alice')")
    db_conn.commit()
    cursor = db_conn.execute("SELECT COUNT(*) FROM users")
    assert cursor.fetchone()[0] == 1
```

**Рекомендация:** всегда использовать `yield`, даже если очистка сейчас не нужна — заложить возможность на будущее.

**Фикстура использует другую фикстуру:**

```python
@pytest.fixture
def admin_cat(cat):    # cat — другая фикстура!
    cat.name = 'Админ'
    return cat

def test_admin(admin_cat):
    assert admin_cat.name == 'Админ'
```

---

## 🟡 Уровни фикстур — scope

По умолчанию фикстура создаётся заново для каждого теста. Уровень можно расширить:

```python
# scope='function' — по умолчанию, новый объект для каждого теста
@pytest.fixture(scope='function')
def cat():
    return Cat('Кемаль')

# scope='class' — один объект на весь класс
@pytest.fixture(scope='class')
def shared_cat():
    return Cat('Общий')

# scope='module' — один объект на весь модуль
@pytest.fixture(scope='module')
def db():
    conn = sqlite3.connect(':memory:')
    yield conn
    conn.close()

# scope='session' — один объект на всю тестовую сессию
@pytest.fixture(scope='session')
def expensive_resource():
    resource = create_expensive_resource()
    yield resource
    resource.cleanup()
```

| scope | Когда создаётся | Когда очищается |
|---|---|---|
| `function` | перед каждым тестом | после каждого теста |
| `class` | перед первым тестом класса | после последнего теста класса |
| `module` | перед первым тестом модуля | после последнего теста модуля |
| `session` | перед первым тестом сессии | после последнего теста сессии |

---

## 🟣 conftest.py — глобальные фикстуры

Фикстуры в `conftest.py` доступны всем тестам в папке и подпапках — без импорта:

```
my_project/
├── conftest.py          ← глобальные фикстуры (весь проект)
├── tests/
│   ├── conftest.py      ← фикстуры для этой папки
│   ├── test_users.py
│   └── test_orders.py
└── src/
    └── my_module.py
```

```python
# conftest.py
import pytest
import sqlite3


@pytest.fixture(scope='session')
def db():
    """Общая БД для всех тестов."""
    conn = sqlite3.connect(':memory:')
    conn.execute('CREATE TABLE users (id INTEGER, name TEXT)')
    yield conn
    conn.close()


@pytest.fixture
def user(db):
    """Тестовый пользователь."""
    db.execute("INSERT INTO users VALUES (1, 'Alice')")
    db.commit()
    yield {'id': 1, 'name': 'Alice'}
    db.execute("DELETE FROM users WHERE id = 1")
    db.commit()
```

```python
# test_users.py — фикстуры из conftest.py не нужно импортировать!
def test_user_name(user):
    assert user['name'] == 'Alice'

def test_user_id(user):
    assert user['id'] == 1
```

---

## ⚙️ Запуск тестов

```bash
# Все тесты в текущей папке и подпапках
pytest

# Подробный вывод
pytest -v

# Показать print() в тестах
pytest -s

# Конкретный файл
pytest test_factorial.py

# Конкретный класс
pytest test_factorial.py::TestFactorial

# Конкретный метод
pytest test_factorial.py::TestFactorial::test_positive

# Тесты содержащие слово в имени
pytest -k 'factorial'
pytest -k 'not slow'
pytest -k 'factorial and not large'

# Остановиться на первом провале
pytest -x

# Запустить N тестов затем остановиться
pytest --maxfail=3

# Краткий traceback
pytest --tb=short

# Запустить doctest
pytest --doctest-modules

# Запустить unittest тесты (pytest совместим!)
pytest test_unittest.py
```

---

## 🎯 pytest vs unittest

| Аспект | unittest | pytest |
|---|---|---|
| Стиль | ООП, классы | функциональный |
| Проверка | `self.assertEqual(a, b)` | `assert a == b` |
| Исключения | `assertRaises` | `pytest.raises` |
| Фикстуры | `setUp/tearDown` | `@pytest.fixture` |
| Параметризация | `subTest()` | `@pytest.mark.parametrize` |
| Отчёт об ошибке | краткий | подробный с развёрткой |
| Встроен в Python | ✅ | ❌ `pip install pytest` |
| Совместимость | — | запускает и unittest тесты |

---

## ⚡ Быстрые примеры

```python
import pytest


# Простые тесты
def test_add():
    assert 1 + 1 == 2

def test_string():
    assert 'hello'.upper() == 'HELLO'

def test_list():
    result = sorted([3, 1, 2])
    assert result == [1, 2, 3]


# Исключения
def test_zero_division():
    with pytest.raises(ZeroDivisionError):
        1 / 0

def test_value_error():
    with pytest.raises(ValueError, match='Некорректный'):
        factorial(-1)


# Фикстура
@pytest.fixture
def sample_list():
    return [1, 2, 3, 4, 5]

def test_sum(sample_list):
    assert sum(sample_list) == 15

def test_len(sample_list):
    assert len(sample_list) == 5


# Класс без наследования
class TestMath:
    def test_add(self):
        assert 2 + 2 == 4

    def test_sub(self):
        assert 5 - 3 == 2
```

---

## 💡 Практические замечания

- pytest функционально — классы только для группировки, наследование не нужно
- pytest совместим с unittest — можно запускать старые тесты без переписывания
- `conftest.py` — мощный инструмент: фикстуры, плагины, хуки для всего проекта
- `yield` в фикстуре — рекомендуемый паттерн даже без очистки
- `pytest --tb=short` — удобнее для длинных трейсбеков

---

## ⚠️ Частые ошибки

**❌ Наследуешь TestCase в pytest-стиле:**
```python
class TestFact(unittest.TestCase):   # ❌ это unittest стиль
    def test_one(self):
        self.assertEqual(factorial(1), 1)

class TestFact:                      # ✅ pytest стиль — без наследования
    def test_one(self):
        assert factorial(1) == 1
```

**❌ Забываешь параметр фикстуры:**
```python
@pytest.fixture
def cat():
    return Cat('Кемаль')

def test_name():        # ❌ нет параметра cat — фикстура не придёт!
    assert cat.name == 'Кемаль'

def test_name(cat):     # ✅ pytest подставит фикстуру
    assert cat.name == 'Кемаль'
```

**❌ Используешь return вместо yield для очистки:**
```python
@pytest.fixture
def db():
    conn = sqlite3.connect(':memory:')
    return conn         # ❌ нет места для очистки!

@pytest.fixture
def db():
    conn = sqlite3.connect(':memory:')
    yield conn          # ✅
    conn.close()        # выполнится после теста
```

---

## ✅ Главные правила

✅ Имя теста начинается с `test_`, файл с `test_` или `_test`  
✅ Класс — только для группировки, без наследования от `TestCase`  
✅ Проверки через обычный `assert`, pytest развернёт выражение  
✅ `pytest.raises` — для проверки исключений  
✅ `@pytest.fixture` — подготовка данных, фикстура приходит через параметр  
✅ `yield` в фикстуре — setUp до yield, tearDown после  
✅ `conftest.py` — глобальные фикстуры без импорта  
✅ `scope` — управляет временем жизни фикстуры  
✅ pytest запускает и unittest тесты — не нужно переписывать  

---

## 🔗 Связанные темы

- [[11 — 🏷 pytest — маркировка]]
- [[03 — 🧩 unittest — основы]]
- [[06 — 🏗 unittest — фикстуры и наборы]]

---

#python/тестирование #pytest #тесты
