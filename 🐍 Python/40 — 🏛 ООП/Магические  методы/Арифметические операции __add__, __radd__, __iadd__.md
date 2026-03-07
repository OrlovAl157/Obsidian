---
tags:
  - python
  - тема/ооп
  - статус/готово
---

# ➕ Арифметические операции

## 📌 Коротко

> Арифметические операторы (`+`, `-`, `*`, `/` и др.) реализуются через магические методы. Основные методы (`__add__` и др.) вызываются у **левого** операнда. Отражённые (`__radd__` и др.) — у **правого**, если левый вернул `NotImplemented`. Методы составного присваивания (`__iadd__` и др.) изменяют объект **на месте** и возвращают `self`.

---

## 📖 Теория

---

### Основные арифметические методы

| Метод | Оператор | Описание |
|---|---|---|
| `__add__(self, other)` | `a + b` | Сложение |
| `__sub__(self, other)` | `a - b` | Вычитание |
| `__mul__(self, other)` | `a * b` | Умножение |
| `__truediv__(self, other)` | `a / b` | Деление |
| `__floordiv__(self, other)` | `a // b` | Целочисленное деление |
| `__mod__(self, other)` | `a % b` | Остаток от деления |
| `__pow__(self, other)` | `a ** b` | Возведение в степень |

Выражение `a + b` равносильно вызову `a.__add__(b)`:

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        if isinstance(other, int):
            return PiggyBank(self.coins + other)        # новый объект + int
        elif isinstance(other, PiggyBank):
            return PiggyBank(self.coins + other.coins)  # новый объект + PiggyBank
        return NotImplemented


bank1 = PiggyBank(10)
bank2 = bank1 + 5       # → PiggyBank(15)
bank3 = bank1 + bank2   # → PiggyBank(25)
```

> ⚠️ Арифметические методы всегда возвращают **новый объект**, а не изменяют исходный. Для изменения на месте используют `__iadd__` и др.

> 💡 После реализации `__add__()` можно использовать встроенную функцию `sum()` для суммирования экземпляров класса.

---

### Отражённые методы (reflected) — префикс `r`

Проблема: `bank + 5` работает, но `5 + bank` падает с `TypeError` — потому что `int.__add__(bank)` не знает что делать с `PiggyBank` и возвращает `NotImplemented`, а дальше Python не знает куда идти.

Решение — отражённые методы:

| Метод | Когда вызывается |
|---|---|
| `__radd__(self, other)` | `other + self`, если `other.__add__(self)` вернул `NotImplemented` |
| `__rsub__(self, other)` | `other - self` |
| `__rmul__(self, other)` | `other * self` |
| `__rtruediv__(self, other)` | `other / self` |
| `__rfloordiv__(self, other)` | `other // self` |
| `__rmod__(self, other)` | `other % self` |

#### Алгоритм выполнения `a + b`

```
a + b
  │
  ▼
a.__add__(b)
  │
  ├── True / False  ──────────────────► результат
  │
  └── NotImplemented
        │
        ▼
   тип(a) == тип(b)?
        │
        ├── ДА ──► TypeError сразу
        │          (бессмысленно спрашивать b, он такой же)
        │
        └── НЕТ ──► b.__radd__(a)
                      │
                      ├── True / False  ──► результат
                      │
                      └── NotImplemented ──► TypeError
```

> ⚠️ Этот механизм работает **только через оператор `+`**. При прямом вызове `a.__add__(b)` никакого fallback нет — сразу получаем `NotImplemented`.

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        if isinstance(other, int):
            return PiggyBank(self.coins + other)
        return NotImplemented

    def __radd__(self, other):
        return self.__add__(other)   # делегируем в __add__


bank = PiggyBank(10)

print(bank + 5)   # → PiggyBank(15)  — вызов bank.__add__(5)
print(5 + bank)   # → PiggyBank(15)  — вызов bank.__radd__(5)

# Прямой вызов — без fallback:
print((5).__add__(bank))   # → NotImplemented
```

> 💡 В `__radd__` обычно просто делегируют в `__add__`, если операция коммутативная (сложение, умножение). Для некоммутативных (вычитание, деление) нужно менять порядок: `return PiggyBank(other - self.coins)`.

---

### Методы составного присваивания — префикс `i` (inplace)

| Метод | Оператор | Описание |
|---|---|---|
| `__iadd__(self, other)` | `a += b` | Сложение на месте |
| `__isub__(self, other)` | `a -= b` | Вычитание на месте |
| `__imul__(self, other)` | `a *= b` | Умножение на месте |
| `__itruediv__(self, other)` | `a /= b` | Деление на месте |
| `__ifloordiv__(self, other)` | `a //= b` | Целочисленное деление на месте |
| `__imod__(self, other)` | `a %= b` | Остаток на месте |
| `__ipow__(self, other)` | `a **= b` | Степень на месте |

Ключевое отличие от `__add__`: метод **изменяет `self`** и возвращает `self`, а не новый объект:

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        return PiggyBank(self.coins + other)   # новый объект

    def __iadd__(self, other):
        self.coins += other
        return self                            # тот же объект!


bank = PiggyBank(10)
print(id(bank))   # → 140234...

bank += 10        # изменяет bank на месте
print(id(bank))   # → 140234...  (id тот же!)
print(bank)       # → PiggyBank(20)
```

#### `__add__` vs `__iadd__` — разница в id

```python
bank = PiggyBank(10)

# С __add__ (без __iadd__):
bank += 5    # Python вызывает __add__ → создаёт новый объект → bank = новый объект
             # id меняется!

# С __iadd__:
bank += 5    # Python вызывает __iadd__ → изменяет существующий → return self
             # id не меняется!
```

> 💡 Если `__iadd__` не определён, но определён `__add__` — оператор `+=` делегируется в `__add__`. Работает, но всегда создаёт новый объект.

> ⚠️ Для **неизменяемых** объектов `__iadd__` не имеет смысла — они по определению не могут меняться. `+=` у них всегда создаёт новый объект (как у `int`, `str`, `tuple`).

---

## 📝 Примечания

### Битовые операции тоже имеют свои методы

```python
__lshift__()   # <<     двоичный сдвиг влево
__rshift__()   # >>     двоичный сдвиг вправо
__and__()      # &      двоичное И
__or__()       # |      двоичное ИЛИ
__xor__()      # ^      двоичное XOR
```

Все они также имеют отражённые (`__rlshift__` и др.) и inplace (`__ilshift__` и др.) версии.

---

## 💻 Быстрая шпаргалка

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    # Основной — создаёт новый объект
    def __add__(self, other):
        if isinstance(other, int):
            return PiggyBank(self.coins + other)
        elif isinstance(other, PiggyBank):
            return PiggyBank(self.coins + other.coins)
        return NotImplemented

    # Отражённый — для случая: 5 + bank
    def __radd__(self, other):
        return self.__add__(other)

    # Inplace — изменяет на месте
    def __iadd__(self, other):
        if isinstance(other, int):
            self.coins += other
            return self
        elif isinstance(other, PiggyBank):
            self.coins += other.coins
            return self
        return NotImplemented


bank1 = PiggyBank(10)
bank2 = PiggyBank(5)

print(bank1 + 3)      # → PiggyBank(13)  — __add__
print(3 + bank1)      # → PiggyBank(13)  — __radd__
print(bank1 + bank2)  # → PiggyBank(15)  — __add__

bank1 += 10           # → __iadd__, bank1 тот же объект
print(bank1)          # → PiggyBank(20)

print(sum([PiggyBank(10), PiggyBank(20), PiggyBank(30)]))  # → PiggyBank(60)
```

---

## 🔗 Связанные темы

- [[Магические методы __eq__ и __ne__]]
- [[Унарные операторы и функции __pos__, __neg__]]
- [[00 Магические методы]]

## ❓ Вопросы / Непонятное

- ...
