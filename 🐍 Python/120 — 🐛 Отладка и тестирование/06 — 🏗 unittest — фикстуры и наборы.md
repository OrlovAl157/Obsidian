---
tags:
  - python
  - тема/тестирование
  - статус/завершён
уровень: продвинутый
---

# 🏗 unittest — фикстуры и наборы тестов

## 📌 Коротко

> Фикстуры — код подготовки данных перед тестами и освобождения ресурсов после них. В unittest есть три уровня: тестового случая (`setUp`/`tearDown`), класса (`setUpClass`/`tearDownClass`) и модуля (`setUpModule`/`tearDownModule`). `TestSuite` и `TestLoader` позволяют объединять и выборочно запускать наборы тестов.

---

## 🚀 Быстрая справка

| Уровень | Методы | Когда выполняются |
|---|---|---|
| Тестовый случай | `setUp()` / `tearDown()` | перед/после **каждого** теста |
| Класс | `setUpClass()` / `tearDownClass()` (classmethod) | один раз на **весь класс** |
| Модуль | `setUpModule()` / `tearDownModule()` (глобальные функции) | один раз на **весь модуль** |

---

## 📖 Основное

### Фикстуры уровня тестового случая

Без фикстур подготовку данных пришлось бы дублировать в каждом методе. `setUp()` выполняется перед **каждым** тестом, `tearDown()` — сразу после:

```python
class TestCat(unittest.TestCase):
    def setUp(self):
        self.cat = Cat('Кемаль')     # новый экземпляр перед каждым тестом

    def tearDown(self):
        del self.cat

    def test_get(self):
        self.assertEqual(self.cat.name, 'Кемаль')

    def test_set(self):
        self.cat.name = 'Роджер'
        self.assertEqual(self.cat.name, 'Роджер')
```

### Фикстуры уровня класса

`setUpClass()`/`tearDownClass()` — classmethod'ы, выполняются **один раз** на весь класс, а не на каждый тест. Атрибут создаётся у `cls`, а не у `self`:

```python
class TestCat(unittest.TestCase):
    @classmethod
    def setUpClass(cls):
        cls.cat = Cat('Кемаль')      # общий объект для всех тестов класса

    @classmethod
    def tearDownClass(cls):
        del cls.cat
```

**Важная ловушка:** так как объект общий, порядок выполнения тестов (лексикографический!) начинает влиять на результат. Если `test_set` выполнится раньше `test_get`, `test_get` провалится — состояние объекта уже изменено предыдущим тестом.

### tearDown вызывается почти всегда

`tearDown()`/`tearDownClass()`/`tearDownModule()` выполняются, даже если тест внутри упал с исключением. Исключение — если ошибка произошла в самих `setUp()`/`setUpClass()`/`setUpModule()`: тогда соответствующий `tearDown` **не вызывается**, а все тесты этого уровня отмечаются как `ERROR`.

### TestSuite — объединение тестов в наборы

Позволяет выполнить не всё сразу, а только выбранные тестовые случаи:

```python
test_cases = [TestArithmetic('test_add'), TestArithmetic('test_sub')]
suite = unittest.TestSuite(test_cases)

runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

Добавлять тесты можно и постепенно: `suite.addTest(...)` — один тест, `suite.addTests([...])` — сразу коллекция.

### TestLoader — автозагрузка всех тестов класса

Чтобы не перечислять методы вручную, загрузчик сам находит все тесты класса:

```python
loader = unittest.defaultTestLoader
suite = loader.loadTestsFromTestCase(TestArithmetic)   # уже готовый TestSuite

runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

Набор может объединять тесты из **разных** классов — не обязательно из одного.

---

## 💡 Важные нюансы

- Уровень класса/модуля — экономия ресурсов (не пересоздавать тяжёлый объект на каждый тест), но плата — риск зависимости тестов друг от друга через общее состояние.
- Порядок тестов внутри класса — лексикографический по имени метода, не по порядку в файле.
- `TestSuite` полезен, когда нужно гонять только часть тестов (например, только для определённой ОС или только «быстрые» тесты).

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[10 — 🐍 pytest — основы]]

## ❓ Вопросы / Непонятное

- ...
