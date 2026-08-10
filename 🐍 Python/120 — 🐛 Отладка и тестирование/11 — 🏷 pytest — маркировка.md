---
tags: [python, тестирование, pytest]
difficulty: advanced
---

# 🏷 pytest — маркировка и параметризация — памятка

> Маркеры (`@pytest.mark...`) — декораторы для категоризации тестов, выборочного запуска и создания нескольких вариантов теста из одного набора данных. Ключевые встроенные маркеры: `parametrize`, `skip`, `skipif`, `xfail`.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 parametrize — параметризация|parametrize]]
- [[#🔵 skip и skipif — пропуск|skip и skipif]]
- [[#🔴 xfail — ожидаемый провал|xfail]]
- [[#🟡 Пользовательские маркеры|Пользовательские маркеры]]
- [[#🟣 Комбинирование маркеров|Комбинирование маркеров]]
- [[#⚙️ pytest.ini — регистрация маркеров|pytest.ini]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Маркер | Назначение |
|---|---|
| `@pytest.mark.parametrize('args', [...])` | несколько вариантов теста с разными данными |
| `@pytest.mark.skip(reason)` | безусловный пропуск |
| `@pytest.mark.skipif(condition, reason=...)` | условный пропуск |
| `@pytest.mark.xfail(reason=...)` | тест ожидаемо провалится |
| `@pytest.mark.<своё_имя>` | пользовательский маркер |

| Команда | Что делает |
|---|---|
| `pytest -m marker` | запустить только тесты с этим маркером |
| `pytest -m 'not marker'` | все тесты кроме отмеченных |
| `pytest -m 'a and b'` | тесты с обоими маркерами |
| `pytest -m 'a or b'` | тесты с любым из маркеров |

---

## 📊 Общая схема

```
parametrize — один тест, много данных:
    @pytest.mark.parametrize('n, expected', [(1,1), (5,120)])
    def test_factorial(n, expected):         ↑ один тест → два запуска
        assert factorial(n) == expected

skip/skipif — пропустить тест:
    @pytest.mark.skip → s (skipped)
    @pytest.mark.skipif(cond) → s если cond True

xfail — тест ожидаемо упадёт:
    @pytest.mark.xfail + тест упал → x (xfail) — нормально
    @pytest.mark.xfail + тест прошёл → X (xpass) — неожиданно!

Пользовательские маркеры — для фильтрации:
    @pytest.mark.slow
    pytest -m slow    → только медленные тесты
    pytest -m 'not slow' → все кроме медленных
```

---

## 🟢 parametrize — параметризация

**Назначение:** запустить один тест с разными наборами данных — каждый набор как отдельный независимый тест.

```python
import pytest


def factorial(n):
    if not isinstance(n, int) or n < 0:
        raise ValueError('Некорректный аргумент')
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result


# Базовый parametrize
@pytest.mark.parametrize('n, expected', [
    (0, 1),
    (1, 1),
    (2, 2),
    (3, 6),
    (5, 120),
    (7, 5040),
])
def test_factorial(n, expected):
    assert factorial(n) == expected
```

Вывод `pytest -v`:
```
test_factorial.py::test_factorial[0-1] PASSED
test_factorial.py::test_factorial[1-1] PASSED
test_factorial.py::test_factorial[2-2] PASSED
test_factorial.py::test_factorial[3-6] PASSED
test_factorial.py::test_factorial[5-120] PASSED
test_factorial.py::test_factorial[7-5040] PASSED
```

**Один параметр — элементы не кортежи:**

```python
@pytest.mark.parametrize('n', [0, 1, 2, 3, 5])  # ← не [(0,), (1,), ...]
def test_non_negative(n):
    assert factorial(n) >= 1
```

**ids — читаемые имена тестов:**

```python
@pytest.mark.parametrize('n, expected', [
    (0, 1),
    (5, 120),
    (7, 5040),
], ids=['factorial_zero', 'factorial_five', 'factorial_seven'])
def test_factorial(n, expected):
    assert factorial(n) == expected

# Вывод: test_factorial[factorial_zero] PASSED
```

**Несколько наборов parametrize — декомпозиция:**

```python
# Два parametrize на одном тесте = декартово произведение
@pytest.mark.parametrize('x', [1, 2, 3])
@pytest.mark.parametrize('y', [10, 20])
def test_multiply(x, y):
    assert x * y > 0
# Создаст 3 × 2 = 6 тестов
```

**parametrize на классе — применяется ко всем методам:**

```python
@pytest.mark.parametrize('n, expected', [(0, 1), (5, 120)])
class TestFactorial:
    def test_value(self, n, expected):
        assert factorial(n) == expected

    def test_type(self, n, expected):
        assert isinstance(factorial(n), int)
```

**Маркировка отдельных параметров:**

```python
import pytest


@pytest.mark.parametrize('n, expected', [
    (0, 1),
    (5, 120),
    pytest.param(50, 30414093201713378043612608166064768844377641568960512000000000000,
                 marks=pytest.mark.slow),   # этот вариант помечен как slow
])
def test_factorial(n, expected):
    assert factorial(n) == expected
```

---

## 🔵 skip и skipif — пропуск

```python
import sys
import pytest


# Безусловный пропуск
@pytest.mark.skip(reason='Функционал не реализован')
def test_not_implemented():
    ...

# Условный пропуск — если условие True
@pytest.mark.skipif(sys.version_info < (3, 12), reason='Нужен Python 3.12+')
def test_batched():
    from itertools import batched
    assert list(batched([1, 2, 3, 4], 2)) == [(1, 2), (3, 4)]

# Пропуск по ОС
@pytest.mark.skipif(sys.platform != 'win32', reason='Только Windows')
def test_windows():
    ...

# Пропуск по наличию библиотеки
numpy = pytest.importorskip('numpy')   # ← если numpy нет — тест пропустится!

def test_numpy():
    arr = numpy.array([1, 2, 3])
    assert arr.sum() == 6
```

**pytest.importorskip** — удобная альтернатива ручной проверке:

```python
# Вместо:
try:
    import numpy
except ImportError:
    pytest.skip('numpy не установлен')

# Можно:
numpy = pytest.importorskip('numpy')
```

**Пропуск внутри теста:**

```python
def test_conditional():
    if not some_condition:
        pytest.skip('Условие не выполнено')
    # остальной код
```

---

## 🔴 xfail — ожидаемый провал

**Назначение:** пометить тест как заведомо проваленный — баг известен, ждёт исправления. Тест выполняется, но провал не считается ошибкой.

```python
import pytest


@pytest.mark.xfail(reason='Баг #123 — ждёт исправления')
def test_known_bug():
    assert some_function() == expected_value   # знаем что упадёт

# Три сценария:
# тест упал    → x (xfail)    — ожидаемо, нормально
# тест прошёл  → X (xpass)    — неожиданно! баг исправлен, убери маркер
```

**strict=True — xpass становится провалом:**

```python
@pytest.mark.xfail(strict=True, reason='Должен упасть')
def test_strict_xfail():
    assert False   # если вдруг пройдёт → FAILED, а не xpass
```

**Условный xfail:**

```python
@pytest.mark.xfail(sys.version_info < (3, 11), reason='Баг в Python < 3.11')
def test_conditional_xfail():
    ...
```

**Чем отличается от skip:**

| | `@skip` | `@xfail` |
|---|---|---|
| Тест выполняется | ❌ | ✅ |
| При провале | s (skipped) | x (xfail) — нормально |
| При успехе | s (skipped) | X (xpass) — сигнал! |
| Когда использовать | функционал не готов | баг известен, ждёт фикса |

---

## 🟡 Пользовательские маркеры

Создаёшь маркер просто используя его — но без регистрации будет предупреждение:

```python
import pytest


@pytest.mark.slow
def test_heavy_computation():
    ...

@pytest.mark.integration
def test_database_connection():
    ...

@pytest.mark.smoke
@pytest.mark.fast
def test_basic():
    ...
```

Запуск по маркеру:
```bash
pytest -m slow                    # только медленные
pytest -m 'not slow'              # все кроме медленных
pytest -m 'smoke and fast'        # оба маркера
pytest -m 'slow or integration'   # любой из маркеров
```

---

## 🟣 Комбинирование маркеров

```python
import pytest
import sys


# Несколько маркеров на одном тесте
@pytest.mark.slow
@pytest.mark.integration
@pytest.mark.skipif(sys.platform == 'darwin', reason='Не поддерживается на macOS')
def test_heavy_integration():
    ...


# parametrize + маркер на отдельном параметре
@pytest.mark.parametrize('n', [
    1,
    5,
    pytest.param(1000, marks=pytest.mark.slow),  # только этот вариант slow
])
def test_factorial(n):
    result = factorial(n)
    assert result > 0


# parametrize + xfail на параметре
@pytest.mark.parametrize('n, expected', [
    (1, 1),
    (5, 120),
    pytest.param(3, 99, marks=pytest.mark.xfail),  # знаем что упадёт
])
def test_factorial_values(n, expected):
    assert factorial(n) == expected
```

---

## ⚙️ pytest.ini — регистрация маркеров

Без регистрации собственные маркеры работают но дают предупреждение `PytestUnknownMarkWarning`. Регистрируй в `pytest.ini`:

```ini
[pytest]
markers =
    slow: тесты требующие много времени
    integration: интеграционные тесты (требуют внешних ресурсов)
    smoke: базовые тесты работоспособности
    unit: юнит-тесты
```

Или в `pyproject.toml`:

```toml
[tool.pytest.ini_options]
markers = [
    "slow: тесты требующие много времени",
    "integration: интеграционные тесты",
    "smoke: базовые тесты",
]
```

Просмотр всех маркеров:
```bash
pytest --markers
```

---

## ⚡ Быстрые примеры

```python
import sys
import pytest


# parametrize — базовый
@pytest.mark.parametrize('a, b, result', [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
])
def test_add(a, b, result):
    assert a + b == result

# parametrize — один параметр
@pytest.mark.parametrize('value', [1, 2, 3, 4, 5])
def test_positive(value):
    assert value > 0

# parametrize — с ids
@pytest.mark.parametrize('n', [0, 5, 10], ids=['zero', 'five', 'ten'])
def test_ids(n):
    assert n >= 0

# skip
@pytest.mark.skip(reason='TODO')
def test_todo(): ...

# skipif
@pytest.mark.skipif(sys.version_info < (3, 10), reason='Нужен Python 3.10+')
def test_new(): ...

# xfail
@pytest.mark.xfail(reason='Известный баг')
def test_bug():
    assert False

# xfail strict
@pytest.mark.xfail(strict=True)
def test_must_fail():
    assert False

# Маркер на классе
@pytest.mark.integration
class TestAPI:
    def test_get(self): ...
    def test_post(self): ...
```

---

## 💡 Практические замечания

- `parametrize` — главный инструмент против дублирования тестов в pytest
- Без `pytest.ini` собственные маркеры работают с предупреждением — зарегистрируй их
- `xfail` — документирует известные баги прямо в коде
- `X (xpass)` — сигнал что баг исправлен, убери `@xfail`
- `pytest.importorskip` — чище чем ручная проверка импорта
- `pytest -m 'not slow'` — удобно для быстрого запуска в CI без тяжёлых тестов

---

## ⚠️ Частые ошибки

**❌ Один параметр — список кортежей:**
```python
@pytest.mark.parametrize('n', [(1,), (2,), (3,)])  # ❌ n будет кортежем!
def test(n):
    assert n > 0    # n = (1,) — не то что ожидали

@pytest.mark.parametrize('n', [1, 2, 3])  # ✅
def test(n):
    assert n > 0
```

**❌ Забыл reason в skipif:**
```python
@pytest.mark.skipif(sys.version_info < (3, 12))   # ❌ TypeError!
@pytest.mark.skipif(sys.version_info < (3, 12), reason='Нужен 3.12+')  # ✅
```

**❌ Думаешь что xpass — это нормально:**
```python
@pytest.mark.xfail(reason='Баг #123')
def test_bug():
    assert True    # ← если баг исправлен — получишь X (xpass)
# X — сигнал! Убери @xfail, тест теперь работает правильно
```

**❌ Используешь маркеры без регистрации и игнорируешь предупреждение:**
```python
# PytestUnknownMarkWarning: Unknown pytest.mark.slow
# Зарегистрируй в pytest.ini — предупреждение исчезнет
```

**❌ Путаешь порядок строк в parametrize:**
```python
@pytest.mark.parametrize('expected, n', [(1, 0), (120, 5)])  # ❌ перепутан порядок!
def test(n, expected):   # n=1, expected=0 — не то!
    assert factorial(n) == expected

@pytest.mark.parametrize('n, expected', [(0, 1), (5, 120)])  # ✅
def test(n, expected):
    assert factorial(n) == expected
```

---

## ✅ Главные правила

✅ `parametrize` — один тест с разными данными, каждый набор независим  
✅ Один параметр — элементы списка, не кортежи: `[1, 2, 3]` не `[(1,), (2,)]`  
✅ `ids` — читаемые имена для параметризованных тестов  
✅ `skip(reason)` — безусловный пропуск, reason обязателен  
✅ `skipif(cond, reason=...)` — условный пропуск, reason через ключевой аргумент  
✅ `xfail` — тест выполняется, провал ожидаем; `X` = баг исправлен  
✅ `xfail(strict=True)` — `xpass` становится провалом  
✅ Регистрируй пользовательские маркеры в `pytest.ini`  
✅ `pytest -m 'not slow'` — удобно для быстрого CI  

---

## 🔗 Связанные темы

- [[10 — 🐍 pytest — основы]]
- [[04 — 🧰 unittest — методы assert]]
- [[05 — ⏭ unittest — пропуск тестов]]

---

#python/тестирование #pytest #маркировка #parametrize
