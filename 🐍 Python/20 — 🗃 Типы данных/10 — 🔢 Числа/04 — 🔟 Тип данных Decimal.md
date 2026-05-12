---
tags:
  - python
  - тема/типы_данных
  - тема/числа
  - статус/завершён
уровень: средний
изменяемый: false
хешируемый: true
dict_key: true
итерируемый: false
индексируемый: false
---

# 🔟 Тип данных Decimal

## 📌 Коротко

> `Decimal` — число с плавающей точкой с **основанием 10** и настраиваемой точностью. Нужен когда точность важнее скорости: финансы, бизнес, научные расчёты. По умолчанию 28 значащих цифр.

---

## 📖 Теория

### Почему float не подходит для точных расчётов

`float` реализован по стандарту **IEEE-754** как 64-битное двоичное число (основание 2). Это значит что многие десятичные дроби хранятся с погрешностью — как бесконечная двоичная дробь, обрезанная до 64 бит.

```python
# Сюрприз №1
print(0.1 + 0.1 + 0.1)         # → 0.30000000000000004
print(0.1 + 0.1 + 0.1 == 0.3)  # → False !

# Сюрприз №2
print(1.2 - 1.0)                # → 0.19999999999999996
print(0.3 - 0.2)                # → 0.09999999999999998

# Накопление ошибок в цикле
total = 0.0
for _ in range(10):
    total += 0.1
print(total)                    # → 0.9999999999999999  (не 1.0!)
```

`Decimal` хранит числа в **десятичной** системе, поэтому `0.1` представляется точно.

### Ключевые особенности Decimal

- ✅ Точные десятичные вычисления
- ✅ Настраиваемая точность (по умолчанию 28 знаков)
- ✅ Настраиваемые режимы округления
- ✅ Неизменяемый тип (как int, float)
- ⚠️ Медленнее float (реализован программно, не аппаратно)
- ⚠️ Нельзя смешивать с float в арифметике

---

## 💻 Примеры кода

### Создание Decimal

```python
from decimal import Decimal

# ✅ Из int — безопасно
Decimal(1)                      # → Decimal('1')
Decimal(567)                    # → Decimal('567')
Decimal(-93)                    # → Decimal('-93')

# ✅ Из строки — рекомендуется для дробей
Decimal('52.198')               # → Decimal('52.198')
Decimal('0.1')                  # → Decimal('0.1') — точно!
Decimal('-3.14')                # → Decimal('-3.14')

# ✅ Псевдоним для краткости
from decimal import Decimal as D
D('1.5') + D('2.3')            # → Decimal('3.8')

# ❌ Из float — НЕ рекомендуется
Decimal(0.1)
# → Decimal('0.1000000000000000055511151231257827021181583404541015625')
# Float уже неточный, Decimal копирует эту неточность!
```

> [!warning] Всегда создавай `Decimal` из **строки** или **int**, никогда из `float`!

### Арифметика

```python
from decimal import Decimal

a = Decimal('5.2')
b = Decimal('2.3')

print(a + b)    # → 7.5
print(a - b)    # → 2.9
print(a * b)    # → 11.96
print(a / b)    # → 2.260869565217391304347826087
print(a // b)   # → 2
print(a ** b)   # → 44.34122533787992500412791298

# Можно миксовать с int — нельзя с float
Decimal('5.2') + 1      # ✅ → Decimal('6.2')
Decimal('5.2') * 2      # ✅ → Decimal('10.4')
Decimal('5.2') + 1.0    # ❌ TypeError!

# Главный профит
d = Decimal('0.1') + Decimal('0.1') + Decimal('0.1')
print(d == Decimal('0.3'))    # → True ✅
```


### Контекст — настройка точности и поведения

```python
from decimal import Decimal, getcontext

# Посмотреть текущий контекст
print(getcontext())
# Context(prec=28, rounding=ROUND_HALF_EVEN, Emin=-999999, Emax=999999,
#         capitals=1, clamp=0, flags=[], traps=[InvalidOperation, DivisionByZero, Overflow])

# Изменить точность (количество значащих цифр)
getcontext().prec = 4

num = Decimal('3.1415926535')
print(num)          # → 3.1415926535  (точность при создании не влияет)
print(num * 1)      # → 3.142          (точность применяется при операции!)
print(num * 2)      # → 6.283
print(num / 2)      # → 1.571
```

> [!important] `prec` — это **общее количество значащих цифр**, не знаков после запятой!
> - `prec=4` для `3.1415` → `3.142` (4 цифры)
> - `prec=4` для `31.415` → `31.42` (4 цифры)
> - `prec=4` для `314.15` → `314.2` (4 цифры)

### Округление — метод quantize()

```python
from decimal import Decimal, getcontext, ROUND_HALF_UP, ROUND_CEILING, ROUND_FLOOR

getcontext().prec = 10

num = Decimal('3.1415926535')

# Округление до N знаков после запятой
print(num.quantize(Decimal('1.000')))   # → 3.142  (3 знака)
print(num.quantize(Decimal('1.00')))    # → 3.14   (2 знака)
print(num.quantize(Decimal('1.0')))     # → 3.1    (1 знак)
print(num.quantize(Decimal('1.')))      # → 3      (целое)

# Стратегии округления
n = Decimal('3.456')
print(n.quantize(Decimal('1.00'), ROUND_HALF_UP))   # → 3.46 (банковское)
print(n.quantize(Decimal('1.00'), ROUND_CEILING))   # → 3.46 (вверх к +∞)
print(n.quantize(Decimal('1.00'), ROUND_FLOOR))     # → 3.45 (вниз к -∞)

# Практика: деньги с округлением
from decimal import ROUND_HALF_UP
price = Decimal('19.994')
print(price.quantize(Decimal('0.01'), ROUND_HALF_UP))  # → 19.99
price2 = Decimal('19.995')
print(price2.quantize(Decimal('0.01'), ROUND_HALF_UP)) # → 20.00
```

**Все стратегии округления:**

| Константа | Описание |
|---|---|
| `ROUND_HALF_EVEN` | К ближайшему чётному (по умолчанию) |
| `ROUND_HALF_UP` | К ближайшему, при .5 — вверх |
| `ROUND_HALF_DOWN` | К ближайшему, при .5 — вниз |
| `ROUND_CEILING` | Всегда вверх (к +∞) |
| `ROUND_FLOOR` | Всегда вниз (к −∞) |
| `ROUND_UP` | От нуля |
| `ROUND_DOWN` | К нулю |
| `ROUND_05UP` | От нуля если последняя цифра 0 или 5 |

### Встроенные математические методы

```python
from decimal import Decimal, getcontext
getcontext().prec = 28

num = Decimal('10.0')

print(num.sqrt())     # → 3.162277660168379331998893544  (квадратный корень)
print(num.exp())      # → 22026.46579480671651695790065  (e^x)
print(num.ln())       # → 2.302585092994045684017991455  (натуральный логарифм)
print(num.log10())    # → 1                               (логарифм по основанию 10)
```

> [!note] Результат методов — тоже `Decimal`, сохраняется точность!
> В отличие от `math.sqrt()` который возвращает `float`.

### Метод as_tuple()

Разбирает число на компоненты:

```python
from decimal import Decimal

num = Decimal('-1.4568769017')
t = num.as_tuple()

print(t)
# DecimalTuple(sign=1, digits=(1, 4, 5, 6, 8, 7, 6, 9, 0, 1, 7), exponent=-10)

print(t.sign)       # → 1  (0 = положительное, 1 = отрицательное)
print(t.digits)     # → (1, 4, 5, 6, 8, 7, 6, 9, 0, 1, 7)
print(t.exponent)   # → -10  (= количество знаков после точки × -1)
```


### Сравнение Decimal чисел

```python
from decimal import Decimal

# Точное сравнение — работает корректно!
Decimal('0.1') * 3 == Decimal('0.3')    # → True ✅
Decimal('0.1') + Decimal('0.2') == Decimal('0.3')  # → True ✅

# Сравнение с int — работает
Decimal('5.0') == 5     # → True
Decimal('5.1') > 5      # → True

# Сортировка и min/max
from decimal import Decimal
nums = [Decimal('3.14'), Decimal('1.0'), Decimal('2.71')]
print(sorted(nums))     # → [Decimal('1.0'), Decimal('2.71'), Decimal('3.14')]
print(min(nums))        # → Decimal('1.0')
print(max(nums))        # → Decimal('3.14')
```

### Практические примеры

#### Финансовые расчёты

```python
from decimal import Decimal, ROUND_HALF_UP

CENT = Decimal('0.01')

def round_money(amount):
    """Округлить до копеек/центов"""
    return amount.quantize(CENT, ROUND_HALF_UP)

price = Decimal('19.99')
quantity = 3
total = price * quantity
print(total)                        # → 59.97

# Налог
tax_rate = Decimal('0.21')
tax = round_money(total * tax_rate)
print(tax)                          # → 12.59
print(round_money(total + tax))     # → 72.56

# Скидка 15%
discount = round_money(total * Decimal('0.15'))
print(discount)                     # → 8.99
print(round_money(total - discount)) # → 50.98
```

#### Сравнение float vs Decimal

```python
from decimal import Decimal

# float
print(0.1 + 0.2)                    # → 0.30000000000000004
print(0.1 + 0.2 == 0.3)             # → False

# Decimal
print(Decimal('0.1') + Decimal('0.2'))              # → 0.3
print(Decimal('0.1') + Decimal('0.2') == Decimal('0.3'))  # → True
```

### Частые ошибки

```python
# ❌ Создание из float
from decimal import Decimal
Decimal(0.1)   # → неточное значение!

# ✅ Создавай из строки
Decimal('0.1')  # → точное значение

# ❌ Смешивание с float
Decimal('5.2') + 1.5    # → TypeError!

# ✅ Конвертируй float через строку если нужно
Decimal(str(1.5))       # → Decimal('1.5')

# ❌ Слишком маленький prec для quantize
from decimal import getcontext
getcontext().prec = 2
Decimal('3.14').quantize(Decimal('1.00'))  # → InvalidOperation!
# prec должен быть >= количества знаков в результате

# ✅ Устанавливай достаточный prec
getcontext().prec = 10
```

### Когда использовать Decimal

**✅ Используй Decimal когда:**
- Финансы и деньги (цены, налоги, балансы)
- Бухгалтерские расчёты
- Нужна точность до последнего знака
- Нельзя допустить погрешности накопления

**❌ Не используй Decimal когда:**
- Физика, графика, игры, симуляции (используй `float`)
- Нужна скорость — Decimal в разы медленнее
- Достаточно приближённых вычислений

---

## 📊 Сравнение float vs Decimal

| Характеристика | `float` | `Decimal` |
|---|---|---|
| Реализация | Аппаратная (IEEE-754) | Программная |
| Размер | 64 бит | Неограничен |
| Основание | 2 (двоичное) | 10 (десятичное) |
| Скорость | ✅ Быстро | ❌ Медленно |
| Точность | ~15-17 знаков, фиксированная | До 28+ знаков, настраиваемая |
| Настройка | ❌ Нет | ✅ Контекст |
| Финансы | ❌ Не подходит | ✅ Идеально |
| Симуляции/графика | ✅ Отлично | ❌ Избыточно |

---

## 🔗 Связанные темы

- [[02 — 🔣 Числа с плавающей точкой (float)]]
- [[03 — 🔄 Округление чисел]]
- [[05 — ➗ Тип данных Fraction]]
