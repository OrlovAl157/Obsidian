---
tags:
  - python
  - тема/ооп
  - статус/готово
---

# 🏷️ Свойства @property

## 📌 Коротко

> `@property` — декоратор, превращающий методы в свойства: позволяет работать с атрибутом через привычный синтаксис `obj.name`, при этом скрытно вызывая геттер, сеттер или делитер.

---

## 📖 Теория

### Зачем нужны свойства?

В прошлой теме мы познакомились с геттерами и сеттерами. У них есть недостаток — работать с атрибутами через методы неудобно: нужно помнить два разных имени (`get_name`, `set_name`), и атрибут перестаёт "ощущаться" как атрибут.

```python
# ❌ Неудобно — нужно помнить названия методов
cat.set_name('Роджер')
print(cat.get_name())

# ✅ Удобно — работаем как с обычным атрибутом
cat.name = 'Роджер'
print(cat.name)
```

**Свойства (property)** — это промежуточное решение между атрибутами и методами. Они позволяют создавать методы, которые **ведут себя как атрибуты**, сохраняя при этом все преимущества геттеров и сеттеров (валидация, контроль доступа).

---

### Функция property()

Встроенная функция `property()` принимает четыре необязательных аргумента и возвращает объект-свойство:

```python
name = property(fget, fset, fdel, doc)
```

| Аргумент | Назначение | По умолчанию |
|---|---|---|
| `fget` | геттер — функция для получения значения | `None` |
| `fset` | сеттер — функция для установки значения | `None` |
| `fdel` | делитер — функция для удаления атрибута | `None` |
| `doc` | строка документации | `None` |

```python
class Cat:
    def __init__(self, name):
        self._name = name          # защищённый атрибут

    def get_name(self):
        return self._name

    def set_name(self, name):
        if isinstance(name, str) and name.isalpha():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    name = property(get_name, set_name)   # создаём свойство


cat = Cat('Кемаль')

# Обращение к свойству — неявно вызывается get_name()
print(cat.name)          # → Кемаль

# Установка значения — неявно вызывается set_name()
cat.name = 'Роджер'
print(cat.name)          # → Роджер

# Некорректное значение — сеттер выбросит исключение
cat.name = 1             # ❌ ValueError: Некорректное имя
```

> 💡 Свойство — это **атрибут класса**, а не экземпляра. Поэтому оно доступно всем экземплярам класса.

---

### Свойство только для чтения

Если передать в `property()` только геттер — свойство будет доступно только для чтения. Попытка записи вызовет ошибку:

```python
class Cat:
    def __init__(self, name):
        self._name = name

    def get_name(self):
        return self._name

    name = property(get_name)    # только геттер


cat = Cat('Кемаль')
print(cat.name)        # → Кемаль
cat.name = 'Роджер'    # ❌ AttributeError: property 'name' of 'Cat' object has no setter
```

---

### Объект property

Свойство — это обычный объект класса `property`. Обратиться к нему можно через сам класс:

```python
print(Cat.name)           # → <property object at 0x...>
print(type(Cat.name))     # → <class 'property'>
```

Внутри объекта `property` хранятся ссылки на геттер, сеттер и делитер — доступны через атрибуты `fget`, `fset`, `fdel`:

```python
print(Cat.name.fget)   # → <function Cat.get_name at 0x...>
print(Cat.name.fset)   # → <function Cat.set_name at 0x...>
print(Cat.name.fdel)   # → None  (делитер не задан)
```

---

### Вычисляемые свойства

Значение свойства не обязано быть фиксированным — оно может **вычисляться** при каждом обращении:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name

    def get_info(self):
        return f'{self.breed} {self.name}'   # вычисляется каждый раз

    info = property(get_info)


cat = Cat('Британский', 'Кемаль')
print(cat.info)    # → Британский Кемаль
```

Если вычисление затратное — можно **кэшировать** результат:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name
        self._info = None              # кэш пустой

    def get_info(self):
        if self._info is None:         # вычисляем только один раз
            self._info = f'{self.breed} {self.name}'
        return self._info

    info = property(get_info)
```

Если свойство вычисляется из нескольких атрибутов и его можно изменять — **сеттер должен обновлять все атрибуты**:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name

    def get_info(self):
        return f'{self.breed} {self.name}'

    def set_info(self, info):
        self.breed, self.name = info.split()   # обновляем оба атрибута

    info = property(get_info, set_info)


cat = Cat('Британский', 'Кемаль')
print(cat.info)          # → Британский Кемаль

cat.info = 'Манчкин Роджер'
print(cat.info)          # → Манчкин Роджер
print(cat.breed)         # → Манчкин  (обновился)
print(cat.name)          # → Роджер   (обновился)
```

---

### ⚠️ Вызов сеттера в __init__

Это важный нюанс, который легко упустить.

**Проблема:** если в `__init__` мы напрямую присваиваем `self._name = name`, сеттер **не вызывается** — валидация обходится при создании объекта:

```python
class Cat:
    def __init__(self, name):
        self._name = name          # прямое обращение — сеттер не вызывается!

    def get_name(self):
        return self._name

    def set_name(self, name):
        if isinstance(name, str) and name.isalpha():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    name = property(get_name, set_name)


cat = Cat(-1)           # некорректное имя — но ошибки нет!
print(cat.name)         # → -1
```

**Решение:** обращаться в `__init__` к свойству `self.name`, а не к атрибуту `self._name` — тогда сеттер вызовется автоматически:

```python
class Cat:
    def __init__(self, name):
        self.name = name           # ✅ обращение к свойству — вызывается сеттер

    def get_name(self):
        return self._name

    def set_name(self, name):
        if isinstance(name, str) and name.isalpha():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    name = property(get_name, set_name)


cat = Cat(-1)           # ❌ ValueError: Некорректное имя  — теперь всё правильно
```

> 💡 В `__init__` пишем `self.name = name` (свойство), а не `self._name = name` (атрибут). Тогда проверка работает и при создании объекта, и при изменении — без дублирования кода.

---

### Декоратор @property

Современный и наиболее популярный способ создания свойств. Вместо того чтобы явно вызывать `property()`, используем декораторы.

```python
class Cat:
    def __init__(self, name):
        self.name = name           # вызываем сеттер через свойство

    @property
    def name(self):                # геттер — имя метода = имя свойства
        return self._name

    @name.setter
    def name(self, name):          # сеттер
        if isinstance(name, str) and name.isalpha():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    @name.deleter
    def name(self):                # делитер
        del self._name
```

**Как это работает под капотом** — три шага:

```python
# Шаг 1: @property создаёт свойство только с геттером
name = property(get_name)

# Шаг 2: @name.setter добавляет сеттер — создаётся НОВЫЙ объект property
name = name.setter(set_name)

# Шаг 3: @name.deleter добавляет делитер — снова новый объект property
name = name.deleter(del_name)
```

Каждый декоратор создаёт новый объект `property`, добавляя к предыдущему новый метод и переприсваивая его той же переменной `name`.

---

### ⚠️ Важные нюансы @property

**1. Все методы должны иметь одинаковое имя** — имя свойства. Иначе вместо одного свойства получится три разных:

```python
class Cat:
    @property
    def name(self):               # свойство name — только геттер
        return self._name

    @name.setter
    def set_name(self, name):     # ❌ другое имя — создаётся НОВОЕ свойство set_name!
        self._name = name
```

**2. Строку документации** нужно указывать в геттере:

```python
class Cat:
    @property
    def name(self):
        """Имя кошки."""       # строка документации — здесь
        return self._name

    @name.setter
    def name(self, name):
        self._name = name


print(Cat.name.__doc__)   # → Имя кошки.
```

**3. Свойство только для чтения** — самый простой случай, только `@property`:

```python
class Cat:
    def __init__(self, name):
        self._name = name

    @property
    def name(self):
        return self._name        # без сеттера — только чтение


cat = Cat('Кемаль')
print(cat.name)      # → Кемаль
cat.name = 'Роджер'  # ❌ AttributeError: property 'name' of 'Cat' object has no setter
```

---

### Как Python понимает какой метод вызвать?

```python
cat.name           # → вызывается геттер  (fget)
cat.name = 'Рекс'  # → вызывается сеттер  (fset)
del cat.name       # → вызывается делитер (fdel)
Cat.name.__doc__   # → строка документации
```

```python
class Cat:
    def __init__(self, name):
        self.name = name        # сеттер — присваивание

    @property
    def name(self):
        return self._name       # геттер — возврат значения

    @name.setter
    def name(self, name):
        if isinstance(name, str) and name.isalpha():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    @name.deleter
    def name(self):
        del self._name


cat = Cat('Кемаль')   # __init__ → сеттер
print(cat.name)       # → геттер → Кемаль
cat.name = 'Роджер'   # → сеттер
del cat.name          # → делитер
print(cat.name)       # ❌ AttributeError — атрибут _name удалён
```

---

### Разбор частой путаницы: self.name vs self._name

| | `self._name` | `self.name` |
|---|---|---|
| Что это | атрибут экземпляра | свойство (атрибут класса) |
| Где хранится | в `__dict__` экземпляра | в `__dict__` класса |
| Обращение | прямой доступ к данным | неявно вызывает геттер/сеттер |
| Создаётся когда | при первом присвоении в сеттере | при определении класса |

```python
class Cat:
    def __init__(self):
        self.name = 'Кемаль'   # обращение к свойству → вызывается сеттер

    @property
    def name(self):
        print(f'геттер: self._name = {self._name!r}')
        return self._name

    @name.setter
    def name(self, new_name):
        print(f'сеттер: new_name = {new_name!r}')
        self._name = new_name   # вот здесь создаётся атрибут экземпляра


obj = Cat()         # сеттер: new_name = 'Кемаль'
obj.name            # геттер: self._name = 'Кемаль'
print(obj._name)    # → Кемаль  (атрибут экземпляра)

# self.name и self._name — это НЕ одно и то же:
# self.name  — свойство, обращение к нему вызывает геттер
# self._name — обычный атрибут, хранящий данные
```

---

### Порядок создания: почему self.name в __init__ вызывает сеттер?

```
Определение класса → тело класса выполняется → все атрибуты и методы создаются
                                                  ↓
                                         свойство name уже существует
                                                  ↓
                              cat = Cat('Кемаль') → вызывается __init__
                                                  ↓
                                    self.name = name → Python видит что
                                    name — это свойство класса → вызывает сеттер
```

> 💡 Свойство — это **атрибут класса**. Атрибуты класса создаются при определении класса, **до** создания любого экземпляра. Поэтому когда `__init__` выполняется, свойство уже существует и `self.name = name` обращается именно к нему.

---

### property() vs @property

| | `property()` | `@property` |
|---|---|---|
| Синтаксис | `name = property(get, set, del)` | декораторы `@property`, `@name.setter`, `@name.deleter` |
| Читаемость | хуже при большом количестве атрибутов | лучше — методы рядом друг с другом |
| Использование | редко в современном коде | стандартный питонический подход |
| Результат | одинаковый объект `property` | одинаковый объект `property` |

---

### Соглашение об именах

Атрибут и управляющее им свойство обычно имеют схожие имена. Атрибут — защищённый (с `_`), свойство — публичное (без `_`):

```python
self._name   # защищённый атрибут — хранит данные
self.name    # публичное свойство — управляет атрибутом
```

---

## 💻 Быстрая шпаргалка

```python
class Person:
    def __init__(self, name, age):
        self.name = name     # через свойство — вызывается сеттер
        self.age = age       # через свойство — вызывается сеттер

    @property
    def name(self):
        """Имя человека."""
        return self._name

    @name.setter
    def name(self, name):
        if isinstance(name, str) and name.strip():
            self._name = name
        else:
            raise ValueError('Некорректное имя')

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, age):
        if isinstance(age, int) and 0 <= age <= 150:
            self._age = age
        else:
            raise ValueError('Некорректный возраст')

    @property
    def info(self):              # вычисляемое свойство — только геттер
        return f'{self._name}, {self._age} лет'


p = Person('Иван', 25)
print(p.name)     # → Иван       (геттер)
p.name = 'Пётр'  # сеттер с валидацией
print(p.info)     # → Пётр, 25 лет

p.age = 200       # ❌ ValueError: Некорректный возраст
p.name = ''       # ❌ ValueError: Некорректное имя

# Доступ к объекту property через класс
print(type(Person.name))      # → <class 'property'>
print(Person.name.fget)       # → геттер
print(Person.name.fset)       # → сеттер
print(Person.name.__doc__)    # → Имя человека.
```

---

## 🔗 Связанные темы

- [[04 — 🔒 Модификаторы доступа и аксессоры]]
- [[05.1 — ⤷ Устройство @property]]
- [[06 — 🔧 Типы методов @classmethod и @staticmethod]]

## ❓ Вопросы / Непонятное

- ...
