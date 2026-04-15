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

animal = Animal()
print(animal.sound())  # None — работает, но бессмысленно
```

Абстрактный класс **запрещает** создавать экземпляры базового класса и **гарантирует**, что все наследники реализуют нужные методы.

> [!tip] Главная идея
> Абстрактный класс нужен не столько чтобы запретить создание экземпляра, сколько чтобы **гарантировать**: любой подкласс будет иметь определённый набор методов. Запрет на экземпляр — это лишь следствие.

---

## 📖 Синтаксис

```python
from abc import ABC, abstractmethod

class Animal(ABC):          # Наследуемся от ABC
    @abstractmethod
    def sound(self):        # Обязателен к реализации
        pass

    @abstractmethod
    def move(self):
        pass
```

- `ABC` — Abstract Base Class, из модуля `abc`
- `@abstractmethod` — помечает метод как обязательный к реализации

---

## 📖 Нельзя создать экземпляр абстрактного класса

```python
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

cat = Cat()  # ✅ OK
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
> Ошибка возникает при создании **экземпляра**, не при объявлении класса.

---

## 📖 Абстрактный метод может иметь реализацию

```python
class Animal(ABC):
    @abstractmethod
    def move(self):
        print('Животное движется')  # Базовая реализация есть!

class Cat(Animal):
    def move(self):
        super().move()           # Вызываем базовую
        print('Кот прыгает')

cat = Cat()
cat.move()
# Животное движется
# Кот прыгает
```

Метод всё равно остаётся абстрактным — наследник **обязан** его переопределить.

---

## 📖 Обычные методы в абстрактном классе

```python
class Animal(ABC):
    @abstractmethod
    def sound(self):        # Обязателен
        pass

    def breathe(self):      # Обычный — переопределять не нужно
        return 'вдох-выдох'

class Cat(Animal):
    def sound(self):
        return 'мяу'

cat = Cat()
print(cat.breathe())  # вдох-выдох — унаследован
```

---

## 📖 Комбинации с другими декораторами

`@abstractmethod` всегда **самый внутренний** декоратор:

```python
class C(ABC):
    # Абстрактный метод класса
    @classmethod
    @abstractmethod
    def create(cls): ...

    # Абстрактный статический метод
    @staticmethod
    @abstractmethod
    def validate(value): ...

    # Абстрактное свойство
    @property
    @abstractmethod
    def name(self): ...
```

---

## 📖 Вместо pass — альтернативы

```python
class C(ABC):
    @abstractmethod
    def method1(self):
        '''Строка документации — допустимо'''

    @abstractmethod
    def method2(self):
        raise NotImplementedError  # Явная ошибка — тоже допустимо
```

---

## 📊 Quick Reference

| Что | Как |
|---|---|
| Создать абстрактный класс | `class MyClass(ABC)` |
| Пометить метод как абстрактный | `@abstractmethod` |
| Абстрактный метод класса | `@classmethod` + `@abstractmethod` |
| Абстрактное свойство | `@property` + `@abstractmethod` |
| Вызвать базовую реализацию | `super().method()` в наследнике |
| Посмотреть абстрактные методы | `MyClass.__abstractmethods__` |

---

## 🔗 Связанные темы

- [[02 — 🧩 collections.abc и протоколы]]
- [[01 — 🧬 Введение в наследование]]
- [[Абстракция]] — принцип ООП
