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

Многие встроенные типы поддерживают арифметику через операторы `+`, `-`, `*`, `/`, `//`, `%`. Чтобы добавить такую же поддержку в свой класс — определяем соответствующие магические методы:

| Метод | Оператор | Описание |
|---|---|---|
| `__add__(self, other)` | `a + b` | Сложение |
| `__sub__(self, other)` | `a - b` | Вычитание |
| `__mul__(self, other)` | `a * b` | Умножение |
| `__truediv__(self, other)` | `a / b` | Деление |
| `__floordiv__(self, other)` | `a // b` | Целочисленное деление |
| `__mod__(self, other)` | `a % b` | Остаток от деления |
| `__pow__(self, other)` | `a ** b` | Возведение в степень |
| `__divmod__(self, other)` | `divmod(a, b)` | Частное и остаток |

Выражение `a + b` равносильно вызову `a.__add__(b)`.

#### Базовый пример

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        return PiggyBank(self.coins + other)   # создаём и возвращаем НОВЫЙ объект


bank1 = PiggyBank(10)
bank2 = bank1 + 5    # → вызов bank1.__add__(5)
bank3 = bank1 + 15

print(bank1)   # → PiggyBank(10)  — исходный не изменился!
print(bank2)   # → PiggyBank(15)
print(bank3)   # → PiggyBank(25)
```

> ⚠️ Арифметические методы всегда возвращают **новый объект**, а не изменяют исходный. Для изменения на месте используют `__iadd__` и др. (см. ниже).

#### Поддержка нескольких типов

Можно гибко определить поведение для разных типов `other`:

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        if isinstance(other, int):
            return PiggyBank(self.coins + other)         # PiggyBank + int
        elif isinstance(other, PiggyBank):
            return PiggyBank(self.coins + other.coins)   # PiggyBank + PiggyBank
        return NotImplemented                            # неизвестный тип


bank1 = PiggyBank(10)
bank2 = bank1 + 5         # → PiggyBank(15)
bank3 = bank1 + bank2     # → PiggyBank(25)
```

> 💡 После реализации `__add__()` можно использовать встроенную функцию `sum()`:
> ```python
> banks = [PiggyBank(10), PiggyBank(20), PiggyBank(30)]
> print(sum(banks))   # → PiggyBank(60)
> ```

---

### Отражённые методы — префикс `r` (reflected)

#### Проблема порядка операндов

При выполнении `a + b` Python вызывает `a.__add__(b)`. Порядок операндов важен:

```python
bank = PiggyBank(10)

print(bank + 5)   # ✅ → bank.__add__(5) → PiggyBank(15)
print(5 + bank)   # ❌ → (5).__add__(bank) → int не знает про PiggyBank → TypeError
```

#### Решение — отражённые методы

Отражённые методы вызываются у **правого** операнда, когда левый вернул `NotImplemented`:

| Метод | Когда вызывается |
|---|---|
| `__radd__(self, other)` | `other + self` |
| `__rsub__(self, other)` | `other - self` |
| `__rmul__(self, other)` | `other * self` |
| `__rtruediv__(self, other)` | `other / self` |
| `__rfloordiv__(self, other)` | `other // self` |
| `__rmod__(self, other)` | `other % self` |
| `__rpow__(self, other)` | `other ** self` |

#### Алгоритм выполнения `a + b`

```
a + b
  │
  ▼
a.__add__(b)
  │
  ├── результат  ────────────────────────────► готово
  │
  └── NotImplemented
        │
        ▼
   тип(a) == тип(b)?
        │
        ├── ДА ──────────────────────────────► TypeError сразу
        │          (бессмысленно спрашивать b, он такой же тип)
        │
        └── НЕТ
              │
              ▼
         b.__radd__(a)
              │
              ├── результат  ────────────────► готово
              │
              └── NotImplemented ────────────► TypeError
```

> ⚠️ Этот механизм работает **только через оператор `+`**. При прямом вызове `a.__add__(b)` никакого fallback нет:
> ```python
> num = 5
> print(num.__add__(bank))   # → NotImplemented  (прямой вызов, без fallback)
> print(5 + bank)            # → PiggyBank(15)   (через оператор — fallback работает)
> ```

#### Реализация `__radd__`

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
        return self.__add__(other)   # делегируем в __add__, т.к. сложение коммутативно


bank = PiggyBank(10)

print(bank + 5)   # → PiggyBank(15)  — вызов __add__
print(5 + bank)   # → PiggyBank(15)  — вызов __radd__ → делегирует в __add__
```

#### Некоммутативность — порядок важен

Для сложения/умножения `__radd__` просто делегирует в `__add__` — порядок не важен. Но для вычитания/деления порядок **критичен**: в `__rsub__` вызов происходит когда `self` стоит **справа** (`other - self`):

```python
def __sub__(self, other):
    if isinstance(other, int):
        return PiggyBank(self.coins - other)   # bank - 5:  self - other

def __rsub__(self, other):
    if isinstance(other, int):
        return PiggyBank(other - self.coins)   # 5 - bank:  other - self  (НЕ self - other!)

bank = PiggyBank(10)

print(bank - 3)    # → PiggyBank(7)   — __sub__:  10 - 3
print(15 - bank)   # → PiggyBank(5)   — __rsub__: 15 - 10
```

> ⚠️ Частая ошибка — написать `return self.__sub__(other)` в `__rsub__` по аналогии со сложением. Вместо `15 - 10 = 5` получим `10 - 15 = -5`.

---

### Методы составного присваивания — префикс `i` (inplace)

Операторы `+=`, `-=`, `*=` и т.д. для **изменяемых** объектов меняют объект **на месте**, не создавая новый.

| Метод | Оператор | Описание |
|---|---|---|
| `__iadd__(self, other)` | `a += b` | Сложение на месте |
| `__isub__(self, other)` | `a -= b` | Вычитание на месте |
| `__imul__(self, other)` | `a *= b` | Умножение на месте |
| `__itruediv__(self, other)` | `a /= b` | Деление на месте |
| `__ifloordiv__(self, other)` | `a //= b` | Целочисленное деление на месте |
| `__imod__(self, other)` | `a %= b` | Остаток на месте |
| `__ipow__(self, other)` | `a **= b` | Степень на месте |

> 💡 Префикс `i` — сокращение от **inplace** (на месте).

#### Ключевое отличие от `__add__`

`__iadd__` изменяет `self` и возвращает `self` (тот же объект), а не новый:

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    def __add__(self, other):
        return PiggyBank(self.coins + other)   # НОВЫЙ объект

    def __iadd__(self, other):
        self.coins += other
        return self                            # ТОТ ЖЕ объект!


bank = PiggyBank(10)
print(id(bank))    # → 140234...

bank += 10
bank += 5

print(id(bank))    # → 140234...  (id не изменился!)
print(bank)        # → PiggyBank(25)
```

#### Зачем `__add__` если есть `__iadd__`?

Они решают **разные задачи** и часто нужны оба:

- `bank + 5` → всегда вызывает `__add__` → нужен новый объект
- `bank += 5` → сначала ищет `__iadd__`, потом `__add__`

```
bank += 5
  │
  ▼
__iadd__ определён?
  │
  ├── ДА ──► изменяет self → return self  (тот же объект)
  │
  └── НЕТ ──► __add__ определён?
                │
                ├── ДА ──► создаёт новый объект
                │
                └── НЕТ ──► TypeError
```

#### Цикличность `id` при `+=` без `__iadd__`

Если `__iadd__` не определён — каждый `+=` создаёт новый объект. В IDE может казаться что `id` одинаковый — на самом деле он **чередуется**:

```python
bank += 10
print(id(bank))   # → 2562652163280
bank += 5
print(id(bank))   # → 2562652164752
bank += 5
print(id(bank))   # → 2562652163280  ← тот же что первый!
bank += 5
print(id(bank))   # → 2562652164752  ← чередуются!
```

**Почему:** сборщик мусора освобождает память старого объекта, и Python переиспользует освободившийся адрес для нового. При определённом `__iadd__` — `id` всегда одинаковый.

#### `__add__` vs `__iadd__` — сравнение по `id`

```python
# Только __add__ (без __iadd__):
bank = PiggyBank(10)
print(id(bank))   # → 111111
bank += 5         # вызывает __add__ → создаёт новый объект
print(id(bank))   # → 222222  ← id изменился!

# С __iadd__:
bank = PiggyBank(10)
print(id(bank))   # → 111111
bank += 5         # вызывает __iadd__ → изменяет self → return self
print(id(bank))   # → 111111  ← id тот же
```

> 💡 Если `__iadd__` не определён, но определён `__add__` — `+=` автоматически делегируется в `__add__`. Работает, но всегда создаёт новый объект.

> ⚠️ Для **неизменяемых** объектов `__iadd__` не имеет смысла — `+=` у них всегда создаёт новый объект, как у `int`, `str`, `tuple`.

#### Разница между `list + list` и `list += list`

```python
# Обычное сложение — новый объект:
nums = [1, 2, 3]
print(id(nums))       # → 111111
nums = nums + [4, 5]
print(id(nums))       # → 222222  ← новый объект

# Составное присваивание — тот же объект:
nums = [1, 2, 3]
print(id(nums))       # → 111111
nums += [4, 5]
print(id(nums))       # → 111111  ← тот же объект (list изменяемый)
```

---

## 📝 Примечания

### Битовые операции тоже имеют магические методы

```python
__lshift__(self, other)    # <<    двоичный сдвиг влево
__rshift__(self, other)    # >>    двоичный сдвиг вправо
__and__(self, other)       # &     двоичное И
__or__(self, other)        # |     двоичное ИЛИ
__xor__(self, other)       # ^     двоичное XOR
```

Все они также имеют отражённые (`__rlshift__` и др.) и inplace-версии (`__ilshift__` и др.).

### Полная таблица операторов составного присваивания

| Оператор | Метод |
|---|---|
| `+=` | `__iadd__()` |
| `-=` | `__isub__()` |
| `*=` | `__imul__()` |
| `/=` | `__itruediv__()` |
| `//=` | `__ifloordiv__()` |
| `%=` | `__imod__()` |
| `**=` | `__ipow__()` |
| `<<=` | `__ilshift__()` |
| `>>=` | `__irshift__()` |
| `&=` | `__iand__()` |
| `^=` | `__ixor__()` |
| `\|=` | `__ior__()` |

---

## 💻 Быстрая шпаргалка

```python
class PiggyBank:
    def __init__(self, coins):
        self.coins = coins

    def __repr__(self):
        return f'PiggyBank({self.coins})'

    # Основной — создаёт НОВЫЙ объект
    def __add__(self, other):
        if isinstance(other, int):
            return PiggyBank(self.coins + other)
        elif isinstance(other, PiggyBank):
            return PiggyBank(self.coins + other.coins)
        return NotImplemented

    # Отражённый — для случая: 5 + bank
    def __radd__(self, other):
        return self.__add__(other)   # коммутативно — делегируем

    # Отражённый некоммутативный — для случая: 5 - bank
    def __rsub__(self, other):
        if isinstance(other, int):
            return PiggyBank(other - self.coins)   # other - self, не self - other!
        return NotImplemented

    # Inplace — изменяет ТОТ ЖЕ объект
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
print(3 + bank1)      # → PiggyBank(13)  — __radd__ → __add__
print(15 - bank1)     # → PiggyBank(5)   — __rsub__: 15 - 10
bank1 += 10           # → __iadd__, тот же объект
print(bank1)          # → PiggyBank(20)

# sum() работает благодаря __add__ и __radd__:
print(sum([PiggyBank(10), PiggyBank(20), PiggyBank(30)]))   # → PiggyBank(60)
```

---

## 🔗 Связанные темы

- [[Магические методы __eq__ и __ne__]]
- [[Унарные операторы и функции __pos__, __neg__]]
- [[00 Магические методы]]

## ❓ Вопросы / Непонятное

- ...
