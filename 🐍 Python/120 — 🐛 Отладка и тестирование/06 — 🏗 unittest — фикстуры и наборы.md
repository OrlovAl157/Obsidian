---
tags: [python, тестирование, unittest]
difficulty: intermediate
---

# 🏗 unittest — фикстуры и наборы — памятка

> Фикстуры — код подготовки данных перед тестами и освобождения ресурсов после. В unittest три уровня: тестового случая (`setUp/tearDown`), класса (`setUpClass/tearDownClass`) и модуля (`setUpModule/tearDownModule`). `TestSuite` позволяет объединять и выборочно запускать тесты.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#🟢 setUp и tearDown — уровень метода|setUp и tearDown]]
- [[#🔵 setUpClass и tearDownClass — уровень класса|setUpClass и tearDownClass]]
- [[#🔴 setUpModule и tearDownModule — уровень модуля|setUpModule и tearDownModule]]
- [[#🟡 Порядок выполнения фикстур|Порядок выполнения]]
- [[#🟣 TestSuite — наборы тестов|TestSuite]]
- [[#⚙️ TestLoader — автозагрузка|TestLoader]]
- [[#⚡ Быстрые примеры|Быстрые примеры]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Уровень | Методы | Когда выполняются |
|---|---|---|
| Тестовый метод | `setUp()` / `tearDown()` | перед/после **каждого** теста |
| Класс | `setUpClass()` / `tearDownClass()` | один раз на **весь класс** |
| Модуль | `setUpModule()` / `tearDownModule()` | один раз на **весь модуль** |

| Метод | Декоратор | Параметр |
|---|---|---|
| `setUp` | нет | `self` |
| `tearDown` | нет | `self` |
| `setUpClass` | `@classmethod` | `cls` |
| `tearDownClass` | `@classmethod` | `cls` |
| `setUpModule` | нет (функция) | нет |
| `tearDownModule` | нет (функция) | нет |

---

## 📊 Общая схема

```
setUpModule()                        ← один раз для всего модуля
│
├── setUpClass()                     ← один раз для всего класса
│   │
│   ├── setUp()                      ← перед каждым тестом
│   │   test_one()
│   │   tearDown()                   ← после каждого теста
│   │
│   ├── setUp()
│   │   test_two()
│   │   tearDown()
│   │
│   └── setUp()
│       test_three()
│       tearDown()
│   │
│   tearDownClass()                  ← один раз после всего класса
│
tearDownModule()                     ← один раз после всего модуля

Важно: tearDown вызывается даже если тест упал.
        НО: если setUp упал — tearDown НЕ вызывается!
```

---

## 🟢 setUp и tearDown — уровень метода

**Назначение:** подготовить данные перед каждым тестом и очистить после. Каждый тест получает свежий объект — тесты не зависят друг от друга.

```python
import unittest


class Cat:
    def __init__(self, name):
        self.name = name


class TestCat(unittest.TestCase):

    def setUp(self):
        """Выполняется перед КАЖДЫМ тестом."""
        self.cat = Cat('Кемаль')
        print(f'\nsetUp: создан {self.cat.name}')

    def tearDown(self):
        """Выполняется после КАЖДОГО теста (даже если тест упал)."""
        print(f'tearDown: удалён {self.cat.name}')
        del self.cat

    def test_name(self):
        self.assertEqual(self.cat.name, 'Кемаль')

    def test_rename(self):
        self.cat.name = 'Роджер'
        self.assertEqual(self.cat.name, 'Роджер')
        # Следующий тест получит нового кота — не Роджера!

    def test_type(self):
        self.assertIsInstance(self.cat, Cat)
```

Порядок выполнения (лексикографический):
```
setUp → test_name → tearDown
setUp → test_rename → tearDown
setUp → test_type → tearDown
```

**Реальные примеры использования:**

```python
class TestDatabase(unittest.TestCase):

    def setUp(self):
        # Создать тестовую БД в памяти
        self.conn = sqlite3.connect(':memory:')
        self.cursor = self.conn.cursor()
        self.cursor.execute('CREATE TABLE users (id INTEGER, name TEXT)')

    def tearDown(self):
        # Закрыть соединение после каждого теста
        self.conn.close()

    def test_insert(self):
        self.cursor.execute("INSERT INTO users VALUES (1, 'Alice')")
        self.conn.commit()
        self.cursor.execute("SELECT COUNT(*) FROM users")
        self.assertEqual(self.cursor.fetchone()[0], 1)
```

---

## 🔵 setUpClass и tearDownClass — уровень класса

**Назначение:** создать общий объект один раз для всего класса. Полезно для дорогих ресурсов — подключение к БД, загрузка файла. Экономит время но создаёт риск зависимости тестов.

```python
class TestCatClass(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        """Выполняется ОДИН РАЗ перед всеми тестами класса."""
        cls.cat = Cat('Кемаль')   # атрибут класса, не экземпляра!
        print('\nsetUpClass: кот создан')

    @classmethod
    def tearDownClass(cls):
        """Выполняется ОДИН РАЗ после всех тестов класса."""
        del cls.cat
        print('tearDownClass: кот удалён')

    def test_name(self):
        self.assertEqual(self.cat.name, 'Кемаль')

    def test_type(self):
        self.assertIsInstance(self.cat, Cat)
```

**Ловушка с общим состоянием:**

```python
class TestCatClass(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        cls.cat = Cat('Кемаль')

    def test_a_rename(self):         # выполнится первым (лексикографически)
        self.cat.name = 'Роджер'     # ← изменяет общий объект!

    def test_b_name(self):           # выполнится вторым
        self.assertEqual(self.cat.name, 'Кемаль')  # ❌ провалится — cat уже 'Роджер'
```

Если тесты меняют общий объект — используй `setUp/tearDown` (уровень метода).

---

## 🔴 setUpModule и tearDownModule — уровень модуля

**Назначение:** выполнить настройку один раз для всего модуля — самый высокий уровень. Обычные функции, не методы класса.

```python
import unittest


def setUpModule():
    """Выполняется один раз перед всеми тестами модуля."""
    print('\nМодуль: начало тестирования')
    # Например: запустить сервер, подключиться к внешней системе


def tearDownModule():
    """Выполняется один раз после всех тестов модуля."""
    print('Модуль: завершение тестирования')
    # Например: остановить сервер, закрыть соединения


class TestA(unittest.TestCase):
    def test_something(self):
        self.assertTrue(True)


class TestB(unittest.TestCase):
    def test_something_else(self):
        self.assertEqual(1 + 1, 2)
```

---

## 🟡 Порядок выполнения фикстур

```python
# Полный порядок при двух классах в одном модуле:

setUpModule()

    setUpClass() ← TestA
        setUp() → test_a_one → tearDown()
        setUp() → test_a_two → tearDown()
    tearDownClass() ← TestA

    setUpClass() ← TestB
        setUp() → test_b_one → tearDown()
        setUp() → test_b_two → tearDown()
    tearDownClass() ← TestB

tearDownModule()
```

**Поведение при ошибке в setUp:**

```python
# Если setUp() бросит исключение:
setUp()           # ← бросает исключение
# tearDown()      ← НЕ вызывается!
# тест помечается как ERROR, не FAIL

# Если сам тест бросит исключение:
setUp()           # выполнился
test_method()     # ← бросает исключение
tearDown()        # ← всё равно вызывается ✅
```

---

## 🟣 TestSuite — наборы тестов

**Назначение:** объединить выбранные тесты и запустить только их — без запуска всего модуля.

```python
import unittest


class TestArithmetic(unittest.TestCase):

    def test_add(self):
        self.assertEqual(1 + 1, 2)

    def test_sub(self):
        self.assertEqual(5 - 3, 2)

    def test_mul(self):
        self.assertEqual(3 * 4, 12)


# Вариант 1: вручную перечислить тесты
suite = unittest.TestSuite([
    TestArithmetic('test_add'),
    TestArithmetic('test_sub'),
])

# Вариант 2: добавлять по одному
suite = unittest.TestSuite()
suite.addTest(TestArithmetic('test_add'))
suite.addTests([TestArithmetic('test_sub'), TestArithmetic('test_mul')])

# Запуск
runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

**Объединение нескольких классов:**

```python
class TestStrings(unittest.TestCase):
    def test_upper(self):
        self.assertEqual('hello'.upper(), 'HELLO')


# Набор из разных классов
suite = unittest.TestSuite([
    TestArithmetic('test_add'),
    TestStrings('test_upper'),
])

runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

---

## ⚙️ TestLoader — автозагрузка

**Назначение:** не перечислять тесты вручную — загрузчик сам найдёт все методы класса начинающиеся с `test`.

```python
loader = unittest.defaultTestLoader

# Загрузить все тесты одного класса
suite = loader.loadTestsFromTestCase(TestArithmetic)

# Загрузить все тесты модуля
import test_mymodule
suite = loader.loadTestsFromModule(test_mymodule)

# Объединить несколько классов
suite = unittest.TestSuite([
    loader.loadTestsFromTestCase(TestArithmetic),
    loader.loadTestsFromTestCase(TestStrings),
])

runner = unittest.TextTestRunner(verbosity=2)
runner.run(suite)
```

---

## ⚡ Быстрые примеры

```python
import unittest
import tempfile
import os


class TestFileOperations(unittest.TestCase):

    def setUp(self):
        # Создать временный файл перед каждым тестом
        self.tmpfile = tempfile.NamedTemporaryFile(delete=False)
        self.tmpfile.write(b'test content')
        self.tmpfile.close()

    def tearDown(self):
        # Удалить временный файл после каждого теста
        os.unlink(self.tmpfile.name)

    def test_file_exists(self):
        self.assertTrue(os.path.exists(self.tmpfile.name))

    def test_file_content(self):
        with open(self.tmpfile.name, 'rb') as f:
            self.assertEqual(f.read(), b'test content')


class TestExpensiveSetup(unittest.TestCase):
    """Пример с дорогой инициализацией на уровне класса."""

    @classmethod
    def setUpClass(cls):
        # Инициализация один раз — например загрузка модели ML
        cls.data = list(range(1000000))

    def test_length(self):
        self.assertEqual(len(self.data), 1000000)

    def test_first(self):
        self.assertEqual(self.data[0], 0)

    def test_last(self):
        self.assertEqual(self.data[-1], 999999)
```

---

## 💡 Практические замечания

- `setUp/tearDown` — для большинства случаев, гарантирует изоляцию тестов
- `setUpClass/tearDownClass` — только если создание объекта реально дорого (БД, сеть, файлы)
- При использовании `setUpClass` тесты не должны изменять общий объект — иначе порядок выполнения влияет на результат
- `tearDown` вызывается даже при провале теста — безопасно освобождать ресурсы
- `tearDown` НЕ вызывается если упал `setUp` — учитывай при освобождении ресурсов
- `TestSuite` удобен для CI — разные наборы для быстрых и медленных тестов

---

## ⚠️ Частые ошибки

**❌ Забыл @classmethod на setUpClass:**
```python
class TestCat(unittest.TestCase):
    def setUpClass(cls):          # ❌ нет @classmethod → TypeError
        cls.cat = Cat('Кемаль')

    @classmethod
    def setUpClass(cls):          # ✅
        cls.cat = Cat('Кемаль')
```

**❌ Используешь self вместо cls в setUpClass:**
```python
@classmethod
def setUpClass(cls):
    self.cat = Cat('Кемаль')   # ❌ NameError — self не существует в classmethod
    cls.cat = Cat('Кемаль')    # ✅
```

**❌ Тесты меняют общий объект setUpClass:**
```python
@classmethod
def setUpClass(cls):
    cls.items = []

def test_a_append(self):
    self.items.append(1)          # ❌ меняет список для всех следующих тестов!

def test_b_empty(self):
    self.assertEqual(self.items, [])  # ❌ провалится — список уже [1]
```

**❌ Ожидаешь что tearDown не вызовется при провале теста:**
```python
def setUp(self):
    self.conn = open_connection()

def tearDown(self):
    self.conn.close()   # ✅ вызовется даже если тест упал

# НО:
def setUp(self):
    raise Exception('ошибка')    # tearDown НЕ вызовется!
```

---

## ✅ Главные правила

✅ `setUp/tearDown` — перед/после каждого теста, `self`  
✅ `setUpClass/tearDownClass` — `@classmethod`, `cls`, один раз на класс  
✅ `setUpModule/tearDownModule` — обычные функции, один раз на модуль  
✅ `tearDown` вызывается даже при провале теста  
✅ `tearDown` НЕ вызывается если упал `setUp`  
✅ `setUpClass` — только если ресурс дорогой; тесты не должны его изменять  
✅ `TestSuite` — для выборочного запуска тестов  
✅ `TestLoader` — автоматически находит все тесты класса/модуля  

---

## 🔗 Связанные темы

- [[03 — 🧩 unittest — основы]]
- [[04 — 🧰 unittest — методы assert]]
- [[05 — ⏭ unittest — пропуск тестов]]
- [[10 — 🐍 pytest — основы]]

---

#python/тестирование #unittest #фикстуры
