---
tags: [python, тестирование, unittest]
difficulty: intermediate
---

# 🧩 unittest — основы — памятка

> `unittest` — встроенный фреймворк модульного тестирования (по образцу Java JUnit). Тесты оформляются как методы классов-наследников `unittest.TestCase`. Каждый метод — независимый автономный тест.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 Минимальный пример|Минимальный пример]]
- [[#🔵 Запуск тестов|Запуск тестов]]
- [[#🔴 Проверка исключений|Проверка исключений]]
- [[#🟡 Организация тестов|Организация тестов]]
- [[#🟣 Пропуск тестов|Пропуск тестов]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Правило | Требование |
|---|---|
| Имя тестового метода | обязан начинаться с `test` |
| Имя тестового класса | наследник `TestCase`, желательно с префиксом `Test` |
| Проверка внутри метода | минимум один вызов `self.assert...()` |
| Запуск всех тестов | `unittest.main()` |
| Запуск конкретного теста | `python -m unittest TestClass.test_method` |
| Подробность отчёта | `unittest.main(verbosity=0/1/2)` |

| Символ в отчёте | Что означает |
|---|---|
| `.` | тест прошёл |
| `F` | тест провалился (`AssertionError`) |
| `E` | ошибка в самом тесте (не `AssertionError`) |
| `S` | тест пропущен (`skip`) |
| `x` | ожидаемый провал (`expectedFailure`) |
| `u` | неожиданный успех (`unexpectedSuccess`) |

---

## 📊 Общая схема

```
Тестовый файл test_mymodule.py
│
├── import unittest
├── from mymodule import factorial
│
├── class TestFactorial(unittest.TestCase):   ← наследник TestCase
│   │
│   ├── def test_zero(self):                  ← имя начинается с test
│   │       self.assertEqual(factorial(0), 1) ← хотя бы один assert
│   │
│   ├── def test_positive(self):
│   │       self.assertEqual(factorial(5), 120)
│   │
│   └── def test_negative(self):
│           with self.assertRaises(ValueError):
│               factorial(-1)
│
└── if __name__ == '__main__':
        unittest.main()

Порядок выполнения: лексикографический по имени метода
test_negative → test_positive → test_zero
```

---

## 🟢 Минимальный пример

```python
import unittest


def factorial(n):
    if not isinstance(n, int) or n < 0:
        raise ValueError('Некорректный аргумент')
    result = 1
    for i in range(1, n + 1):
        result *= i
    return result


class TestFactorial(unittest.TestCase):

    def test_zero(self):
        self.assertEqual(factorial(0), 1)

    def test_one(self):
        self.assertEqual(factorial(1), 1)

    def test_positive(self):
        self.assertEqual(factorial(5), 120)

    def test_large(self):
        self.assertEqual(factorial(7), 5040)


if __name__ == '__main__':
    unittest.main()
```

Вывод при успехе:
```
....
----------------------------------------------------------------------
Ran 4 tests in 0.000s

OK
```

Вывод при провале:
```
F
======================================================================
FAIL: test_positive (__main__.TestFactorial.test_positive)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "main.py", line 18, in test_positive
    self.assertEqual(factorial(5), 121)
AssertionError: 120 != 121

----------------------------------------------------------------------
Ran 4 tests in 0.001s

FAILED (failures=1)
```

**Важное:** тестовый метод без `assert...()` всегда засчитывается успешным — исключений нет, но и пользы нет.

---

## 🔵 Запуск тестов

```bash
# Запустить все тесты в файле
python -m unittest test_mymodule

# Запустить конкретный класс
python -m unittest test_mymodule.TestFactorial

# Запустить конкретный метод
python -m unittest test_mymodule.TestFactorial.test_positive

# Найти и запустить все тесты в папке (discovery)
python -m unittest discover

# Подробный вывод
python -m unittest -v test_mymodule

# Или через unittest.main() с verbosity
unittest.main(verbosity=2)
```

**Параметр verbosity:**

| Значение | Поведение |
|---|---|
| `0` | только итог (`Ran N tests`, `OK`/`FAILED`) |
| `1` | строка из точек/F (по умолчанию) |
| `2` | каждый тест отдельно + `ok`/`FAIL` + докстринг метода |

**Порядок выполнения — лексикографический:**

```python
# Выполнятся в таком порядке:
def test_a(self): ...   # 1
def test_b(self): ...   # 2
def test_z(self): ...   # 3

# Если нужен конкретный порядок — нумеруй:
def test_01_create(self): ...
def test_02_update(self): ...
def test_03_delete(self): ...
```

---

## 🔴 Проверка исключений

**assertRaises — проверить что исключение возникло:**

```python
class TestFactorial(unittest.TestCase):

    def test_negative_raises(self):
        with self.assertRaises(ValueError):
            factorial(-1)

    def test_float_raises(self):
        with self.assertRaises(TypeError):
            factorial(2.5)

    # Через callable — без контекстного менеджера
    def test_negative_raises_alt(self):
        self.assertRaises(ValueError, factorial, -1)
```

**assertRaisesRegex — проверить ещё и текст сообщения:**

```python
def test_negative_message(self):
    with self.assertRaises(ValueError) as ctx:
        factorial(-1)
    self.assertEqual(str(ctx.exception), 'Некорректный аргумент')

# Через assertRaisesRegex — проверка через регулярное выражение
def test_negative_message_regex(self):
    with self.assertRaisesRegex(ValueError, r'Некорректный'):
        factorial(-1)
```

**Проверка что исключение НЕ возникло:**

```python
def test_no_exception(self):
    try:
        factorial(5)
    except Exception as e:
        self.fail(f'Неожиданное исключение: {e}')
```

---

## 🟡 Организация тестов

**subTest — несколько случаев без прерывания:**

```python
def test_normal_cases(self):
    test_cases = [(0, 1), (1, 1), (3, 6), (5, 120), (7, 5040)]
    for n, expected in test_cases:
        with self.subTest(n=n):                # n=n появится в отчёте
            self.assertEqual(factorial(n), expected)
```

Без `subTest()` первый же провал прерывает весь цикл — об остальных случаях ничего не узнать. С `subTest()` каждая итерация независима.

**Несколько файлов с тестами — соглашение об именах:**

```
my_project/
├── mymodule.py
├── test_mymodule.py      ← префикс test_
└── mymodule_test.py      ← суффикс _test (тоже принято)
```

---

## 🟣 Пропуск тестов

```python
import sys
import unittest


class TestFactorial(unittest.TestCase):

    @unittest.skip('Функционал не реализован')
    def test_not_implemented(self):
        ...

    @unittest.skipIf(sys.version_info < (3, 11), 'Нужен Python 3.11+')
    def test_new_feature(self):
        ...

    @unittest.skipUnless(sys.platform == 'win32', 'Только для Windows')
    def test_windows_only(self):
        ...

    @unittest.expectedFailure
    def test_known_bug(self):
        self.assertEqual(factorial(3), 99)   # знаем что упадёт
```

| Декоратор | Когда использовать |
|---|---|
| `@skip(reason)` | всегда пропустить |
| `@skipIf(cond, reason)` | пропустить если условие True |
| `@skipUnless(cond, reason)` | пропустить если условие False |
| `@expectedFailure` | тест ожидаемо провалится (баг известен) |

---

## ⚡ Быстрые примеры

```python
import unittest


class TestString(unittest.TestCase):

    def test_upper(self):
        self.assertEqual('hello'.upper(), 'HELLO')

    def test_isupper(self):
        self.assertTrue('HELLO'.isupper())
        self.assertFalse('Hello'.isupper())

    def test_split(self):
        s = 'hello world'
        self.assertEqual(s.split(), ['hello', 'world'])
        # Проверяем что при неправильном sep вылетает исключение
        with self.assertRaises(TypeError):
            s.split(2)

    def test_multiple_cases(self):
        cases = [
            ('hello', 'HELLO'),
            ('world', 'WORLD'),
            ('python', 'PYTHON'),
        ]
        for input_str, expected in cases:
            with self.subTest(input=input_str):
                self.assertEqual(input_str.upper(), expected)
```

---

## 💡 Практические замечания

- Тестовые файлы называй с префиксом `test_` — тогда `python -m unittest discover` их найдёт автоматически
- Один класс = одна тестируемая единица (функция, класс, модуль)
- Порядок методов в файле не влияет на порядок выполнения — всегда лексикографический
- `self.fail(msg)` — принудительно провалить тест с сообщением
- Если тест проверяет реальный ввод-вывод или базу данных — это уже не юнит-тест, а интеграционный

---

## ⚠️ Частые ошибки

**❌ Метод без assert — всегда зелёный:**
```python
def test_something(self):
    result = factorial(5)
    # забыл assert ← тест пройдёт всегда!

def test_something(self):
    result = factorial(5)
    self.assertEqual(result, 120)  # ✅
```

**❌ Имя метода без префикса test:**
```python
def check_factorial(self):          # ❌ не будет запущен!
    self.assertEqual(factorial(5), 120)

def test_factorial(self):           # ✅
    self.assertEqual(factorial(5), 120)
```

**❌ Ожидаем тип исключения но не проверяем:**
```python
def test_negative(self):
    factorial(-1)                   # ❌ если исключение не вылетит — тест пройдёт!

def test_negative(self):
    with self.assertRaises(ValueError):
        factorial(-1)               # ✅
```

**❌ Цикл без subTest — обрывается на первой ошибке:**
```python
def test_cases(self):
    for n, expected in [(1, 1), (5, 120), (7, 5040)]:
        self.assertEqual(factorial(n), expected)  # ❌ при ошибке на n=5 не узнаем про n=7

def test_cases(self):
    for n, expected in [(1, 1), (5, 120), (7, 5040)]:
        with self.subTest(n=n):
            self.assertEqual(factorial(n), expected)  # ✅
```

---

## ✅ Главные правила

✅ Имя метода обязательно начинается с `test`  
✅ Класс наследует `unittest.TestCase`  
✅ Каждый метод — независимый тест, минимум один `assert`  
✅ Порядок выполнения — лексикографический, не по коду  
✅ `subTest()` — когда проверяешь много случаев в одном методе  
✅ `assertRaises` / `assertRaisesRegex` — для проверки исключений  
✅ Файлы с тестами называй `test_название.py`  
✅ `python -m unittest discover` — найдёт все тесты автоматически  

---

## 🔗 Связанные темы

- [[00 — 🧪 Введение в тестирование]]
- [[04 — 🧰 unittest — методы assert]]
- [[05 — ⏭ unittest — пропуск тестов]]
- [[06 — 🏗 unittest — фикстуры и наборы]]
- [[10 — 🐍 pytest — основы]]

---

#python/тестирование #unittest #тесты
