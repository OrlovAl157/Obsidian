---

tags:

- python
- ООП
- перечисления
- статус/завершён

---

# 🔢 Перечисления (Enum)

## 📌 Коротко

> Перечисление — набор символических имён, привязанных к уникальным постоянным значениям. Используется когда нужен фиксированный набор констант с говорящими именами.

---

## 📖 Зачем нужны перечисления

Без перечислений код с магическими числами нечитаем:

```python
# ❌ Без перечисления — что значит 1? Понедельник? Воскресенье?
if day == 1:
    do_something()

# ✅ С перечислением — сразу понятно
if day == Weekday.MONDAY:
    do_something()
```

Перечисления полезны для: дней недели, времён года, сторон света, HTTP-кодов, цветов светофора — любого фиксированного набора констант.

---

## 📖 Создание через подкласс Enum

```python
from enum import Enum

class Weekday(Enum):
    MONDAY = 1
    TUESDAY = 2
    WEDNESDAY = 3
    THURSDAY = 4
    FRIDAY = 5
    SATURDAY = 6
    SUNDAY = 7
```

- `Weekday` — перечисление
- `Weekday.MONDAY` и др. — элементы перечисления
- Имена элементов рекомендуется писать в **верхнем регистре** — они константы
- Значения могут быть любого типа:

```python
class Size(Enum):
    S = 'small'
    M = 'medium'
    L = 'large'
    XL = 'extra large'
```

---

## 📖 Создание через функцию Enum()

Альтернативный способ — без синтаксиса класса:

```python
from enum import Enum

# Из списка имён — значения автоматически с 1
Weekday = Enum('Weekday', ['MONDAY', 'TUESDAY', 'WEDNESDAY'])

# Из строки с именами через пробел
Weekday = Enum('Weekday', 'MONDAY TUESDAY WEDNESDAY')

# С указанием начального значения
Weekday = Enum('Weekday', ['MONDAY', 'TUESDAY'], start=0)

# Из списка пар имя-значение
Size = Enum('Size', [('S', 'small'), ('M', 'medium'), ('L', 'large')])
```

---

## 📖 Атрибуты элементов — name и value

Каждый элемент перечисления — это **экземпляр класса** перечисления с двумя атрибутами:

```python
print(type(Weekday.MONDAY))       # <enum 'Weekday'>

print(Weekday.MONDAY.name)        # 'MONDAY'  — строковое имя
print(Weekday.MONDAY.value)       # 1         — значение

print(str(Weekday.MONDAY))        # 'Weekday.MONDAY'
print(repr(Weekday.MONDAY))       # '<Weekday.MONDAY: 1>'
```

> \[!tip\] Запомни `name` — всегда строка с именем атрибута (`'MONDAY'`) `value` — то что присвоили (`1`, `'small'` и т.д.)

---

## 📖 Три способа доступа к элементам

```python
Weekday.MONDAY      # точечная нотация — по имени
Weekday['MONDAY']   # через [] — по имени (вызывает __getitem__)
Weekday(1)          # через () — по значению

# Все три возвращают один и тот же объект:
print(Weekday.MONDAY is Weekday['MONDAY'] is Weekday(1))  # True
```

> \[!warning\] Важно `[]` — доступ по **имени**`()` — доступ по **значению**
>
> `Weekday('MONDAY')` → `ValueError` — 'MONDAY' это имя, а не значение!

> \[!tip\] Вызов через () — не создание экземпляра `Weekday(1)` не создаёт новый объект — а **ищет существующий** элемент по значению. Создание экземпляров класса перечисления напрямую невозможно.

---

## 📖 Итерирование

Перечисления — итерируемые объекты. Элементы располагаются в порядке определения:

```python
for day in Weekday:
    print(day)
# Weekday.MONDAY
# Weekday.TUESDAY
# ...

for day in Weekday:
    print(day.name, '->', day.value)
# MONDAY -> 1
# TUESDAY -> 2
# ...

print(list(Weekday))   # список всех элементов
print(len(Weekday))    # количество элементов
```

---

## 📖 Сравнение

```python
day = Weekday.MONDAY

# Сравнение на идентичность (рекомендуется — быстрее)
print(day is Weekday.MONDAY)      # True
print(day is Weekday.TUESDAY)     # False
print(day is not Weekday.SUNDAY)  # True

# Сравнение на равенство (делегирует в is)
print(day == Weekday.MONDAY)      # True
print(day != Weekday.TUESDAY)     # True

# Проверка принадлежности
print(Weekday.MONDAY in Weekday)  # True
```

> \[!tip\] Рекомендация Используй `is` и `is not` — они быстрее, так как каждый элемент является **синглтоном** (существует в единственном экземпляре).

---

## 📖 Почему Weekday.MONDAY == 1 → False

```python
print(Weekday.MONDAY == 1)   # False
print(Weekday.MONDAY == '1') # False
```

`==` в `Enum` делегирует вызов в `is` — сравнение на идентичность. `Weekday.MONDAY` это объект типа `Weekday`, а `1` — объект типа `int`. Это **разные объекты** в памяти → `False`.

Чтобы сравнить с числом — используй `.value`:

```python
print(Weekday.MONDAY.value == 1)  # True
```

---

## 📖 Псевдонимы — элементы с одинаковым значением

Если два элемента имеют одинаковое значение — второй становится **псевдонимом** первого:

```python
class Weekday(Enum):
    MONDAY = 1
    Monday = 1   # псевдоним MONDAY

print(Weekday.Monday)                    # Weekday.MONDAY — возвращает первый!
print(Weekday.Monday is Weekday.MONDAY)  # True — один и тот же объект
```

Псевдонимы **не появляются** при итерировании и не учитываются в `len()`:

```python
for day in Weekday:
    print(day.name)   # Monday не будет в выводе
```

---

## 📖 @unique — запрет дублей

```python
from enum import Enum, unique

@unique
class Weekday(Enum):
    MONDAY = 1
    Monday = 1   # ValueError: duplicate values found in Weekday: Monday -> MONDAY
```

---

## 📖 auto() — автоматические значения

```python
from enum import Enum, auto

class Weekday(Enum):
    MONDAY = auto()    # 1
    TUESDAY = auto()   # 2
    WEDNESDAY = auto() # 3
```

`auto()` берёт **предыдущее значение** и прибавляет 1 — неважно откуда оно пришло:

```python
class Color(Enum):
    RED = auto()     # 1
    GREEN = auto()   # 2
    BLUE = auto()    # 3
    YELLOW = 10      # явное присваивание
    PURPLE = auto()  # 11 — берёт предыдущее 10, не 3!
```

> \[!tip\] Удобный трюк для длинных перечислений
>
> ```python
> MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY = (auto() for _ in range(7))
> ```

---

## 📖 Сортировка

По умолчанию перечисления **не поддерживают** `>`, `<`, `>=`, `<=`. Для сортировки — ключевая функция:

```python
print(sorted(Weekday, key=lambda day: day.value))
# [MONDAY, TUESDAY, ..., SUNDAY]
```

---

## 📖 Пользовательские методы

```python
class Weekday(Enum):
    MONDAY = 1
    SUNDAY = 7

    def about(self):                        # метод экземпляра
        return self.name, self.value

    def __str__(self):                      # переопределение __str__
        return f'Today is {self.name}'      # по умолчанию: 'Weekday.MONDAY'

    @classmethod
    def favorite_day(cls):                  # метод класса
        return cls.SUNDAY


day = Weekday.favorite_day()
print(day)          # Today is SUNDAY  — переопределённый __str__
print(day.about())  # ('SUNDAY', 7)
```

> \[!tip\] Помни Элементы перечисления — это **экземпляры класса** перечисления. Поэтому `self` в методах — это сам элемент (`Weekday.SUNDAY`).

---

## 📖 IntEnum — элементы как int

Наследуется и от `Enum` и от `int` — элементы ведут себя как числа:

```python
from enum import IntEnum

class Weekday(IntEnum):
    MONDAY = 1
    TUESDAY = 2
    SUNDAY = 7

print(Weekday.MONDAY == 1)                    # True  — в отличие от Enum!
print(Weekday.MONDAY < Weekday.SUNDAY)        # True  — сравнение работает
print(Weekday.MONDAY + Weekday.TUESDAY)       # 3     — арифметика работает
print(type(Weekday.MONDAY + Weekday.TUESDAY)) # int   — результат это int, не Weekday!
```

> \[!warning\] Важно Результат арифметики — всегда `int`, даже если такое значение есть в перечислении. Чтобы получить элемент — конвертируй явно:
>
> ```python
> result = Weekday(Weekday.MONDAY + Weekday.TUESDAY)  # Weekday.WEDNESDAY
> ```

---

## 📖 Магические методы перечислений

```python
print(Weekday.SUNDAY in Weekday)    # True  — __contains__
print(Weekday['SUNDAY'])            # Weekday.SUNDAY — __getitem__
print(list(Weekday))                # все элементы — __iter__
print(len(Weekday))                 # количество — __len__
print(list(reversed(Weekday)))      # в обратном порядке — __reversed__
```

---

## 📖 Enum vs словарь vs namedtuple

`Enumdictnamedtuple`Защита от изменений✅❌✅Итерирование✅✅✅Защита от опечаток (IDE)✅❌✅Синглтон✅❌❌Сравнение через `is`✅❌❌Арифметикатолько IntEnum❌❌

---

## 📊 Quick Reference

```python
from enum import Enum, IntEnum, auto, unique

# Создание
class Color(Enum):
    RED = 1
    GREEN = auto()

# Доступ к элементу
Color.RED           # по имени через точку
Color['RED']        # по имени через []
Color(1)            # по значению через ()

# Атрибуты элемента
Color.RED.name      # 'RED'
Color.RED.value     # 1

# Сравнение
Color.RED is Color.RED      # True — рекомендуется
Color.RED == Color.RED      # True — делегирует в is
Color.RED == 1              # False — разные типы!
Color.RED.value == 1        # True — сравнение значений

# Итерирование
for c in Color: ...
list(Color)
len(Color)
list(reversed(Color))
```

---

## 🔗 Связанные темы

- [[01 — 🧬 Введение в наследование]]
- [01 — 🔒 Абстрактные классы (abc)](./01%20%E2%80%94%20%F0%9F%94%92%20%D0%90%D0%B1%D1%81%D1%82%D1%80%D0%B0%D0%BA%D1%82%D0%BD%D1%8B%D0%B5%20%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D1%8B%20\(abc\).md "mcp-wiki:%5B%5B01%20%E2%80%94%20%F0%9F%94%92%20%D0%90%D0%B1%D1%81%D1%82%D1%80%D0%B0%D0%BA%D1%82%D0%BD%D1%8B%D0%B5%20%D0%BA%D0%BB%D0%B0%D1%81%D1%81%D1%8B%20(abc)%5D%5D")
- [[Магические методы]]
