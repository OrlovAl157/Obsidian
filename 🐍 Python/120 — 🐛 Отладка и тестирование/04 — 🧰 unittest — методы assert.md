---
tags: [python, тестирование, unittest]
difficulty: intermediate
---

# 🧰 unittest — методы assert — памятка

> `unittest.TestCase` предоставляет десятки специализированных методов проверки. Они дают более информативные сообщения об ошибках чем голый `assert` и проверяют не только значение но и тип.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Сравнение значений|Сравнение значений]]
- [[#🔵 Проверка истинности|Проверка истинности]]
- [[#🔴 Проверка коллекций|Проверка коллекций]]
- [[#🟡 Проверка исключений|Проверка исключений]]
- [[#🟣 Числа и строки|Числа и строки]]
- [[#⚙️ Проверка типов и None|Проверка типов и None]]
- [[#🔧 subTest — группировка случаев|subTest]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

**Сравнение значений:**

| Метод | Проверяет | Эквивалент |
|---|---|---|
| `assertEqual(a, b)` | `a == b` | `assert a == b` |
| `assertNotEqual(a, b)` | `a != b` | `assert a != b` |
| `assertGreater(a, b)` | `a > b` | `assert a > b` |
| `assertGreaterEqual(a, b)` | `a >= b` | `assert a >= b` |
| `assertLess(a, b)` | `a < b` | `assert a < b` |
| `assertLessEqual(a, b)` | `a <= b` | `assert a <= b` |

**Истинность:**

| Метод | Проверяет |
|---|---|
| `assertTrue(x)` | `bool(x) is True` |
| `assertFalse(x)` | `bool(x) is False` |

**Коллекции:**

| Метод | Для чего | Проверяет тип |
|---|---|---|
| `assertListEqual(a, b)` | списки | ✅ |
| `assertTupleEqual(a, b)` | кортежи | ✅ |
| `assertDictEqual(a, b)` | словари | ✅ |
| `assertSetEqual(a, b)` | множества | ✅ |
| `assertSequenceEqual(a, b)` | любые последовательности | ✅ |
| `assertIn(x, container)` | `x in container` | ❌ |
| `assertNotIn(x, container)` | `x not in container` | ❌ |

**Исключения:**

| Метод | Проверяет |
|---|---|
| `assertRaises(exc)` | исключение возникло |
| `assertRaisesRegex(exc, regex)` | исключение + текст по regex |

**Числа и строки:**

| Метод | Проверяет |
|---|---|
| `assertAlmostEqual(a, b, places=7)` | `a ≈ b` с точностью до N знаков |
| `assertNotAlmostEqual(a, b)` | `a ≠ b` с точностью |
| `assertMultiLineEqual(a, b)` | многострочные строки |
| `assertRegex(text, regex)` | строка совпадает с паттерном |
| `assertNotRegex(text, regex)` | строка не совпадает |

**Типы и None:**

| Метод | Проверяет |
|---|---|
| `assertIsNone(x)` | `x is None` |
| `assertIsNotNone(x)` | `x is not None` |
| `assertIsInstance(x, cls)` | `isinstance(x, cls)` |
| `assertNotIsInstance(x, cls)` | `not isinstance(x, cls)` |
| `assertIs(a, b)` | `a is b` (идентичность) |
| `assertIsNot(a, b)` | `a is not b` |

---

## 📊 Общая схема

```
Метод assert          Когда использовать
─────────────────     ────────────────────────────────────────
assertEqual           значения должны совпадать (числа, строки)
assertListEqual       списки — показывает diff при ошибке
assertIn              элемент должен быть в коллекции
assertIsNone          результат должен быть None
assertIsInstance      тип объекта должен совпадать
assertAlmostEqual     float-сравнение (0.1 + 0.2 ≠ 0.3)
assertRaises          функция должна бросить исключение
assertTrue            булевое выражение должно быть True

Почему не просто assert?
  assert a == b           → AssertionError
  assertEqual(a, b)       → AssertionError: 5 != 6  ← видно что именно
  assertListEqual(a, b)   → показывает какие элементы отличаются
```

---

## 🟢 Сравнение значений

```python
class TestMath(unittest.TestCase):

    def test_equal(self):
        self.assertEqual(2 + 2, 4)
        self.assertEqual('hello'.upper(), 'HELLO')

    def test_not_equal(self):
        self.assertNotEqual(2 + 2, 5)

    def test_greater(self):
        self.assertGreater(10, 5)      # 10 > 5 ✅
        self.assertGreaterEqual(5, 5)  # 5 >= 5 ✅

    def test_less(self):
        self.assertLess(3, 10)         # 3 < 10 ✅
        self.assertLessEqual(5, 5)     # 5 <= 5 ✅
```

Сообщение при провале:
```
AssertionError: 4 != 5
AssertionError: 10 not greater than 10
```

**Дополнительное сообщение** — второй аргумент:
```python
self.assertEqual(result, 42, msg=f'Ожидали 42, получили {result}')
```

---

## 🔵 Проверка истинности

```python
def is_positive(n):
    return n > 0

class TestIsPositive(unittest.TestCase):

    def test_positive(self):
        self.assertTrue(is_positive(5))    # вместо assertEqual(..., True)

    def test_negative(self):
        self.assertFalse(is_positive(-1))  # вместо assertEqual(..., False)

    def test_truthy(self):
        self.assertTrue([1, 2, 3])         # непустой список — truthy
        self.assertFalse([])               # пустой список — falsy
        self.assertTrue('hello')
        self.assertFalse('')
```

**Важное:** `assertTrue(x)` проверяет `bool(x)`, не `x is True`. Если нужна строгая проверка на `True` — используй `assertIs(x, True)`.

```python
self.assertTrue(1)          # ✅ 1 это truthy
self.assertIs(1, True)      # ❌ 1 is not True (разные объекты!)
self.assertEqual(1, True)   # ✅ 1 == True (числовое равенство)
```

---

## 🔴 Проверка коллекций

**Почему assertListEqual лучше assertEqual для списков:**

```python
a = [1, 2, 3]
b = [1, 2, 4]

# assertEqual
AssertionError: [1, 2, 3] != [1, 2, 4]

# assertListEqual — показывает разницу
AssertionError: Lists differ: [1, 2, 3] != [1, 2, 4]
First differing element 2:
3
4
```

Плюс `assertListEqual` проверяет **тип** — если функция вернула кортеж вместо списка, тест провалится даже если содержимое совпадает.

```python
class TestCollections(unittest.TestCase):

    def test_list(self):
        result = sorted([3, 1, 2])
        self.assertListEqual(result, [1, 2, 3])

    def test_dict(self):
        result = {'a': 1, 'b': 2}
        self.assertDictEqual(result, {'a': 1, 'b': 2})

    def test_set(self):
        result = {1, 2, 3}
        self.assertSetEqual(result, {3, 1, 2})   # порядок не важен

    def test_tuple(self):
        result = (1, 2, 3)
        self.assertTupleEqual(result, (1, 2, 3))

    def test_in(self):
        self.assertIn(3, [1, 2, 3, 4])
        self.assertIn('key', {'key': 'value'})
        self.assertNotIn(5, [1, 2, 3])
```

---

## 🟡 Проверка исключений

```python
def factorial(n):
    if not isinstance(n, int) or n < 0:
        raise ValueError('Некорректный аргумент')
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result


class TestFactorialExceptions(unittest.TestCase):

    # assertRaises — через контекстный менеджер
    def test_negative(self):
        with self.assertRaises(ValueError):
            factorial(-1)

    # assertRaises — через callable (старый стиль)
    def test_negative_alt(self):
        self.assertRaises(ValueError, factorial, -1)

    # assertRaisesRegex — проверка текста исключения
    def test_negative_message(self):
        with self.assertRaisesRegex(ValueError, r'Некорректный'):
            factorial(-1)

    # Проверка точного текста через ctx.exception
    def test_exact_message(self):
        with self.assertRaises(ValueError) as ctx:
            factorial(-1)
        self.assertEqual(str(ctx.exception), 'Некорректный аргумент')

    # Проверка что исключение НЕ возникло
    def test_no_exception(self):
        try:
            factorial(5)
        except Exception as e:
            self.fail(f'Неожиданное исключение: {e}')
```

---

## 🟣 Числа и строки

**assertAlmostEqual — для float:**

```python
class TestFloat(unittest.TestCase):

    def test_float_sum(self):
        # Нельзя сравнивать float через assertEqual!
        # 0.1 + 0.2 == 0.30000000000000004 — False!
        self.assertAlmostEqual(0.1 + 0.2, 0.3)          # по умолчанию 7 знаков
        self.assertAlmostEqual(0.1 + 0.2, 0.3, places=5) # 5 знаков после точки
        self.assertAlmostEqual(1.0001, 1.0002, delta=0.001) # абсолютная дельта

    def test_not_almost_equal(self):
        self.assertNotAlmostEqual(0.1, 0.9)
```

**assertRegex — для строк:**

```python
class TestStrings(unittest.TestCase):

    def test_regex(self):
        self.assertRegex('hello world', r'\w+')
        self.assertNotRegex('hello', r'^\d+$')   # не состоит только из цифр

    def test_multiline(self):
        a = "line1\nline2\nline3"
        b = "line1\nline2\nline3"
        self.assertMultiLineEqual(a, b)   # при ошибке покажет построчную разницу
```

---

## ⚙️ Проверка типов и None

```python
class TestTypes(unittest.TestCase):

    def test_none(self):
        result = None
        self.assertIsNone(result)

    def test_not_none(self):
        result = 42
        self.assertIsNotNone(result)

    def test_isinstance(self):
        result = [1, 2, 3]
        self.assertIsInstance(result, list)
        self.assertIsInstance(result, (list, tuple))   # любой из типов

    def test_not_isinstance(self):
        result = 42
        self.assertNotIsInstance(result, str)

    def test_identity(self):
        a = [1, 2, 3]
        b = a
        self.assertIs(a, b)        # один и тот же объект

        c = [1, 2, 3]
        self.assertIsNot(a, c)     # разные объекты (хотя a == c)
```

---

## 🔧 subTest — группировка случаев

Без `subTest()` первый провал прерывает весь цикл. С `subTest()` каждый случай независим:

```python
class TestFactorial(unittest.TestCase):

    def test_normal_cases(self):
        test_cases = [(0, 1), (1, 1), (2, 2), (3, 6), (5, 120), (7, 5040)]
        for n, expected in test_cases:
            with self.subTest(n=n):                     # n=n отображается в отчёте
                self.assertEqual(factorial(n), expected)

    def test_with_message(self):
        cases = [(0, 1), (5, 120)]
        for n, expected in cases:
            with self.subTest(msg=f'factorial({n})', n=n):
                self.assertEqual(factorial(n), expected)
```

Отчёт при провале нескольких случаев:
```
FAIL: test_normal_cases (__main__.TestFactorial) (n=1)
FAIL: test_normal_cases (__main__.TestFactorial) (n=7)
```

**Несколько assert в одном subTest:**

```python
def test_multiple(self):
    result = abs(-69)
    with self.subTest('проверка знака'):
        self.assertGreater(result, 0)
    with self.subTest('проверка значения'):
        self.assertEqual(result, 69)
```

---

## ⚡ Быстрые примеры

```python
class TestQuick(unittest.TestCase):

    def test_values(self):
        self.assertEqual(1 + 1, 2)
        self.assertNotEqual(1 + 1, 3)
        self.assertGreater(10, 5)
        self.assertLess(3, 10)

    def test_bool(self):
        self.assertTrue('hello')
        self.assertFalse('')
        self.assertIsNone(None)
        self.assertIsNotNone(42)

    def test_collections(self):
        self.assertIn(1, [1, 2, 3])
        self.assertNotIn(5, [1, 2, 3])
        self.assertListEqual([1, 2], [1, 2])
        self.assertDictEqual({'a': 1}, {'a': 1})

    def test_types(self):
        self.assertIsInstance([], list)
        self.assertIsInstance(42, (int, float))

    def test_float(self):
        self.assertAlmostEqual(0.1 + 0.2, 0.3, places=5)

    def test_exception(self):
        with self.assertRaises(ZeroDivisionError):
            1 / 0
```

---

## ⚠️ Частые ошибки

**❌ Сравниваешь float через assertEqual:**
```python
self.assertEqual(0.1 + 0.2, 0.3)        # ❌ провалится! 0.30000000000000004 != 0.3
self.assertAlmostEqual(0.1 + 0.2, 0.3)  # ✅
```

**❌ assertTrue вместо assertIsInstance:**
```python
self.assertTrue(isinstance(result, list))  # ❌ работает, но менее информативно
self.assertIsInstance(result, list)        # ✅ при ошибке покажет тип result
```

**❌ assertEqual вместо assertIs для None:**
```python
self.assertEqual(result, None)    # ❌ работает, но == None — плохой стиль
self.assertIsNone(result)         # ✅
```

**❌ Цикл без subTest:**
```python
for n, expected in test_cases:
    self.assertEqual(factorial(n), expected)  # ❌ прервётся на первой ошибке

for n, expected in test_cases:
    with self.subTest(n=n):
        self.assertEqual(factorial(n), expected)  # ✅
```

**❌ assertRaises без блока — не ловит:**
```python
self.assertRaises(ValueError)    # ❌ нет кода который должен бросить исключение!
with self.assertRaises(ValueError):
    factorial(-1)                # ✅
```

---

## ✅ Главные правила

✅ `assertEqual` — для чисел, строк и общего сравнения  
✅ `assertListEqual` / `assertDictEqual` / `assertSetEqual` — для коллекций, показывают diff  
✅ `assertAlmostEqual` — для float, никогда не `assertEqual`  
✅ `assertIsNone` / `assertIsNotNone` — вместо `assertEqual(..., None)`  
✅ `assertIsInstance` — проверка типа, информативнее чем `assertTrue(isinstance(...))`  
✅ `assertRaises` — всегда через контекстный менеджер `with`  
✅ `assertRaisesRegex` — когда важен текст исключения  
✅ `subTest()` — когда проверяешь много случаев в одном методе  
✅ Все методы принимают `msg=` — добавляй своё сообщение для сложных тестов  

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[05 — ⏭ unittest — пропуск тестов]]
- [[06 — 🏗 unittest — фикстуры и наборы]]
- [[10 — 🐍 pytest — основы]]

---

#python/тестирование #unittest #assert
