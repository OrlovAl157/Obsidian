---
tags:
  - python
  - ООП
  - наследование
  - статус/завершён
---

# 🔁 super() и расширение методов

## 📌 Коротко

> `super()` — встроенная функция, которая даёт доступ к родительскому классу без явного указания его имени. Используется для расширения методов, а не их полной замены.

---

## 📖 Проблема: дублирование кода при переопределении

Когда дочерний класс переопределяет `__init__()`, приходится повторять код родителя:

```python
class Animal:
    def __init__(self, name, age, eyecolor):
        self.name = name
        self.age = age
        self.eyecolor = eyecolor

class Cat(Animal):
    def __init__(self, name, age, eyecolor, breed):
        self.name = name        # ❌ Дублирование
        self.age = age          # ❌ Дублирование
        self.eyecolor = eyecolor  # ❌ Дублирование
        self.breed = breed
```

---

## 📖 Решение 1: явный вызов родительского метода

```python
class Cat(Animal):
    def __init__(self, name, age, eyecolor, breed):
        Animal.__init__(self, name, age, eyecolor)  # Явное обращение
        self.breed = breed
```

**Недостаток:** если переименовать `Animal` или сменить родителя — нужно обновлять вручную везде.

---

## 📖 Решение 2: super()

`super()` обращается к родительскому классу **автоматически**, без явного указания имени:

```python
class Cat(Animal):
    def __init__(self, name, age, eyecolor, breed):
        super().__init__(name, age, eyecolor)  # Неявное обращение к родителю
        self.breed = breed

cat = Cat('Кемаль', 1, 'синий', 'манчкин')
print(cat.name, cat.breed)  # Кемаль манчкин
```

> [!tip] Отличие от явного вызова
> При `super().__init__(...)` не нужно передавать `self` первым аргументом — `super()` делает это автоматически.

---

## 📖 Как работает super()

`super()` возвращает **прокси-объект** — посредника, который:
- знает текущий класс (`Cat`)
- знает текущий экземпляр (`self`)
- при обращении к методу ищет его в **родительском классе и выше по иерархии**

```python
# Эти две строки равнозначны:
Animal.__init__(self, name, age)
super().__init__(name, age)
```

---

## 📖 Явная форма super()

Можно передать класс и экземпляр явно — тогда поиск начинается от **родителя указанного класса**:

```python
super(Cat, self).__init__(name, age)
# Равнозначно super().__init__(name, age) внутри класса Cat
```

Это полезно когда нужно явно управлять точкой начала поиска в иерархии:

```python
class A:
    def method(self): return 'A'

class B(A):
    def method(self): return 'B'

class C(B):
    def method(self): return 'C'

class D(C):
    def method(self):
        print(super().method())           # C — ищет от родителя D
        print(super(D, self).method())    # C — то же самое
        print(super(C, self).method())    # B — ищет от родителя C
        print(super(B, self).method())    # A — ищет от родителя B
        # super(A, self).method()         # AttributeError — выше только object

d = D()
d.method()
```

> [!warning] Важно
> `super(ClassName, self)` — поиск начинается **от родителя ClassName**, не от самого ClassName.

---

## 📖 super() и многоуровневое наследование

`super()` ищет метод **по всей иерархии**, пропуская классы без нужного метода:

```python
class Animal:
    def __init__(self, name):
        print('Инициализация Animal')
        self.name = name

class Cat(Animal):
    pass  # Нет __init__

class Kitten(Cat):
    def __init__(self, name, breed):
        print('Инициализация Kitten')
        super().__init__(name)  # Ищет в Cat (нет) → находит в Animal
        self.breed = breed

kitten = Kitten('Кемаль', 'манчкин')
# Инициализация Kitten
# Инициализация Animal
print(kitten.name, kitten.breed)  # Кемаль манчкин
```

---

## 📖 Расширение vs полная замена метода

**Расширение** — добавить логику к родительскому методу, сохранив его:

```python
class Cat(Animal):
    def sleep(self):
        base = super().sleep()          # Берём результат родителя
        return base + ' (очень крепко)'

cat = Cat('Кемаль', 1)
print(cat.sleep())  # Кемаль спит zZ (очень крепко)
```

**Полная замена** — игнорировать родительский метод:

```python
class Cat(Animal):
    def sleep(self):
        return f'{self.name} мяукает и спит!'  # super() не используется
```

> [!tip] Правило выбора
> Если новый метод использует логику родителя → `super()`. Если полностью другой → не используй.

---

## 📖 super() для доступа к атрибутам класса

`super()` даёт доступ не только к методам, но и к атрибутам родителя:

```python
class Parent:
    attr = 1

class Child(Parent):
    attr = 2

    def get_parent_attr(self):
        return super().attr  # Атрибут родителя, не свой

obj = Child()
print(obj.attr)                # 2 — собственный
print(obj.get_parent_attr())   # 1 — родительский
```

---

## 📖 Паттерн: метод базового класса вызывает метод наследника

Родительский класс может определить метод, который вызывает метод, **реализованный в наследниках**:

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def info(self):
        return f'Имя: {self.name}, звук: {self.sound()}'  # sound() нет в Animal!

class Cat(Animal):
    def sound(self):
        return 'мяу'

class Dog(Animal):
    def sound(self):
        return 'гав'

print(Cat('Кемаль').info())  # Имя: Кемаль, звук: мяу
print(Dog('Роджер').info())  # Имя: Роджер, звук: гав
```

**Механизм:** `self.sound()` — Python ищет `sound()` в классе **текущего объекта** (Cat или Dog), а не в Animal.

> [!warning] Подводный камень
> Этот паттерн опасен — вызов `info()` на экземпляре самого `Animal` упадёт:
> ```python
> Animal('Эдди').info()  # AttributeError: 'Animal' has no attribute 'sound'
> ```
> Правильное решение — сделать `sound()` абстрактным методом. Подробнее: [[Абстрактные классы]].

---

## 📊 Quick Reference

| Форма | Что делает |
|---|---|
| `super().__init__(args)` | Вызов `__init__` родителя, стандартный способ |
| `super().method()` | Вызов любого метода родителя |
| `super().attr` | Доступ к атрибуту родителя |
| `super(ClassName, self).method()` | Поиск от родителя `ClassName` вверх |
| `ParentClass.__init__(self, args)` | Явный вызов — хрупко, не рекомендуется |

---

## 🔗 Связанные темы

- [[01 — 🧬 Введение в наследование]]
- [[03 — 🏗 Наследование от неизменяемых типов]]
- [[04 — 📦 Наследование от dict и list]]
- [[Наследование]] — общий справочник
