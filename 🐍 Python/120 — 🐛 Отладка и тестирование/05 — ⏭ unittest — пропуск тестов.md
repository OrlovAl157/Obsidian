---
tags:
  - python
  - тема/тестирование
  - статус/завершён
уровень: средний
---

# ⏭ unittest — пропуск тестов

## 📌 Коротко

> Иногда тест нужно временно исключить из выполнения, не удаляя его из кода — например, если функционал ещё не реализован. Для этого используются декораторы `@skip`, `@skipIf`, `@skipUnless` и исключение `SkipTest`.

---

## 🚀 Быстрая справка

| Инструмент | Условие пропуска | Аргументы |
|---|---|---|
| `@unittest.skip(reason)` | всегда | `reason` — обязателен |
| `@unittest.skipIf(condition, reason)` | если `condition` истинно | оба обязательны |
| `@unittest.skipUnless(condition, reason)` | если `condition` ложно | оба обязательны |
| `self.skipTest(reason)` | вызов внутри метода/подтеста | `reason` |
| `raise unittest.SkipTest(reason)` | то же самое вручную | — |

---

## 📖 Основное

### @unittest.skip — безусловный пропуск

```python
class TestFactorial(unittest.TestCase):
    def test_normal_cases(self):
        ...

    @unittest.skip('Функционал ещё не реализован.')
    def test_incorrect_argument(self):
        """Тестирование некорректного аргумента."""
```

При `verbosity=2` в отчёте видна причина: `test_incorrect_argument ... skipped 'Функционал ещё не реализован.'`. В итоговой строке — `OK (skipped=1)`.

Декоратор можно поставить и на весь класс `@unittest.skip(...)` — тогда пропустятся все его тесты разом.

### @skipIf и @skipUnless — условный пропуск

`skipIf(condition, reason)` — пропустить, если `condition` истинно. `skipUnless(condition, reason)` — пропустить, если `condition` ложно (то есть выполнить только при истинности).

```python
import sys

class TestItertools(unittest.TestCase):
    @unittest.skipIf(sys.version_info < (3, 12), 'Функция доступна с Python 3.12.')
    def test_batched(self):
        ...

class TestOSWindows(unittest.TestCase):
    @unittest.skipUnless(sys.platform.startswith('win'), 'Требуется ОС Windows.')
    def test_os_windows(self):
        ...
```

**Разница по смыслу:** `skipIf` — «пропусти, если условие плохое» (версия слишком старая); `skipUnless` — «выполни только если условие хорошее» (только на нужной ОС). По сути одно выражается через другое с `not`.

**Важно:** аргумент `reason` в обоих декораторах **обязателен**, иначе — `TypeError`.

### Пропуск подтестов

`@skip` пропускает тест **только целиком** — нельзя пропустить один подтест внутри `subTest()`. Для этого используют `self.skipTest()`:

```python
def test_normal_cases(self):
    test_cases = [(1, 1), (7, 5040),
                  (50, 30414093201713378043612608166064768844377641568960512000000000000)]
    for n, expected in test_cases:
        with self.subTest():
            if n == 50:
                self.skipTest('Нужна оптимизация функции')   # пропустит только этот подтест
            self.assertEqual(factorial(n), expected)
```

---

## 💡 Важные нюансы

- Пропущенный тест не считается ни успешным, ни проваленным — он отдельно учитывается в `OK (skipped=N)`.
- `@skip` — это уровень целого теста/класса; `skipTest()`/`SkipTest` — единственный способ пропустить отдельный подтест внутри `subTest()`.
- Без `verbosity=2` причина пропуска в отчёте не видна — виден только символ `s`.

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[04 — 🧰 unittest — методы assert]]
- [[11 — 🏷 pytest — маркировка]]

## ❓ Вопросы / Непонятное

- ...
