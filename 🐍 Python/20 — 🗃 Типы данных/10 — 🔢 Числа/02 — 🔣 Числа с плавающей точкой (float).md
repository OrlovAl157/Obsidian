---
tags:
  - python
  - тема/типы_данных
  - тема/числа
  - статус/завершён
уровень: начальный
изменяемый: false
хешируемый: true
dict_key: true
итерируемый: false
индексируемый: false
---

# 🔣 Числа с плавающей точкой (float)

## 📌 Коротко

> Числа с плавающей точкой в Python. Используются для дробных чисел, но имеют ограниченную точность (~15-17 значащих цифр). Поддерживают специальные значения: бесконечность (inf) и NaN.

---

## 📖 Теория

### Основы

**float** в Python — это числа с плавающей точкой двойной точности (стандарт IEEE 754). Они используются для представления дробных чисел, но имеют ограничения по точности.

**💡 Ключевые особенности:**
- ✅ Дробные числа
- ✅ Научная нотация (1.5e2 = 150.0)
- ✅ Специальные значения (inf, nan)
- ⚠️ Ограниченная точность (~15-17 значащих цифр)
- ⚠️ Проблема точности: 0.1 + 0.2 ≠ 0.3

### Создание float

```python
# Обычные числа
x = 3.14
y = -0.5
z = 0.0

# Целые числа автоматически становятся float при делении
10 / 2                          # → 5.0 (не 5!)

# Научная нотация (экспоненциальная)
x = 1.5e2                       # → 150.0 (1.5 × 10²)
y = 1.5e-2                      # → 0.015 (1.5 × 10⁻²)
z = 3e8                         # → 300000000.0
large = 6.02e23                 # → число Авогадро

# Из строки
x = float("3.14")               # → 3.14
y = float("-2.5")               # → -2.5
z = float("1.5e2")              # → 150.0
w = float("  42.0  ")           # → 42.0 (пробелы игнорируются)

# Из целого числа
x = float(42)                   # → 42.0
y = float(-5)                   # → -5.0

# Специальные значения
inf = float("inf")              # → бесконечность
neg_inf = float("-inf")         # → минус бесконечность
nan = float("nan")              # → Not a Number

# Проверка типа
isinstance(3.14, float)         # → True
isinstance(3, float)            # → False
type(3.14)                      # → <class 'float'>
```

---

## 💻 Примеры кода

### Арифметические операции

```python
# Базовые операции
3.5 + 2.5                       # → 6.0
5.7 - 2.3                       # → 3.4
2.5 * 4                         # → 10.0
7.5 / 2                         # → 3.75
2.5 ** 2                        # → 6.25

# Целочисленное деление (возвращает float)
7.5 // 2                        # → 3.0 (не 3!)
-7.5 // 2                       # → -4.0

# Остаток от деления
7.5 % 2                         # → 1.5
-7.5 % 2                        # → 0.5

# Унарные операторы
+3.14                           # → 3.14
-3.14                           # → -3.14

# Составные операторы
x = 10.0
x += 2.5                        # → 12.5
x -= 1.5                        # → 11.0
x *= 2                          # → 22.0
x /= 4                          # → 5.5
x **= 2                         # → 30.25

# Операции с int и float
5 + 2.5                         # → 7.5 (результат float)
10 * 0.5                        # → 5.0 (результат float)
```

### Преобразование типов

```python
# float → int (отбрасывает дробную часть)
int(3.7)                        # → 3
int(-3.7)                       # → -3
int(3.9999)                     # → 3

# float → str
str(3.14)                       # → "3.14"
str(1.5e2)                      # → "150.0"

# str → float
float("3.14")                   # → 3.14
float("123")                    # → 123.0
# float("abc")                  # ❌ ValueError!

# bool → float
float(True)                     # → 1.0
float(False)                    # → 0.0

# float → bool
bool(0.0)                       # → False
bool(0.001)                     # → True
bool(-1.5)                      # → True
```

### Встроенные функции

```python
# Абсолютное значение
abs(-3.7)                       # → 3.7
abs(3.7)                        # → 3.7

# Округление
round(3.7)                      # → 4
round(3.5)                      # → 4 (к ближайшему чётному!)
round(4.5)                      # → 4 (к ближайшему чётному!)
round(3.14159, 2)               # → 3.14
round(123.456, 1)               # → 123.5
round(123.456, -1)              # → 120.0 (округление десятков)

# Минимум и максимум
min(3.14, 2.71, 1.41)           # → 1.41
max(3.14, 2.71, 1.41)           # → 3.14

# Сумма
sum([1.5, 2.5, 3.5])            # → 7.5

# Возведение в степень
pow(2.5, 2)                     # → 6.25
pow(4, 0.5)                     # → 2.0 (корень квадратный)
```

### Методы float

```python
# Проверка, является ли float целым числом
(3.0).is_integer()              # → True
(3.5).is_integer()              # → False
(3.00001).is_integer()          # → False

# as_integer_ratio() - представить как дробь
(0.5).as_integer_ratio()        # → (1, 2) то есть 1/2
(0.25).as_integer_ratio()       # → (1, 4) то есть 1/4
(3.14).as_integer_ratio()       # → (3537115888337719, 1125899906842624)

# hex() - шестнадцатеричное представление
(3.14).hex()                    # → '0x1.91eb851eb851fp+1'
float.fromhex('0x1.91eb851eb851fp+1')  # → 3.14
```

### Специальные значения

#### Бесконечность (infinity)

```python
# Создание бесконечности
inf = float("inf")
neg_inf = float("-inf")

# Арифметика с бесконечностью
inf + 100                       # → inf
inf - 100                       # → inf
inf * 2                         # → inf
inf / 2                         # → inf
-inf                            # → -inf

# Сравнение
inf > 1000000                   # → True
inf > inf                       # → False
inf == inf                      # → True
-inf < inf                      # → True

# Особые случаи
inf + inf                       # → inf
inf - inf                       # → nan
inf / inf                       # → nan
inf * 0                         # → nan

# Проверка на бесконечность
import math
math.isinf(inf)                 # → True
math.isinf(-inf)                # → True
math.isinf(3.14)                # → False
```

#### Not a Number (NaN)

```python
# Создание NaN
nan = float("nan")

# NaN не равен ничему, даже самому себе!
nan == nan                      # → False (!)
nan != nan                      # → True
nan < 5                         # → False
nan > 5                         # → False

# Арифметика с NaN всегда даёт NaN
nan + 5                         # → nan
nan * 2                         # → nan
nan / 2                         # → nan

# Проверка на NaN
import math
math.isnan(nan)                 # → True (правильный способ!)
math.isnan(3.14)                # → False

# Откуда берётся NaN
0.0 / 0.0                       # → nan
float("inf") - float("inf")     # → nan
float("inf") / float("inf")     # → nan
```

#### Проверка специальных значений

```python
import math

# Проверка на конечность
math.isfinite(3.14)             # → True
math.isfinite(float("inf"))     # → False
math.isfinite(float("nan"))     # → False

# Проверка всех случаев
value = 42.0
if math.isnan(value):
    print("NaN")
elif math.isinf(value):
    print("Infinity")
elif math.isfinite(value):
    print("Normal number")
```

### Проблема точности float

#### Почему 0.1 + 0.2 ≠ 0.3

```python
# Проблема точности
0.1 + 0.2                       # → 0.30000000000000004 (не 0.3!)
0.1 + 0.2 == 0.3                # → False (!)

# Почему так происходит?
# Float хранится в двоичном формате (IEEE 754)
# 0.1 в двоичной системе - бесконечная дробь
# Поэтому хранится с погрешностью

# Другие примеры
0.1 + 0.1 + 0.1                 # → 0.30000000000000004
1.2 - 1.0                       # → 0.19999999999999996
0.3 - 0.2                       # → 0.09999999999999998

# Накопление ошибок
result = 0.0
for _ in range(10):
    result += 0.1
result                          # → 0.9999999999999999 (не 1.0!)
```

#### Правильное сравнение float

```python
import math

# ❌ НЕПРАВИЛЬНО: прямое сравнение
0.1 + 0.2 == 0.3                # → False

# ✅ ПРАВИЛЬНО: сравнение с погрешностью
math.isclose(0.1 + 0.2, 0.3)    # → True

# С указанием точности
math.isclose(1.0, 1.0001, rel_tol=1e-3)     # → True
math.isclose(1.0, 1.0001, rel_tol=1e-5)     # → False

# Абсолютная погрешность
math.isclose(0.0, 0.0001, abs_tol=1e-4)     # → True
math.isclose(0.0, 0.0001, abs_tol=1e-5)     # → False

# Ручное сравнение с погрешностью
def almost_equal(a, b, epsilon=1e-9):
    return abs(a - b) < epsilon

almost_equal(0.1 + 0.2, 0.3)    # → True
```

#### Решение: Decimal для точности

```python
from decimal import Decimal

# Обычный float
0.1 + 0.2                       # → 0.30000000000000004

# С Decimal
Decimal("0.1") + Decimal("0.2") # → Decimal('0.3') ✅

# Для денежных расчётов
price = Decimal("19.99")
quantity = Decimal("3")
total = price * quantity        # → Decimal('59.97')

# С округлением
from decimal import ROUND_HALF_UP
total.quantize(Decimal("0.01"), ROUND_HALF_UP)  # → Decimal('59.97')

# ⚠️ НЕ создавайте Decimal из float!
Decimal(0.1)                    # → Decimal('0.1000000000000000055...')
Decimal("0.1")                  # → Decimal('0.1') ✅
```

### Сравнение чисел

```python
# Операторы сравнения
3.14 == 3.14                    # → True
3.14 != 2.71                    # → True
3.14 > 2.71                     # → True
3.14 < 2.71                     # → False
3.14 >= 3.14                    # → True
3.14 <= 3.14                    # → True

# Цепочки сравнений
1.0 < 2.5 < 3.0                 # → True
x = 2.5
1.0 < x < 10.0                  # → True

# Сравнение с int
3.0 == 3                        # → True
3.5 == 3                        # → False
3.0 > 2                         # → True

# Проблемы с точностью
0.1 + 0.2 == 0.3                # → False!
import math
math.isclose(0.1 + 0.2, 0.3)    # → True ✅
```

### Практические примеры

#### Проценты

```python
def percentage(value, total):
    """Вычислить процент"""
    return (value / total) * 100

percentage(25, 100)             # → 25.0
percentage(3, 4)                # → 75.0

def percent_of(percent, total):
    """Процент от числа"""
    return (percent / 100) * total

percent_of(20, 150)             # → 30.0
percent_of(15.5, 200)           # → 31.0

def percent_change(old, new):
    """Процентное изменение"""
    return ((new - old) / old) * 100

percent_change(50, 75)          # → 50.0 (рост на 50%)
percent_change(100, 80)         # → -20.0 (падение на 20%)
```

#### Конвертация температуры

```python
def celsius_to_fahrenheit(c):
    """Цельсий в Фаренгейт"""
    return (c * 9/5) + 32

def fahrenheit_to_celsius(f):
    """Фаренгейт в Цельсий"""
    return (f - 32) * 5/9

def celsius_to_kelvin(c):
    """Цельсий в Кельвин"""
    return c + 273.15

celsius_to_fahrenheit(0)        # → 32.0
celsius_to_fahrenheit(100)      # → 212.0
fahrenheit_to_celsius(32)       # → 0.0
celsius_to_kelvin(0)            # → 273.15
```

#### Линейная интерполяция

```python
def lerp(start, end, t):
    """Линейная интерполяция (t от 0.0 до 1.0)"""
    return start + (end - start) * t

lerp(0, 100, 0.5)               # → 50.0
lerp(10, 20, 0.25)              # → 12.5
lerp(0, 10, 0.75)               # → 7.5

def inverse_lerp(start, end, value):
    """Обратная интерполяция"""
    return (value - start) / (end - start)

inverse_lerp(0, 100, 50)        # → 0.5
inverse_lerp(10, 20, 15)        # → 0.5
```

#### Ограничение диапазона (clamp)

```python
def clamp(value, min_value, max_value):
    """Ограничить значение диапазоном"""
    return max(min_value, min(value, max_value))

clamp(5.5, 0.0, 10.0)           # → 5.5
clamp(-2.3, 0.0, 10.0)          # → 0.0
clamp(15.7, 0.0, 10.0)          # → 10.0
```

#### Среднее значение

```python
def average(numbers):
    """Среднее арифметическое"""
    return sum(numbers) / len(numbers)

average([1.5, 2.5, 3.5])        # → 2.5
average([10.2, 20.5, 15.8])     # → 15.5

def weighted_average(values, weights):
    """Взвешенное среднее"""
    return sum(v * w for v, w in zip(values, weights)) / sum(weights)

weighted_average([85, 90, 78], [0.3, 0.5, 0.2])  # → 85.6
```

#### Проверка диапазона

```python
def in_range(value, min_val, max_val):
    """Проверить, что значение в диапазоне"""
    return min_val <= value <= max_val

in_range(5.5, 0.0, 10.0)        # → True
in_range(-1.0, 0.0, 10.0)       # → False

def normalize(value, min_val, max_val):
    """Нормализовать в диапазон [0, 1]"""
    return (value - min_val) / (max_val - min_val)

normalize(5, 0, 10)             # → 0.5
normalize(7.5, 0, 10)           # → 0.75
```

### Частые ошибки

```python
# ❌ Прямое сравнение float
0.1 + 0.2 == 0.3                # → False!

# ✅ Используйте isclose()
import math
math.isclose(0.1 + 0.2, 0.3)    # → True

# ❌ Деление на ноль
# 1.0 / 0.0                     # ZeroDivisionError!

# ✅ Проверка перед делением
if divisor != 0:
    result = number / divisor

# ❌ Создание Decimal из float
from decimal import Decimal
Decimal(0.1)                    # → неточное значение

# ✅ Создавайте из строки
Decimal("0.1")                  # → точное значение

# ⚠️ Накопление ошибок в циклах
total = 0.0
for i in range(10):
    total += 0.1
total == 1.0                    # → False!

# ⚠️ Сравнение очень малых чисел
1e-100 == 0.0                   # → False (но очень близко)

# ⚠️ Округление банкира
round(0.5)                      # → 0 (к чётному!)
round(1.5)                      # → 2
round(2.5)                      # → 2 (к чётному!)
```

### Когда использовать float

**✅ Используйте float когда:**
- Нужны дробные числа
- Научные вычисления
- Геометрия, физика, математика
- Измерения (температура, расстояние, вес)
- Быстрые приближённые вычисления
- Работа с процентами, коэффициентами
- Графика, игры, симуляции

**❌ НЕ используйте float когда:**
- Финансовые расчёты, деньги (используйте Decimal)
- Критична точность до последнего знака (используйте Decimal)
- Нужны точные дроби (используйте Fraction)
- Достаточно целых чисел (используйте int)

```python
# ✅ Хорошо для float
temperature = 36.6              # температура
distance = 123.45               # расстояние в км
coefficient = 0.85              # коэффициент

# ❌ Плохо для float (используйте Decimal)
price = 19.99                   # цена
balance = 1000.50               # баланс счёта
tax_rate = 0.15                 # налог

# Правильно для денег:
from decimal import Decimal
price = Decimal("19.99")
balance = Decimal("1000.50")
tax_rate = Decimal("0.15")
```

---

## 🔗 Связанные темы

- [[Целые числа (int) 🔹]]
- [[Округление чисел]]
- [[03 — ➡️ Операторы]]
- [[01 — 🔢 Math]]

## ❓ Вопросы / Непонятное

-
