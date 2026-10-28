---
tags:
  - python
  - ООП
  - абстрактные_классы
  - статус/завершён
---

# 🔒 Абстрактные классы (abc)

## 📌 Коротко

> Абстрактный класс — это **контракт для наследников**. Он говорит: "Вот что ты **обязан** реализовать — а как именно, решай сам." Экземпляр абстрактного класса создать нельзя.

---

## 📖 Зачем нужны абстрактные классы

Без абстрактных классов базовый класс можно создать напрямую — даже если это не имеет смысла:

```python
class Animal:
    def sound(self):
        pass
    def move(self):
        pass

animal = Animal()
print(animal.sound())  # None — работает, но бессмысленно
print(animal.move())   # None — то же самое
```

Абстрактный класс **запрещает** создавать экземпляры базового класса и **гарантирует**, что все наследники реализуют нужные методы.

> [!tip] Главная идея
> Абстрактный класс нужен не столько чтобы запретить создание экземпляра, сколько чтобы **гарантировать**: любой подкласс будет иметь определённый набор методов. Запрет на экземпляр — это лишь следствие.

Абстрактный класс имеет несколько особенностей:
- Содержит один или несколько **абстрактных методов** — методов без реализации
- **Не позволяет** создавать свои экземпляры напрямую
- Предоставляет **общий интерфейс** для наследников, чтобы избежать дублирования кода
- **Заставляет** подклассы реализовывать абстрактные методы

---

## 📖 Синтаксис

```python
from abc import ABC, abstractmethod

class Animal(ABC):          # Наследуемся от ABC
    @abstractmethod
    def sound(self):        # Абстрактный метод — обязателен к реализации
        pass

    @abstractmethod
    def move(self):
        pass
```

- `ABC` — Abstract Base Class (Абстрактный Базовый Класс), импортируется из модуля `abc`
- `@abstractmethod` — декоратор, помечает метод как обязательный к реализации

---

## 📖 Нельзя создать экземпляр абстрактного класса

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def move(self):
        pass

    @abstractmethod
    def sound(self):
        pass

animal = Animal()
# TypeError: Can't instantiate abstract class Animal
# with abstract methods move, sound
```

> [!warning] Важно
> Класс является абстрактным только если **одновременно**:
> 1. Наследуется от `ABC`
> 2. Имеет хотя бы один `@abstractmethod`
>
> Без любого из условий — класс обычный, экземпляр создать можно.

---

## 📖 Наследник обязан реализовать все абстрактные методы

```python
class Cat(Animal):
    def sound(self):
        return 'мяу'

    def move(self):
        return 'кот бежит'

cat = Cat()  # ✅ OK — все абстрактные методы реализованы
```

Если забыть хотя бы один:

```python
class Dog(Animal):
    def sound(self):
        return 'гав'
    # move() не реализован!

dog = Dog()
# TypeError: Can't instantiate abstract class Dog
# with abstract method move
```

> [!tip] Когда возникает ошибка
> Ошибка возникает при создании **экземпляра**, не при объявлении класса. Сам класс `Dog` создастся без проблем — ошибка только при `Dog()`.

---

## 📖 Абстрактный метод может иметь реализацию

В отличие от некоторых других языков, в Python абстрактный метод **может содержать код**. Наследник может вызвать его через `super()`:

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def sound(self):
        print('Не определен')

    @abstractmethod
    def move(self):
        print('Животное движется')  # Базовая реализация

class Cat(Animal):
    def sound(self):
        print('мяу')

    def move(self):
        super().move()          # Вызываем базовую реализацию
        print('Кот движется')   # Добавляем свою

cat = Cat()
cat.move()
cat.sound()
# Животное движется
# Кот движется
# мяу
```

Метод всё равно остаётся абстрактным — наследник **обязан** его переопределить, даже если вызывает `super()`.

---

## 📖 Обычные методы в абстрактном классе

Абстрактный класс может содержать и **обычные методы** — их переопределять не обязательно:

```python
from abc import ABC, abstractmethod

class C(ABC):
    @abstractmethod
    def method1(self):      # Обязателен к реализации
        pass

    def method2(self):      # Обычный — наследуется как есть
        return 2

class D(C):
    def method1(self):
        return 1

d = D()
print(d.method1())  # 1 — своя реализация
print(d.method2())  # 2 — унаследован от C
```

---

## 📖 Комбинации с другими декораторами

`@abstractmethod` можно комбинировать с `@property`, `@classmethod`, `@staticmethod`. Важно: `@abstractmethod` всегда **самый внутренний** декоратор.

### Абстрактный метод класса

```python
from abc import ABC, abstractmethod

class C(ABC):
    @classmethod
    @abstractmethod
    def abstract_class_method(cls):
        ...
```

### Абстрактный статический метод

```python
from abc import ABC, abstractmethod

class C(ABC):
    @staticmethod
    @abstractmethod
    def abstract_static_method():
        ...
```

### Абстрактное свойство (только чтение)

```python
from abc import ABC, abstractmethod

class C(ABC):
    @property
    @abstractmethod
    def abstract_property(self):
        ...
```

### Абстрактное свойство (чтение и запись)

Если только некоторые компоненты абстрактные — помечаем только их:

```python
from abc import ABC, abstractmethod

class C(ABC):
    @property
    def x(self):          # getter — обычный
        ...

    @x.setter
    @abstractmethod
    def x(self, value):   # setter — абстрактный
        ...

class D(C):
    @C.x.setter           # Реализуем только абстрактный компонент
    def x(self, value):
        self._x = value
```

---

## 📖 Вместо pass — альтернативы

```python
from abc import ABC, abstractmethod

class C(ABC):
    @abstractmethod
    def method1(self, input):
        '''Строка документации — допустимо'''

    @abstractmethod
    def method2(self, output, data):
        raise NotImplementedError  # Явная ошибка — тоже допустимо
```

---

## 📖 Посмотреть абстрактные методы класса

```python
from abc import ABC, abstractmethod

class Animal(ABC):
    @abstractmethod
    def sound(self): pass

    @abstractmethod
    def move(self): pass

print(Animal.__abstractmethods__)
# frozenset({'move', 'sound'})
```

---

## 📊 Quick Reference

| Что | Как |
|---|---|
| Создать абстрактный класс | `class MyClass(ABC)` |
| Пометить метод как абстрактный | `@abstractmethod` |
| Абстрактный метод класса | `@classmethod` + `@abstractmethod` (abstractmethod внутри) |
| Абстрактный статический метод | `@staticmethod` + `@abstractmethod` |
| Абстрактное свойство | `@property` + `@abstractmethod` |
| Вызвать базовую реализацию | `super().method()` в наследнике |
| Посмотреть абстрактные методы | `MyClass.__abstractmethods__` |

---

## 🔗 Связанные темы

- [[02 — 🧩 collections.abc и протоколы]]
- [[01 — 🧬 Введение в наследование]]
- [[02 — 🔁 super() и расширение методов]]
- [[Абстракция]] — принцип ООП
