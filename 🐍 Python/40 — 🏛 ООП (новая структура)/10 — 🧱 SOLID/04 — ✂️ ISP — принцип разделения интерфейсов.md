---
tags: [python, ооп, solid, isp, принципы, абстрактные_классы]
difficulty: intermediate
---

# ✂️ ISP — принцип разделения интерфейсов

> Модули не должны зависеть от интерфейсов, которые они не используют. Класс должен зависеть только от тех интерфейсов, которые он может полностью реализовать, — лучше несколько маленьких, чем один «толстый».

## Содержание

- [[#Справка|Справка]]
- [[#🧠 Суть принципа|Суть принципа]]
- [[#🐾 Пример 1 — Animal|Пример 1 — Animal]]
- [[#🖨 Пример 2 — Printer|Пример 2 — Printer]]
- [[#🐍 Протоколы как лёгкий способ|Протоколы как лёгкий способ]]
- [[#🚩 Как заметить нарушение|Как заметить нарушение]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]
- [[#🔗 Связанные темы|Связанные темы]]

---

## Справка

| Нарушение | Исправление |
| --- | --- |
| `Animal` с `make_sound()`, `fly()`, `swim()` | `Animal`, `FlyAnimal`, `SwimAnimal` |
| `Printer` с `print()`, `copy()`, `scan()` | `Printer`, `Copier`, `Scanner` |
| класс реализует часть методов, остальные — `raise NotImplementedError` | класс наследует только нужные интерфейсы |

| Принцип | Чем похож на ISP |
| --- | --- |
| SRP | разделение интерфейсов распределяет ответственность между ними |
| LSP | малые интерфейсы упрощают контракты и снижают вероятность нарушения подстановки |

---

## 🧠 Суть принципа

Дочерний класс не должен получать функциональность, которая ему не нужна. Если в интерфейсе много методов, каждый класс вынужден реализовывать их все — пусть даже заглушкой.

```
один толстый интерфейс                 несколько узких
┌──────────────────────┐              ┌──────────┐ ┌─────────┐ ┌─────────┐
│ make_sound, fly, swim│     ──►      │make_sound│ │  fly    │ │  swim   │
└──────────────────────┘              └──────────┘ └─────────┘ └─────────┘
 Cat обязан реализовать fly            Cat берёт только make_sound
```

Принцип помогает строить интерфейсы так, чтобы их изменения влияли только на связанные модули. Для этого обычно разрабатывают более мелкие интерфейсы.

---

## 🐾 Пример 1 — Animal

**Нарушение.** Один интерфейс описывает и звук, и полёт:

```python
from abc import ABC, abstractmethod


class Animal(ABC):
    @abstractmethod
    def make_sound(self):
        pass

    @abstractmethod
    def fly(self):
        pass


class Cat(Animal):
    def make_sound(self):
        print('Мяу')

    def fly(self):
        raise NotImplementedError        # заглушка: кошки не летают


class Bird(Animal):
    def make_sound(self):
        print('Пение птицы')

    def fly(self):
        print('Лечу')
```

`Bird` реализует интерфейс полностью, а `Cat` — только частично. Из-за метода `fly()` система становится хрупкой: появляется жёсткая зависимость от интерфейса, который класс не использует.

Добавим рыбу и метод `swim()` — и заглушек станет больше:

```python
class Animal(ABC):
    @abstractmethod
    def make_sound(self): pass

    @abstractmethod
    def fly(self): pass

    @abstractmethod
    def swim(self): pass


class Fish(Animal):
    def make_sound(self):
        raise NotImplementedError        # рыбы не издают звуков

    def fly(self):
        raise NotImplementedError        # и не летают

    def swim(self):
        print('Плыву')
```

**Исправление.** Делим интерфейс на несколько и подключаем нужные через множественное наследование:

```python
from abc import ABC, abstractmethod


class Animal(ABC):
    @abstractmethod
    def make_sound(self):
        pass


class FlyAnimal(ABC):
    @abstractmethod
    def fly(self):
        pass


class SwimAnimal(ABC):
    @abstractmethod
    def swim(self):
        pass


class Cat(Animal):
    def make_sound(self):
        print('Мяу')


class Bird(Animal, FlyAnimal):
    def make_sound(self):
        print('Пение птицы')

    def fly(self):
        print('Лечу')


class Fish(SwimAnimal):
    def swim(self):
        print('Плыву')
```

Теперь каждое животное реализует только те интерфейсы, которые ему действительно нужны.

---

## 🖨 Пример 2 — Printer

**Нарушение:**

```python
from abc import ABC, abstractmethod


class Printer(ABC):
    @abstractmethod
    def print(self, document): pass

    @abstractmethod
    def copy(self, document): pass

    @abstractmethod
    def scan(self, document): pass


class MatrixPrinter(Printer):
    def print(self, document):
        print(f'Печатаю {document}')

    def copy(self, document):
        raise NotImplementedError         # старый принтер не копирует

    def scan(self, document):
        raise NotImplementedError         # и не сканирует


class MFPPrinter(Printer):
    def print(self, document):
        print(f'Печатаю {document}')

    def copy(self, document):
        print(f'Копирую {document}')

    def scan(self, document):
        print(f'Сканирую {document}')
```

`MFPPrinter` реализует интерфейс полностью, а `MatrixPrinter` — частично. С новыми моделями проблема растёт: появляются новые методы, и зависимость от лишнего интерфейса усиливается.

**Исправление:**

```python
from abc import ABC, abstractmethod


class Printer(ABC):
    @abstractmethod
    def print(self, document):
        pass


class Copier(ABC):
    @abstractmethod
    def copy(self, document):
        pass


class Scanner(ABC):
    @abstractmethod
    def scan(self, document):
        pass


class MatrixPrinter(Printer):
    def print(self, document):
        print(f'Печатаю {document}')


class MFPPrinter(Printer, Copier, Scanner):
    def print(self, document):
        print(f'Печатаю {document}')

    def copy(self, document):
        print(f'Копирую {document}')

    def scan(self, document):
        print(f'Сканирую {document}')
```

---

## 🐍 Протоколы как лёгкий способ

> 💡 Этого блока нет в исходных примерах — это дополнение: в Python узкие интерфейсы можно описывать через `typing.Protocol`, без явного наследования.

```python
from typing import Protocol


class Flyer(Protocol):
    def fly(self) -> None: ...


class Swimmer(Protocol):
    def swim(self) -> None: ...


def migrate(animal: Flyer) -> None:     # нужен только fly(), остальное неважно
    animal.fly()
```

Любой объект с методом `fly()` подойдёт под `Flyer`, даже если не наследуется от него явно. Подробнее — [[04 — 🔗 Протоколы, Sequence и Callable]].

---

## 🚩 Как заметить нарушение

| Признак в коде | Что это значит |
| --- | --- |
| `raise NotImplementedError` в реализованном методе | класс вынужден поддерживать то, что ему не нужно |
| пустые методы-заглушки (`pass`) только ради интерфейса | то же самое, только молча |
| каждый новый класс требует правки базового интерфейса | интерфейс слишком широкий |

---

## 💡 Практические замечания

- ISP можно сравнить с SRP: разделение интерфейсов распределяет ответственность между ними. Вместе они упрощают контракты между модулями — [[01 — 🎯 SRP — принцип единственной ответственности]]
- Узкие интерфейсы снижают риск нарушения LSP: наследнику не приходится «обманывать» контракт заглушками — [[03 — 🔄 LSP — принцип подстановки Лисков]]
- Несколько интерфейсов объединяют множественным наследованием — [[05 — 🔀 Множественное наследование и MRO]]

---

## ⚠️ Частые ошибки

**❌ Оставлять методы-заглушки с `NotImplementedError` как норму:**
```python
def fly(self):
    raise NotImplementedError     # ❌ сигнал, что интерфейс слишком широкий
```

**❌ Разделять интерфейсы чрезмерно.** Если два метода всегда реализуются вместе, раздельные интерфейсы только усложняют код.

**❌ Выносить в интерфейс всё «на всякий случай».** Интерфейс должен включать только то, что нужно всем реализациям.

**❌ Путать с SRP.** SRP — про причины для изменения класса, ISP — про то, что класс обязан уметь по контракту.

---

## ✅ Главные правила

✅ Модуль зависит только от тех интерфейсов, которые использует  
✅ Много узких интерфейсов лучше одного широкого  
✅ Нет заглушек `NotImplementedError` — каждый класс реализует всё, что обещает  
✅ Интерфейсы объединяются множественным наследованием  
✅ ISP + SRP снижают риск нарушения LSP  

---

## 🔗 Связанные темы

- [[00 — 🧱 SOLID — обзор]]
- [[01 — 🎯 SRP — принцип единственной ответственности]]
- [[03 — 🔄 LSP — принцип подстановки Лисков]]
- [[05 — 🔌 DIP — принцип инверсии зависимостей]]
- [[01 — 🔒 Абстрактные классы (abc)]]
- [[05 — 🔀 Множественное наследование и MRO]]
- [[04 — 🔗 Протоколы, Sequence и Callable]]

---

#python/ооп #solid #isp #разделение_интерфейсов
