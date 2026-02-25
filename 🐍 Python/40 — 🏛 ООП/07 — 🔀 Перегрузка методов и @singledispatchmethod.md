---
tags:
  - python
  - тема/ооп
  - статус/готово
---

# 🔀 Перегрузка методов и @singledispatchmethod

## 📌 Коротко

> Python не поддерживает перегрузку методов напрямую — повторное определение просто перезаписывает предыдущее. Альтернативы: необязательные аргументы, методы класса как конструкторы, или `@singledispatchmethod` для выбора реализации по типу аргумента.

---

## 📖 Теория

### Перегрузка методов

В многих языках (C++, C#, Java) можно определить несколько одноимённых методов с разной сигнатурой — это называется **перегрузкой методов**. Python такое не поддерживает напрямую.

Причина: класс хранит методы в словаре `__dict__`, а словарь не может иметь повторяющихся ключей — последнее определение просто перезаписывает предыдущее:

```python
class Cat:
    def say(self):
        print('meow')

    def say(self):        # перезаписывает предыдущий say()
        print('мяу')


cat = Cat()
cat.say()   # → мяу  (только последняя версия)
```

То же самое происходит с обычными функциями вне класса — последнее определение побеждает.

---

### Способы имитации нескольких инициализаторов

Поскольку `__init__` — обычный метод, его тоже нельзя перегружать. Но есть несколько способов обойти это ограничение.

---

#### Способ 1 — Необязательные аргументы (самый простой)

```python
class Cat:
    def __init__(self, breed='Британский', name='Кемаль'):
        self.breed = breed
        self.name = name


cat1 = Cat()                                    # используем оба значения по умолчанию
cat2 = Cat(name='Роджер')                       # только имя своё
cat3 = Cat(breed='Шотландский', name='Роджер')  # всё своё

print(cat1.breed, cat1.name)   # → Британский Кемаль
print(cat2.breed, cat2.name)   # → Британский Роджер
print(cat3.breed, cat3.name)   # → Шотландский Роджер
```

> 💡 Чистый питонический подход, подходит в большинстве случаев.

---

#### Способ 2 — Проверка типов аргументов (анти-паттерн)

```python
from datetime import date

class Cat:
    def __init__(self, breed, name, birth_date):
        self.breed = breed
        self.name = name
        if isinstance(birth_date, date):
            self.birth_date = birth_date
        elif isinstance(birth_date, str):
            self.birth_date = date.fromisoformat(birth_date)
        else:
            raise ValueError(f'неверный формат даты: {birth_date}')


cat1 = Cat('Британский', 'Кемаль', date(2021, 3, 31))   # объект date
cat2 = Cat('Шотландский', 'Роджер', '2020-09-10')        # строка ISO

print(cat1.birth_date)   # → 2021-03-31
print(cat2.birth_date)   # → 2020-09-10
```

> ⚠️ Это **анти-паттерн** — плохо масштабируется. Для каждого нового типа нужно добавлять `elif`, код быстро разрастается.

---

#### Способ 3 — Методы класса (рекомендуемый подход)

Самый чистый и питонический способ. Методы класса используются как **альтернативные конструкторы**. По соглашению такие методы называют с префиксом `from_`:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name

    @classmethod
    def create_british_cat(cls, name):
        return cls('Британский', name)

    @classmethod
    def create_kemal_cat(cls, breed):
        return cls(breed, 'Кемаль')


cat1 = Cat('Британский', 'Кемаль')          # стандартный способ
cat2 = Cat.create_british_cat('Роджер')      # альтернативный
cat3 = Cat.create_kemal_cat('Шотландский')   # альтернативный

print(cat1.breed, cat1.name)   # → Британский Кемаль
print(cat2.breed, cat2.name)   # → Британский Роджер
print(cat3.breed, cat3.name)   # → Шотландский Кемаль
```

> 💡 Встроенные типы Python используют именно этот подход: `dict.fromkeys()`, `datetime.fromtimestamp()`, `date.fromisoformat()` — всё это альтернативные конструкторы.

---

#### Способ 4 — Декоратор @singledispatchmethod

Наиболее продвинутый способ. Позволяет выбирать нужную реализацию метода **в зависимости от типа первого аргумента**.

---

### Декоратор @singledispatchmethod

Импортируется из модуля `functools`. Создаёт **универсальную функцию одиночной диспетчеризации** — метод, у которого есть базовая реализация и несколько альтернативных, каждая для своего типа аргумента.

```python
from functools import singledispatchmethod


class MyClass:
    @singledispatchmethod
    def base_implementation(self, arg):          # базовая реализация
        print('Базовая реализация')

    @base_implementation.register(int)
    def int_implementation(self, arg):           # для int
        print('Реализация для целочисленного аргумента')

    @base_implementation.register(str)
    def str_implementation(self, arg):           # для str
        print('Реализация для строкового аргумента')


obj = MyClass()

obj.base_implementation(1)          # → Реализация для целочисленного аргумента
obj.base_implementation('bee')      # → Реализация для строкового аргумента
obj.base_implementation([1, 2, 3])  # → Базовая реализация  (нет подходящей — берётся базовая)
```

**Как работает:**

- `@singledispatchmethod` — помечает метод как базовую реализацию
- `@базовый.register(тип)` — закрепляет альтернативную реализацию за конкретным типом
- При вызове Python смотрит на тип **первого аргумента после `self`** и выбирает нужную реализацию

> ⚠️ Альтернативные реализации **не должны иметь то же имя** что и базовая. Их принято называть с префиксом `_`, делая защищёнными.

---

### Перегрузка __init__ через @singledispatchmethod

```python
from functools import singledispatchmethod


class Cat:
    @singledispatchmethod
    def __init__(self, breed, name, age):        # базовая реализация
        self.breed = breed
        self.name = name
        self.age = age

    @__init__.register(list)
    def _from_list(self, data):                  # альтернатива для list
        self.breed, self.name, self.age = data


cat1 = Cat('Британский', 'Кемаль', 1)           # стандартный вызов
cat2 = Cat(['Манчкин', 'Роджер', 1])            # передаём список

print(cat1.breed, cat1.name, cat1.age)   # → Британский Кемаль 1
print(cat2.breed, cat2.name, cat2.age)   # → Манчкин Роджер 1
```

---

### Один метод — несколько типов

Можно закрепить одну реализацию сразу за несколькими типами, просто добавив несколько декораторов:

```python
from functools import singledispatchmethod


class Cat:
    @singledispatchmethod
    def __init__(self, breed, name, age):
        self.breed = breed
        self.name = name
        self.age = age

    @__init__.register(list)
    @__init__.register(tuple)               # и list, и tuple — одна реализация
    def _from_list_tuple(self, data):
        self.breed, self.name, self.age = data


cat1 = Cat(('Британский', 'Кемаль', 1))    # tuple
cat2 = Cat(['Манчкин', 'Роджер', 1])       # list

print(cat1.breed, cat1.name, cat1.age)   # → Британский Кемаль 1
print(cat2.breed, cat2.name, cat2.age)   # → Манчкин Роджер 1
```

---

### Аннотации типов вместо register(тип)

Вместо явного указания типа в `register(тип)` можно использовать аннотации:

```python
from functools import singledispatchmethod


class MyClass:
    @singledispatchmethod
    def base_implementation(self, arg):
        print('Базовая реализация')

    @base_implementation.register          # без аргумента — тип берётся из аннотации
    def int_implementation(self, arg: int):
        print('Реализация для целочисленного аргумента')

    @base_implementation.register
    def str_implementation(self, arg: str):
        print('Реализация для строкового аргумента')


obj = MyClass()
obj.base_implementation(1)       # → Реализация для целочисленного аргумента
obj.base_implementation('bee')   # → Реализация для строкового аргумента
```

> ⚠️ В Python 3.10 и ниже нельзя объединять типы через `|` в аннотации — это вызовет `TypeError`. В Python 3.11 это поведение исправлено.

> ⚠️ Абстрактные типы из модуля `typing` (например `typing.Iterable`) использовать нельзя — будет `TypeError`.

---

### Работает с любым типом метода

`@singledispatchmethod` можно применять не только к методам экземпляра, но и к методам класса и статическим методам:

```python
from functools import singledispatchmethod


class MyClass:
    # Перегрузка метода экземпляра
    @singledispatchmethod
    def method(self, arg):
        print('Базовая реализация метода экземпляра')

    @method.register(int)
    def _method(self, arg):
        print('Метод экземпляра для int')

    # Перегрузка метода класса
    @singledispatchmethod
    @classmethod
    def class_method(cls, arg):
        print('Базовая реализация метода класса')

    @class_method.register(str)
    @classmethod
    def _class_method(cls, arg):
        print('Метод класса для str')

    # Перегрузка статического метода
    @singledispatchmethod
    @staticmethod
    def static_method(arg):
        print('Базовая реализация статического метода')

    @static_method.register(list)
    @staticmethod
    def _static_method(arg):
        print('Статический метод для list')


obj = MyClass()

obj.method('bee')           # → Базовая реализация метода экземпляра
obj.method(1)               # → Метод экземпляра для int

obj.class_method(1)         # → Базовая реализация метода класса
obj.class_method('bee')     # → Метод класса для str

obj.static_method(1)        # → Базовая реализация статического метода
obj.static_method([1, 2])   # → Статический метод для list
```

---

### ⚠️ Важный нюанс — только позиционный аргумент

При вызове метода с `@singledispatchmethod` первый аргумент (после `self`) **обязательно должен быть позиционным**. Если передать его как именованный — будет ошибка:

```python
obj.base_implementation(arg=1)   # ❌ IndexError (Python ≤ 3.12)
                                  # ❌ TypeError (Python 3.13+)
obj.base_implementation(1)        # ✅ правильно
```

**Почему:** декоратор определяет тип аргумента через `args[0].__class__`. Если нет позиционных аргументов — кортеж `args` пустой и обращение по индексу падает.

---

### @singledispatch для обычных функций

Модуль `functools` также содержит `@singledispatch` — для перегрузки обычных функций вне класса:

```python
from functools import singledispatch

@singledispatch
def base_implementation(arg):
    print('Базовая реализация')

@base_implementation.register
def intfloat_implementation(arg: int | float):
    print('Реализация для int и float')

@base_implementation.register
def str_implementation(arg: str):
    print('Реализация для str')


base_implementation([1, 2, 3])   # → Базовая реализация
base_implementation(1)           # → Реализация для int и float
base_implementation(1.0)         # → Реализация для int и float
base_implementation('bee')       # → Реализация для str
```

---

### Ограничения @singledispatchmethod

- Диспетчеризация только по **одному** аргументу — первому после `self`
- Нельзя использовать `typing.Iterable` и другие абстрактные типы из `typing`
- Объединение типов через `|` работает только с Python 3.11+
- Первый аргумент должен быть позиционным

> 💡 Для перегрузки по нескольким аргументам используются сторонние библиотеки: `multipledispatch` и `multimethod`.

---

## 💻 Быстрая шпаргалка

```python
from functools import singledispatchmethod


class Cat:
    @singledispatchmethod
    def __init__(self, breed, name, age):      # базовая реализация
        self.breed = breed
        self.name = name
        self.age = age

    @__init__.register(list)
    @__init__.register(tuple)                  # один метод — два типа
    def _from_sequence(self, data):
        self.breed, self.name, self.age = data

    @singledispatchmethod
    def describe(self, detail):                # базовая
        print(f'{self.name}')

    @describe.register(int)
    def _describe_int(self, detail: int):      # для int
        print(f'{self.name}, возраст: {self.age}')

    @describe.register(str)
    def _describe_str(self, detail: str):      # для str
        print(f'{self.name} ({self.breed}), {detail}')


cat1 = Cat('Британский', 'Кемаль', 3)
cat2 = Cat(['Манчкин', 'Роджер', 1])
cat3 = Cat(('Шотландский', 'Барсик', 5))

cat1.describe(True)            # → Кемаль  (bool не зарегистрирован — базовая)
cat1.describe(1)               # → Кемаль, возраст: 3
cat1.describe('любит рыбу')    # → Кемаль (Британский), любит рыбу
```

| Способ | Когда использовать | Плюсы | Минусы |
|---|---|---|---|
| Необязательные аргументы | аргументы одного типа | просто, чисто | не подходит для разных типов |
| Проверка типов `isinstance` | редко | гибко | анти-паттерн, плохо масштабируется |
| Методы класса `@classmethod` | разные способы создания объекта | чисто, питонично | нужно помнить имена методов |
| `@singledispatchmethod` | разные типы одного аргумента | автоматический выбор по типу | только один аргумент для диспетчеризации |

---

## 🔗 Связанные темы

- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]
- [[03 — 📦 Атрибуты объектов и классов]]

## ❓ Вопросы / Непонятное

- ...
