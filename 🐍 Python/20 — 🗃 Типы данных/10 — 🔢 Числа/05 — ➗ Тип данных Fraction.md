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

# ➗ Тип данных Fraction

## 📌 Коротко

> `Fraction` — рациональное число в виде точной дроби `числитель/знаменатель`. Всегда автоматически сокращается. Нужен когда важна абсолютная точность дробей без округлений.

---

## 📖 Теория

### Что такое рациональное число

Рациональное число — число вида `m/n`, где `m` и `n` целые, `n ≠ 0`.

`Fraction` хранит ровно это: два целых числа. Никакого IEEE-754, никаких двоичных погрешностей. `1/3` — это ровно `1/3`, не `0.3333333...`.

**Ключевые особенности:**
- ✅ Абсолютно точные дробные вычисления
- ✅ Автосокращение при создании (`6/4` → `3/2`)
- ✅ Можно смешивать с `int`
- ⚠️ Медленнее `float` и `int` (программная реализация)
- ⚠️ Нельзя смешивать с `Decimal` в арифметике
- ⚠️ Не рекомендуется создавать из `float`

---

## 💻 Примеры кода

### Создание Fraction

```python
from fractions import Fraction

# ✅ Из двух int (числитель, знаменатель)
Fraction(3, 4)          # → 3/4
Fraction(1, 3)          # → 1/3
Fraction(-5, 8)         # → -5/8

# Автосокращение!
Fraction(6, 4)          # → 3/2   (не 6/4)
Fraction(5, 10)         # → 1/2
Fraction(75, 100)       # → 3/4
Fraction(23, 23)        # → 1

# ✅ Из строки (десятичная запись)
Fraction('0.25')        # → 1/4
Fraction('0.55')        # → 11/20
Fraction('3.14')        # → 157/50

# ✅ Из строки (дробная запись)
Fraction('1/3')         # → 1/3
Fraction('3/8')         # → 3/8
Fraction('-5/16')       # → -5/16

# Python 3.12+: можно с пробелами
Fraction('1 / 9')       # → 1/9  (в Python < 3.12 это ValueError)

# Псевдоним для краткости
from fractions import Fraction as F
F(1, 3) + F(1, 6)       # → 1/2

# ❌ Из float — НЕ рекомендуется
Fraction(0.34)
# → 6124895493223875/18014398509481984  (погрешность float!)
# Ожидалось 17/50, но float 0.34 ≠ ровно 0.34
```

> [!warning] Создавай `Fraction` из строки или пары int, **не из float**!

### Арифметика

```python
from fractions import Fraction

a = Fraction(1, 3)   # 1/3
b = Fraction(1, 6)   # 1/6

print(a + b)    # → 1/2    (1/3 + 1/6 = 2/6 + 1/6 = 3/6 = 1/2)
print(a - b)    # → 1/6    (2/6 - 1/6 = 1/6)
print(a * b)    # → 1/18
print(a / b)    # → 2      (1/3 ÷ 1/6 = 1/3 × 6/1 = 2)

# Смешивание с int — ок
Fraction(3, 8) + 1      # → 11/8
Fraction(3, 8) * 2      # → 3/4
Fraction(3, 8) ** 4     # → 81/4096

# ❌ Возведение в дробную степень → float (не Fraction!)
Fraction(3, 8) ** Fraction(1, 2)    # → 0.6123724356957945 (float!)

# ❌ Смешивание с Decimal — TypeError
from decimal import Decimal
Decimal('1.5') + Fraction(1, 2)     # → TypeError!
```

### Свойства numerator и denominator

```python
from fractions import Fraction

num = Fraction(5, 16)

print(num.numerator)    # → 5   (числитель)
print(num.denominator)  # → 16  (знаменатель)

# Целые дроби
print(Fraction(5, 1).numerator)    # → 5
print(Fraction(5, 1).denominator)  # → 1

# as_integer_ratio() — кортеж (числитель, знаменатель) — Python 3.8+
Fraction(-5, 16).as_integer_ratio() # → (-5, 16)
```

### Сравнение

```python
from fractions import Fraction

Fraction(1, 2) == Fraction(15, 30)  # → True  (оба = 1/2)
Fraction(1, 2) != Fraction(1, 3)    # → True
Fraction(3, 5) > Fraction(1, 2)     # → True  (0.6 > 0.5)
Fraction(1, 3) < 1                  # → True  (сравнение с int)
Fraction(3, 4) == 0.75              # → True  (сравнение с float!)

# Точное сравнение — в отличие от float
Fraction(1, 10) * 3 == Fraction(3, 10)  # → True ✅
0.1 * 3 == 0.3                           # → False (float!)
```

### Метод limit_denominator() — фишка!

Возвращает ближайшую дробь с ограниченным знаменателем. Очень удобно для приближений иррациональных чисел:

```python
from fractions import Fraction
import math

pi_str = str(math.pi)           # '3.141592653589793'
pi = Fraction(pi_str)
print(pi)                       # → 3141592653589793/1000000000000000

# Ищем красивые приближения π
print(pi.limit_denominator(10))     # → 22/7    ≈ 3.14285...
print(pi.limit_denominator(100))    # → 311/99  ≈ 3.14141...
print(pi.limit_denominator(1000))   # → 355/113 ≈ 3.14159292...

# Приближение числа e
e = Fraction(str(math.e))
print(e.limit_denominator(100))     # → 87/32   ≈ 2.71875
print(e.limit_denominator(1000))    # → 878/323 ≈ 2.71826...

# Приближение √2
sqrt2 = Fraction(str(math.sqrt(2)))
print(sqrt2.limit_denominator(100)) # → 99/70   ≈ 1.41428...
```

> [!tip] `limit_denominator()` по умолчанию ограничивает знаменатель до 1 000 000.


### Математические функции

```python
from fractions import Fraction
from math import sqrt, sin, log

# math принимает Fraction, но возвращает float
sqrt(Fraction(1, 4))        # → 0.5 (float!)
sin(Fraction(1, 6))         # → 0.16589... (float!)
log(Fraction(1, 2))         # → -0.6931... (float!)

# Если нужен Fraction — конвертируй результат
result = sqrt(Fraction(9, 4))
Fraction(result).limit_denominator(1000)    # → 3/2
```

### Практические примеры

#### Точные вычисления с дробями

```python
from fractions import Fraction

# Задача: разделить 1 торт между 3 людьми и ещё раз пополам
piece = Fraction(1, 3)
half_piece = piece / 2
print(half_piece)           # → 1/6   (точно!)

# Сумма дробей — всегда точно
total = Fraction(1, 2) + Fraction(1, 3) + Fraction(1, 6)
print(total)                # → 1  (1/2 + 1/3 + 1/6 = 3/6 + 2/6 + 1/6 = 6/6 = 1)

# Составные проценты
principal = Fraction(1000)
rate = Fraction(1, 10)      # 10%
after_year = principal * (1 + rate)
print(after_year)           # → 1100  (без погрешностей)
```

#### Конвертация единиц (точная)

```python
from fractions import Fraction

# 1 фут = 12 дюймов, 1 дюйм = 2.54 см
inch_to_cm = Fraction(254, 100)   # 2.54 точно как 127/50
foot_to_inch = Fraction(12)

def feet_to_cm(feet):
    return feet * foot_to_inch * inch_to_cm

print(feet_to_cm(Fraction(5, 2)))   # → 381/5 = 76.2 см
```

#### Нахождение рационального приближения

```python
from fractions import Fraction
import math

# Найти простую дробь для любого float
def to_simple_fraction(value, max_denominator=100):
    return Fraction(value).limit_denominator(max_denominator)

print(to_simple_fraction(0.333))    # → 1/3
print(to_simple_fraction(0.142))    # → 1/7
print(to_simple_fraction(0.618))    # → 13/21  (золотое сечение!)
```

### Частые ошибки

```python
# ❌ Создание из float даёт неожиданный результат
from fractions import Fraction
Fraction(0.34)
# → 6124895493223875/18014398509481984  (а не 17/50!)

# ✅ Через строку
Fraction('0.34')    # → 17/50

# ❌ Смешивание с Decimal
from decimal import Decimal
Decimal('1.5') + Fraction(1, 2)     # → TypeError!

# ✅ Конвертируй явно
Decimal(Fraction(1, 2).numerator) / Decimal(Fraction(1, 2).denominator)
# → Decimal('0.5')

# ⚠️ Возведение в дробную степень → float
Fraction(2, 1) ** Fraction(1, 2)    # → 1.4142135... (float, не Fraction!)

# ⚠️ Python < 3.12: пробелы в строке → ValueError
Fraction('1 / 3')   # ValueError в Python < 3.12
Fraction('1/3')     # ✅ работает везде
```

### Когда использовать Fraction

**✅ Используй Fraction когда:**
- Математические задачи с дробями
- Нужны точные рациональные числа
- Конвертация единиц с точными коэффициентами
- Приближение иррациональных чисел через `limit_denominator()`
- Алгоритмы где важна точность (линейная алгебра, вероятности)

**❌ Не используй Fraction когда:**
- Финансовые расчёты (используй `Decimal`)
- Нужна скорость (используй `float`)
- Числа иррациональные и дробь не имеет смысла

---

## 📊 Сравнение числовых типов

| | `int` | `float` | `Decimal` | `Fraction` |
|---|---|---|---|---|
| Тип числа | Целое | Вещественное | Десятичное | Рациональное |
| Точность | Абсолютная | ~15-17 знаков | Настраиваемая | Абсолютная |
| Скорость | ✅✅ | ✅✅ | ⚠️ | ⚠️ |
| Смешивание с int | ✅ | ✅ | ✅ | ✅ |
| Смешивание с float | ✅ | ✅ | ❌ | ✅ |
| Для финансов | ⚠️ | ❌ | ✅ | ⚠️ |
| Для математики дробей | ❌ | ❌ | ⚠️ | ✅ |

---

## 🔗 Связанные темы

- [[02 — 🔣 Числа с плавающей точкой (float)]]
- [[04 — 🔟 Тип данных Decimal]]
- [[06 — 🔮 Тип данных complex]]
