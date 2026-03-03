---
tags:
  - python
  - тема/ооп
  - статус/готово
---

# ➕➖ Унарные операторы и функции

## 📌 Коротко

> Унарные операторы (`+`, `-`, `~`) и математические функции (`abs()`, `round()`, `floor()` и др.) реализуются через соответствующие магические методы. Унарные операторы возвращают **новые объекты**, а не изменяют исходный.

---

## 📖 Теория

### Унарные операторы

Унарные операторы работают с **одним** операндом:

```python
num = 10

print(+num)   # → 10
print(-num)   # → -10
print(~num)   # → -11
```

| Метод | Оператор | Описание |
|---|---|---|
| `__pos__(self)` | `+obj` | Унарный плюс |
| `__neg__(self)` | `-obj` | Унарный минус |
| `__invert__(self)` | `~obj` | Инвертирование (побитовое НЕ) |

---

### Реализация унарных операторов

```python
class Angle:
    def __init__(self, value):
        self.value = value

    def __repr__(self):
        return f'Angle({self.value})'

    def __pos__(self):
        return Angle(self.value)             # возвращает копию

    def __neg__(self):
        return Angle(-self.value)            # угол со знаком минус

    def __invert__(self):
        if 0 <= self.value <= 180:
            return Angle(180 - self.value)   # смежный угол
        return Angle(180 + self.value)       # диаметрально противоположный


angle = Angle(100)

print(+angle)    # → Angle(100)
print(-angle)    # → Angle(-100)
print(~angle)    # → Angle(80)
```

> ⚠️ Унарные операторы возвращают **новые объекты** того же класса, а не изменяют исходный.

---

### Математические функции

| Метод | Функция | Описание |
|---|---|---|
| `__abs__(self)` | `abs(obj)` | Абсолютное значение |
| `__round__(self, n=None)` | `round(obj, n)` | Округление; `n` — знаков после запятой |
| `__trunc__(self)` | `math.trunc(obj)` | Усечение до целого (к нулю) |
| `__floor__(self)` | `math.floor(obj)` | Округление вниз |
| `__ceil__(self)` | `math.ceil(obj)` | Округление вверх |

```python
import math

class Angle:
    def __init__(self, value):
        self.value = value

    def __repr__(self):
        return f'Angle({self.value})'

    def __abs__(self):
        return Angle(abs(self.value))

    def __round__(self, n=None):
        if n is None:
            return Angle(round(self.value))
        return Angle(round(self.value, n))

    def __trunc__(self):
        return Angle(math.trunc(self.value))

    def __floor__(self):
        return Angle(math.floor(self.value))

    def __ceil__(self):
        return Angle(math.ceil(self.value))


angle = Angle(-101.54)

print(abs(angle))          # → Angle(101.54)
print(round(angle))        # → Angle(-102)
print(round(angle, 1))     # → Angle(-101.5)
print(math.trunc(angle))   # → Angle(-101)
print(math.floor(angle))   # → Angle(-102)
print(math.ceil(angle))    # → Angle(-101)
```

---

## 📝 Примечания

### Оператор `~` для встроенных типов

Из встроенных типов `~` определён только для `int` и `bool`. Формула: `~x == -(x + 1)`:

```python
print(~0)      # → -1
print(~1)      # → -2
print(~9)      # → -10
print(~-10)    # → 9
print(~True)   # → -2  (равнозначно ~1)
print(~False)  # → -1  (равнозначно ~0)
```

> ⚠️ В Python 3.12 использование `~` с типом `bool` **устарело** и будет удалено в Python 3.16.

### `trunc()` vs `floor()` vs `ceil()` — разница

```python
import math

x = -101.54

print(math.trunc(x))   # → -101  (к нулю)
print(math.floor(x))   # → -102  (вниз)
print(math.ceil(x))    # → -101  (вверх)
```

> 💡 Для отрицательных чисел `trunc()` и `floor()` дают **разные** результаты: `trunc` всегда идёт к нулю, `floor` — всегда вниз.

---

## 💻 Быстрая шпаргалка

```python
import math
from functools import total_ordering

class MyNumber:
    def __init__(self, value):
        self.value = value

    def __repr__(self):
        return f'MyNumber({self.value})'

    # Унарные операторы
    def __pos__(self):      return MyNumber(+self.value)
    def __neg__(self):      return MyNumber(-self.value)
    def __invert__(self):   return MyNumber(~self.value)

    # Математические функции
    def __abs__(self):              return MyNumber(abs(self.value))
    def __round__(self, n=None):    return MyNumber(round(self.value, n))
    def __trunc__(self):            return MyNumber(math.trunc(self.value))
    def __floor__(self):            return MyNumber(math.floor(self.value))
    def __ceil__(self):             return MyNumber(math.ceil(self.value))
```

---

## 🔗 Связанные темы

- [[09 — ⚖️ Сравнение объектов]]
- [[00 — 🪄 Магические методы — шпаргалка]]

## ❓ Вопросы / Непонятное

- ...
