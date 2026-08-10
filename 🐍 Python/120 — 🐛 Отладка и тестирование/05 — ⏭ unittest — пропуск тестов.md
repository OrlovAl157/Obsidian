---
tags: [python, тестирование, unittest]
difficulty: intermediate
---

# ⏭ unittest — пропуск тестов — памятка

> Иногда тест нужно временно исключить из выполнения не удаляя из кода — функционал ещё не реализован, тест зависит от версии Python или ОС, или баг известен и ждёт исправления. Для этого используются декораторы `@skip`, `@skipIf`, `@skipUnless`, `@expectedFailure` и метод `skipTest()`.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 skip — безусловный пропуск|skip]]
- [[#🔵 skipIf и skipUnless — условный пропуск|skipIf и skipUnless]]
- [[#🔴 expectedFailure — ожидаемый провал|expectedFailure]]
- [[#🟡 skipTest — пропуск внутри метода|skipTest]]
- [[#🟣 Пропуск класса целиком|Пропуск класса]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Инструмент | Когда пропускает | Аргументы |
|---|---|---|
| `@unittest.skip(reason)` | всегда | `reason` обязателен |
| `@unittest.skipIf(cond, reason)` | если `cond` истинно | оба обязательны |
| `@unittest.skipUnless(cond, reason)` | если `cond` ложно | оба обязательны |
| `@unittest.expectedFailure` | не пропускает — ожидает провал | нет аргументов |
| `self.skipTest(reason)` | вызов внутри метода | `reason` обязателен |
| `raise unittest.SkipTest(reason)` | то же вручную | — |

| Символ в отчёте | Что означает |
|---|---|
| `s` | тест пропущен (`skip`) |
| `x` | ожидаемый провал (`expectedFailure`) |
| `u` | неожиданный успех (`unexpectedSuccess`) |

---

## 📊 Общая схема

```
@skip            → тест не выполняется вообще           → s (skipped)
@skipIf(True)    → тест не выполняется                  → s (skipped)
@skipIf(False)   → тест выполняется как обычно          → . или F
@skipUnless(True) → тест выполняется как обычно         → . или F
@skipUnless(False) → тест не выполняется                → s (skipped)

@expectedFailure + тест упал   → всё ожидаемо           → x (xfail)
@expectedFailure + тест прошёл → неожиданный успех      → u (unexpected)

self.skipTest() внутри subTest → пропускает только этот подтест
```

---

## 🟢 skip — безусловный пропуск

**Назначение:** временно исключить тест из выполнения — функционал не готов, тест устарел, требует ручной проверки.

```python
import unittest


class TestFactorial(unittest.TestCase):

    def test_normal(self):
        self.assertEqual(factorial(5), 120)

    @unittest.skip('Функционал ещё не реализован.')
    def test_not_implemented(self):
        self.assertEqual(factorial(-1), None)

    @unittest.skip('Требует ручной проверки окружения.')
    def test_manual(self):
        ...
```

Вывод при `verbosity=1`:
```
.s.
----------------------------------------------------------------------
Ran 3 tests in 0.000s

OK (skipped=1)
```

Вывод при `verbosity=2`:
```
test_manual ... skipped 'Требует ручной проверки окружения.'
test_normal ... ok
test_not_implemented ... skipped 'Функционал ещё не реализован.'

OK (skipped=2)
```

**Важное:** пропущенный тест не считается ни успешным ни проваленным — учитывается отдельно в `OK (skipped=N)`.

---

## 🔵 skipIf и skipUnless — условный пропуск

**Назначение:** пропустить тест в зависимости от условия — версия Python, ОС, наличие зависимости.

```python
import sys
import unittest


class TestPythonVersion(unittest.TestCase):

    # Пропустить если версия Python меньше 3.12
    @unittest.skipIf(sys.version_info < (3, 12), 'Нужен Python 3.12+.')
    def test_batched(self):
        from itertools import batched
        self.assertEqual(list(batched([1, 2, 3, 4], 2)), [(1, 2), (3, 4)])

    # Выполнить только на Python 3.11+
    @unittest.skipUnless(sys.version_info >= (3, 11), 'Нужен Python 3.11+.')
    def test_tomllib(self):
        import tomllib


class TestOSSpecific(unittest.TestCase):

    # Только для Windows
    @unittest.skipUnless(sys.platform.startswith('win'), 'Только Windows.')
    def test_windows(self):
        ...

    # Только для Linux
    @unittest.skipUnless(sys.platform.startswith('linux'), 'Только Linux.')
    def test_linux(self):
        ...

    # Пропустить на macOS
    @unittest.skipIf(sys.platform == 'darwin', 'Не поддерживается на macOS.')
    def test_not_mac(self):
        ...


class TestDependencies(unittest.TestCase):

    # Пропустить если библиотека не установлена
    try:
        import numpy
        HAS_NUMPY = True
    except ImportError:
        HAS_NUMPY = False

    @unittest.skipUnless(HAS_NUMPY, 'Требуется numpy.')
    def test_numpy_array(self):
        import numpy as np
        arr = np.array([1, 2, 3])
        self.assertEqual(arr.sum(), 6)
```

**Разница по смыслу:**

| Декоратор | Логика | Читается как |
|---|---|---|
| `skipIf(cond, reason)` | пропустить если `cond` True | «пропусти если условие плохое» |
| `skipUnless(cond, reason)` | пропустить если `cond` False | «выполни только если условие хорошее» |

Они взаимозаменяемы через `not`:
```python
@skipIf(version < (3, 12), ...)     # эквивалентно
@skipUnless(version >= (3, 12), ...) # этому
```

**Важное:** аргумент `reason` обязателен в обоих декораторах — без него `TypeError`.

---

## 🔴 expectedFailure — ожидаемый провал

**Назначение:** пометить тест как заведомо проваленный — баг известен, ждёт исправления. Тест выполняется, но провал не считается ошибкой.

```python
class TestKnownBugs(unittest.TestCase):

    @unittest.expectedFailure
    def test_known_bug(self):
        # Знаем что это упадёт — баг в трекере #123
        self.assertEqual(some_function(), expected_value)

    @unittest.expectedFailure
    def test_another_bug(self):
        self.assertListEqual(get_sorted_list(), [1, 2, 3])
```

**Три сценария:**

```
Тест упал    + @expectedFailure → x (xfail)          ← ожидаемо, всё нормально
Тест прошёл  + @expectedFailure → u (unexpected)      ← баг исправлен! убери декоратор
Тест упал    без декоратора     → F (failed)          ← обычный провал
```

**Чем отличается от `@skip`:**

| | `@skip` | `@expectedFailure` |
|---|---|---|
| Тест выполняется | ❌ нет | ✅ да |
| При провале | s (skipped) | x (xfail) — нормально |
| При успехе | s (skipped) | u (unexpected) — сигнал! |
| Когда использовать | функционал не готов | баг известен, ждёт фикса |

---

## 🟡 skipTest — пропуск внутри метода

**Назначение:** `@skip` пропускает тест целиком. Для пропуска **одного подтеста** внутри `subTest()` используй `self.skipTest()`.

```python
class TestFactorial(unittest.TestCase):

    def test_cases(self):
        test_cases = [(0, 1), (1, 1), (5, 120), (50, None)]
        for n, expected in test_cases:
            with self.subTest(n=n):
                if n == 50:
                    self.skipTest('Большие числа требуют оптимизации')
                self.assertEqual(factorial(n), expected)

    # Условный пропуск внутри метода
    def test_with_condition(self):
        if sys.platform != 'win32':
            self.skipTest('Только для Windows')
        # дальнейший код выполнится только на Windows
        ...
```

**Через raise — то же самое:**
```python
def test_something(self):
    if not HAS_NUMPY:
        raise unittest.SkipTest('Требуется numpy')
    ...
```

---

## 🟣 Пропуск класса целиком

Декоратор на классе пропускает все методы класса:

```python
@unittest.skip('Весь модуль временно отключён.')
class TestFeatureX(unittest.TestCase):

    def test_one(self):   # будет пропущен
        ...

    def test_two(self):   # будет пропущен
        ...
```

**Условный пропуск класса:**

```python
@unittest.skipIf(sys.version_info < (3, 11), 'Нужен Python 3.11+.')
class TestNewFeatures(unittest.TestCase):
    ...
```

---

## ⚡ Быстрые примеры

```python
import sys
import unittest


class TestSkipExamples(unittest.TestCase):

    @unittest.skip('WIP — в разработке')
    def test_wip(self):
        ...

    @unittest.skipIf(sys.version_info < (3, 10), 'match-case требует Python 3.10+')
    def test_match_case(self):
        match 42:
            case 42: result = 'found'
        self.assertEqual(result, 'found')

    @unittest.skipUnless(sys.platform == 'win32', 'Только Windows')
    def test_windows_path(self):
        import os
        self.assertTrue(os.path.exists('C:\\'))

    @unittest.expectedFailure
    def test_broken_feature(self):
        self.assertEqual(1 + 1, 3)   # заведомо упадёт

    def test_conditional_skip(self):
        if not hasattr(__builtins__, 'ExceptionGroup'):
            self.skipTest('ExceptionGroup требует Python 3.11+')
        ...
```

---

## 💡 Практические замечания

- `@skip` — временная мера, не забудь убрать когда функционал готов
- `@expectedFailure` — сигнализирует что баг зафиксирован; `u` в отчёте значит баг исправлен
- `skipTest()` внутри `setUp()` — пропустит весь тест, не только подтест
- Проверку зависимостей (`try: import numpy`) лучше делать на уровне модуля, а не внутри теста
- В отчёте причина пропуска видна только при `verbosity=2`

---

## ⚠️ Частые ошибки

**❌ Забыл reason в skipIf/skipUnless:**
```python
@unittest.skipIf(sys.version_info < (3, 12))   # ❌ TypeError — reason обязателен
@unittest.skipIf(sys.version_info < (3, 12), 'Нужен Python 3.12+')  # ✅
```

**❌ Перепутал skipIf и skipUnless:**
```python
# Хочешь запустить только на Python 3.11+:
@unittest.skipIf(sys.version_info >= (3, 11), '...')    # ❌ наоборот — пропустит на 3.11+!
@unittest.skipUnless(sys.version_info >= (3, 11), '...') # ✅ выполнит только на 3.11+
```

**❌ Ожидаешь что @expectedFailure скроет ошибку навсегда:**
```python
@unittest.expectedFailure
def test_bug(self):
    self.assertEqual(broken_func(), 42)
# Когда баг исправят — тест даст u (unexpected success)
# Это сигнал убрать декоратор — не игнорируй его!
```

**❌ Пытаешься пропустить subTest через @skip:**
```python
# @skip пропускает ВЕСЬ метод, не один подтест
# Для пропуска подтеста используй self.skipTest() внутри with self.subTest():
```

---

## ✅ Главные правила

✅ `@skip(reason)` — безусловный пропуск, `reason` обязателен  
✅ `@skipIf(cond, reason)` — пропустить если `cond` истинно  
✅ `@skipUnless(cond, reason)` — пропустить если `cond` ложно  
✅ `@expectedFailure` — баг известен, провал ожидаем; `u` = баг исправлен  
✅ `self.skipTest()` — единственный способ пропустить конкретный `subTest`  
✅ Декоратор на классе пропускает все тесты класса  
✅ Причина пропуска видна в отчёте только при `verbosity=2`  
✅ Пропущенный тест — не успех и не провал, учитывается отдельно  

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[04 — 🧰 unittest — методы assert]]
- [[06 — 🏗 unittest — фикстуры и наборы]]
- [[11 — 🏷 pytest — маркировка]]

---

#python/тестирование #unittest #skip
