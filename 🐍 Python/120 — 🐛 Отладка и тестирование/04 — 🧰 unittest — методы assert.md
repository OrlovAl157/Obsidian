---
tags:
  - python
  - тема/тестирование
  - статус/завершён
уровень: средний
---

# 🧰 unittest — методы assert

## 📌 Коротко

> Помимо `assertEqual()`, `unittest.TestCase` предоставляет десятки специализированных методов проверки — для значений, коллекций, типов, исключений и т.д. Специализированные методы дают более информативные сообщения об ошибках, чем голый `assert`.

---

## 🚀 Быстрая справка

**Сравнение значений**

| Метод | Проверяет |
|---|---|
| `assertEqual(a, b)` | `a == b` |
| `assertNotEqual(a, b)` | `a != b` |
| `assertTrue(x)` | `bool(x) is True` |
| `assertFalse(x)` | `bool(x) is False` |

**Сравнение коллекций** (проверяют ещё и **тип**, не только содержимое)

| Метод | Для чего |
|---|---|
| `assertListEqual` | списки |
| `assertTupleEqual` | кортежи |
| `assertDictEqual` | словари |
| `assertSetEqual` | множества |
| `assertSequenceEqual` | любые последовательности |
| `assertMultiLineEqual` | многострочные строки |

---

## 📖 Основное

### assertTrue/assertFalse вместо сравнения с константой

Если функция и так возвращает `bool`, сравнивать с `True`/`False` через `assertEqual` избыточно:

```python
def is_positive(num):
    return num > 0

class TestIsPositive(unittest.TestCase):
    def test_positive_int(self):
        self.assertTrue(is_positive(69))    # вместо assertEqual(is_positive(69), True)

    def test_negative_int(self):
        self.assertFalse(is_positive(-17))
```

### Почему assertListEqual лучше assertEqual для коллекций

`assertListEqual` при провале показывает **разницу** между списками (какие элементы отличаются), а не просто "не равно":

```python
AssertionError: Lists differ: [1, 2, 3] != [3, 2, 1]

First differing element 0:
1
3
```

Плюс дополнительно проверяет **тип**: если функция вернула кортеж вместо списка, `assertListEqual` это заметит и провалит тест, даже если содержимое совпадает.

### Группировка похожих случаев — subTest()

Если засунуть цикл с несколькими случаями в один `assert`, при провале одного случая **весь цикл прерывается** — об остальных случаях ничего не узнать:

```python
def test_normal_cases(self):
    test_cases = [(1, 2), (2, 2), (3, 6), (7, 504)]
    for n, expected in test_cases:
        self.assertEqual(factorial(n), expected)   # прервётся на первом же несовпадении
```

Контекстный менеджер `subTest()` решает эту проблему — каждая итерация становится независимым подтестом, ошибка одного не прерывает остальные:

```python
def test_normal_cases(self):
    test_cases = [(1, 2), (2, 2), (3, 6), (7, 504)]
    for n, expected in test_cases:
        with self.subTest(n=n):                     # именованный аргумент — отображается в отчёте
            self.assertEqual(factorial(n), expected)
```

Отчёт покажет оба провала отдельно, с пометкой `(n=1)` и `(n=7)` вместо просто `<subtest>`.

Параметр `msg` добавляет собственный текст:

```python
with self.subTest(msg=f'Ошибка при n={n}', n=n):
    self.assertEqual(factorial(n), expected)
```

### Несколько assert в одном subTest

Если внутри одного `subTest()` несколько разных `assert`-методов, при провале первого — остальные внутри этого же `subTest()` не выполнятся (прервётся, как обычный тест). Чтобы видеть все независимо — каждый `assert` в свой `subTest()`:

```python
def test_negative_int(self):
    result = abs(-69)
    with self.subTest():
        self.assertTrue(result < 0)      # провалится
    with self.subTest():
        self.assertEqual(result, -69)    # тоже провалится, но покажется отдельно
```

---

## 💡 Важные нюансы

- `assertEqual` — универсальный метод, но для коллекций/строк специализированные варианты (`assertListEqual` и т.д.) дают более точную диагностику.
- `subTest()` не заменяет параметризацию тестов — это способ группировки похожих случаев в одном методе без потери информации о каждом провале.
- Без `subTest()` цикл с несколькими проверками ведёт себя как единый тест «всё или ничего» до первой ошибки.

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[05 — ⏭ unittest — пропуск тестов]]
- [[11 — 🏷 pytest — маркировка]]

## ❓ Вопросы / Непонятное

- ...
