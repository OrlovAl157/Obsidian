# Памятка: Числа (int, float) в Python

## 💡 Полезные советы

- **int** - целые числа неограниченной длины (можно хранить очень большие числа)
- **float** - числа с плавающей точкой (примерно 15-17 значащих цифр)
- **Деление**: `/` всегда возвращает float, `//` целочисленное деление
- **Округление**: `round()` округляет к ближайшему чётному при .5
- **Проверка типа**: используйте `isinstance(x, (int, float))`
- **inf и nan**: float может быть бесконечностью (`inf`) или NaN (`nan`)
- **Underscores в числах**: `1_000_000` для читаемости (Python 3.6+)
- **Системы счисления**: `0b` (двоичная), `0o` (восьмеричная), `0x` (шестнадцатеричная)

## 🚀 Быстрая справка

|Задача               |Функция/Оператор|Пример                 |
|---------------------|----------------|-----------------------|
|Создать int          |`int()`         |`int("123")`           |
|Создать float        |`float()`       |`float("3.14")`        |
|Сложение             |`+`             |`5 + 3` → `8`          |
|Вычитание            |`-`             |`5 - 3` → `2`          |
|Умножение            |`*`             |`5 * 3` → `15`         |
|Деление              |`/`             |`5 / 2` → `2.5`        |
|Целочисленное деление|`//`            |`5 // 2` → `2`         |
|Остаток              |`%`             |`5 % 2` → `1`          |
|Возведение в степень |`**`            |`2 ** 3` → `8`         |
|Модуль (абсолютное)  |`abs()`         |`abs(-5)` → `5`        |
|Округление           |`round()`       |`round(3.7)` → `4`     |
|Округление вниз      |`math.floor()`  |`math.floor(3.7)` → `3`|
|Округление вверх     |`math.ceil()`   |`math.ceil(3.2)` → `4` |
|Минимум              |`min()`         |`min(1, 2, 3)` → `1`   |
|Максимум             |`max()`         |`max(1, 2, 3)` → `3`   |
|Сумма                |`sum()`         |`sum([1, 2, 3])` → `6` |
|Степень              |`pow()`         |`pow(2, 3)` → `8`      |

## 🔢 Целые числа (int)

### Создание целых чисел

```python
# Обычные числа
x = 42
y = -15
z = 0

# Очень большие числа (без ограничения)
big = 123456789012345678901234567890
huge = 10 ** 100                # гугол

# С underscores для читаемости (Python 3.6+)
million = 1_000_000
billion = 1_000_000_000

# Из строки
x = int("123")                  # → 123
y = int("-456")                 # → -456

# С указанием системы счисления
binary = int("1010", 2)         # → 10 (двоичная)
octal = int("17", 8)            # → 15 (восьмеричная)
hex_num = int("FF", 16)         # → 255 (шестнадцатеричная)

# Из float (отбрасывает дробную часть)
x = int(3.7)                    # → 3
y = int(-3.7)                   # → -3

# Из булева
x = int(True)                   # → 1
y = int(False)                  # → 0
```

### Системы счисления

```python
num = 42

# Двоичная (binary)
bin(42)                         # → "0b101010"
int("101010", 2)                # → 42
binary_literal = 0b101010       # → 42

# Восьмеричная (octal)
oct(42)                         # → "0o52"
int("52", 8)                    # → 42
octal_literal = 0o52            # → 42

# Шестнадцатеричная (hexadecimal)
hex(42)                         # → "0x2a"
int("2a", 16)                   # → 42
hex_literal = 0x2a              # → 42

# Убрать префикс
bin(42)[2:]                     # → "101010"
hex(42)[2:]                     # → "2a"
```

## 🔢 Числа с плавающей точкой (float)

### Создание float

```python
# Обычные числа
x = 3.14
y = -0.5
z = 0.0

# Научная нотация
x = 1.5e2                       # → 150.0 (1.5 × 10²)
y = 1.5e-2                      # → 0.015 (1.5 × 10⁻²)
z = 3e8                         # → 300000000.0

# Из строки
x = float("3.14")               # → 3.14
y = float("-2.5")               # → -2.5
z = float("1.5e2")              # → 150.0

# Из целого числа
x = float(42)                   # → 42.0

# Специальные значения
inf = float("inf")              # → бесконечность
neg_inf = float("-inf")         # → минус бесконечность
nan = float("nan")              # → Not a Number

# Проверка специальных значений
import math
math.isinf(inf)                 # → True
math.isnan(nan)                 # → True
math.isfinite(3.14)             # → True
```

### Точность float

```python
# Float имеет ограниченную точность
0.1 + 0.2                       # → 0.30000000000000004 (не 0.3!)

# Для точных вычислений используйте Decimal
from decimal import Decimal
Decimal("0.1") + Decimal("0.2") # → Decimal("0.3")

# Для денежных расчётов
from decimal import Decimal, ROUND_HALF_UP
price = Decimal("19.99")
quantity = Decimal("3")
total = (price * quantity).quantize(Decimal("0.01"), ROUND_HALF_UP)
# → Decimal("59.97")
```

## ➕ Арифметические операции

```python
# Базовые операции
5 + 3                           # → 8
5 - 3                           # → 2
5 * 3                           # → 15
5 / 2                           # → 2.5 (всегда float)
5 // 2                          # → 2 (целочисленное деление)
5 % 2                           # → 1 (остаток)
2 ** 3                          # → 8 (возведение в степень)

# Унарные операторы
+5                              # → 5
-5                              # → -5

# Составные операторы
x = 10
x += 5                          # → x = 15
x -= 3                          # → x = 12
x *= 2                          # → x = 24
x /= 4                          # → x = 6.0
x //= 2                         # → x = 3.0
x %= 2                          # → x = 1.0
x **= 3                         # → x = 1.0

# Приоритет операций (от высшего к низшему)
# 1. **
# 2. унарный +, -
# 3. *, /, //, %
# 4. бинарный +, -

2 + 3 * 4                       # → 14 (сначала умножение)
(2 + 3) * 4                     # → 20 (скобки первые)
2 ** 3 ** 2                     # → 512 (справа налево: 2^(3^2))
```

## 🔄 Преобразование типов

```python
# int → float
x = 42
float(x)                        # → 42.0

# float → int (отбрасывает дробную часть)
x = 3.7
int(x)                          # → 3
int(-3.7)                       # → -3

# str → int
int("123")                      # → 123
int("  42  ")                   # → 42 (пробелы игнорируются)
# int("123.45")                 # ❌ ValueError!
int(float("123.45"))            # → 123 ✅

# str → float
float("3.14")                   # → 3.14
float("  2.5  ")                # → 2.5
float("123")                    # → 123.0 ✅

# bool → int/float
int(True)                       # → 1
int(False)                      # → 0
float(True)                     # → 1.0

# int/float → bool
bool(0)                         # → False
bool(0.0)                       # → False
bool(42)                        # → True
bool(-1)                        # → True
bool(0.001)                     # → True
```

## 📊 Математические функции

### Встроенные функции

```python
# Абсолютное значение
abs(-5)                         # → 5
abs(-3.7)                       # → 3.7

# Округление
round(3.7)                      # → 4
round(3.5)                      # → 4 (к ближайшему чётному)
round(4.5)                      # → 4
round(3.14159, 2)               # → 3.14 (до 2 знаков)

# Минимум и максимум
min(1, 2, 3, 4, 5)              # → 1
max(1, 2, 3, 4, 5)              # → 5
min([1, 2, 3])                  # → 1 из списка

# Сумма
sum([1, 2, 3, 4, 5])            # → 15
sum(range(10))                  # → 45

# Возведение в степень
pow(2, 3)                       # → 8
pow(2, 3, 5)                    # → 3 (2³ mod 5)

# Divmod (частное и остаток)
divmod(17, 5)                   # → (3, 2) то есть 17 = 5*3 + 2
quotient, remainder = divmod(17, 5)
```

### Модуль math

```python
import math

# Константы
math.pi                         # → 3.141592653589793
math.e                          # → 2.718281828459045
math.tau                        # → 6.283185307179586 (2π)
math.inf                        # → бесконечность
math.nan                        # → Not a Number

# Округление
math.floor(3.7)                 # → 3 (вниз)
math.ceil(3.2)                  # → 4 (вверх)
math.trunc(3.7)                 # → 3 (отбросить дробную часть)

# Степени и корни
math.sqrt(16)                   # → 4.0 (квадратный корень)
math.pow(2, 3)                  # → 8.0 (степень)
math.exp(1)                     # → 2.718... (e^x)
math.log(10)                    # → 2.302... (натуральный логарифм)
math.log10(100)                 # → 2.0 (логарифм по основанию 10)
math.log2(8)                    # → 3.0 (логарифм по основанию 2)
math.log(8, 2)                  # → 3.0 (логарифм по основанию 2)

# Тригонометрия (радианы!)
math.sin(math.pi / 2)           # → 1.0
math.cos(0)                     # → 1.0
math.tan(math.pi / 4)           # → 1.0
math.asin(1)                    # → π/2 (арксинус)
math.acos(1)                    # → 0.0 (арккосинус)
math.atan(1)                    # → π/4 (арктангенс)

# Перевод градусов в радианы и обратно
math.radians(180)               # → 3.141... (π)
math.degrees(math.pi)           # → 180.0

# Гиперболические функции
math.sinh(1)                    # → гиперболический синус
math.cosh(0)                    # → 1.0
math.tanh(0)                    # → 0.0

# Другие функции
math.factorial(5)               # → 120 (5!)
math.gcd(48, 18)                # → 6 (НОД)
math.lcm(12, 18)                # → 36 (НОК, Python 3.9+)
math.fabs(-5)                   # → 5.0 (абсолютное значение как float)
math.copysign(5, -1)            # → -5.0 (копирует знак)

# Проверки
math.isfinite(3.14)             # → True
math.isinf(float("inf"))        # → True
math.isnan(float("nan"))        # → True
math.isclose(0.1 + 0.2, 0.3)    # → True (сравнение с погрешностью)
```

## 🔢 Операции с целыми числами

### Битовые операции

```python
# Битовые операторы
a = 60                          # → 0b111100
b = 13                          # → 0b001101

a & b                           # → 12 (AND: 0b001100)
a | b                           # → 61 (OR: 0b111101)
a ^ b                           # → 49 (XOR: 0b110001)
~a                              # → -61 (NOT: инверсия битов)
a << 2                          # → 240 (сдвиг влево: умножить на 4)
a >> 2                          # → 15 (сдвиг вправо: делить на 4)

# Практическое применение
# Проверить чётность
num = 42
is_even = (num & 1) == 0        # → True

# Умножение/деление на степень 2
num << 1                        # → умножить на 2
num >> 1                        # → делить на 2

# Обмен значений без временной переменной (трюк)
a = 5
b = 3
a = a ^ b
b = a ^ b
a = a ^ b
# → a = 3, b = 5
```

### Работа с разрядами

```python
num = 42

# Количество битов
num.bit_length()                # → 6 (нужно 6 бит для 42)

# Количество единиц в двоичном представлении
num.bit_count()                 # → 3 (Python 3.10+)

# Из байтов
int.from_bytes(b'\x00\x10', byteorder='big')    # → 16
int.from_bytes(b'\x10\x00', byteorder='little') # → 16

# В байты
(1024).to_bytes(2, byteorder='big')             # → b'\x04\x00'
```

## 📐 Сравнение чисел

```python
# Операторы сравнения
5 == 5                          # → True (равно)
5 != 3                          # → True (не равно)
5 > 3                           # → True (больше)
5 < 3                           # → False (меньше)
5 >= 5                          # → True (больше или равно)
5 <= 3                          # → False (меньше или равно)

# Цепочки сравнений
1 < 2 < 3                       # → True
1 < 2 > 0                       # → True
x = 5
1 < x < 10                      # → True

# Сравнение float (проблема точности)
0.1 + 0.2 == 0.3                # → False!

# Правильное сравнение float
import math
math.isclose(0.1 + 0.2, 0.3)    # → True
abs((0.1 + 0.2) - 0.3) < 1e-9   # → True

# Сравнение с бесконечностью
float("inf") > 1000000          # → True
float("-inf") < -1000000        # → True

# NaN не равен ничему, даже самому себе!
nan = float("nan")
nan == nan                      # → False
math.isnan(nan)                 # → True (правильная проверка)
```

## 🎯 Практические примеры

### Проверка типа числа

```python
def is_integer(n):
    return isinstance(n, int)

def is_float(n):
    return isinstance(n, float)

def is_number(n):
    return isinstance(n, (int, float))

# Проверка, что float - целое число
def is_whole_number(n):
    return isinstance(n, float) and n.is_integer()

is_whole_number(3.0)            # → True
is_whole_number(3.5)            # → False
```

### Проверка чётности/нечётности

```python
def is_even(n):
    return n % 2 == 0

def is_odd(n):
    return n % 2 != 0

is_even(4)                      # → True
is_odd(7)                       # → True

# Через битовые операции (быстрее)
def is_even_fast(n):
    return (n & 1) == 0
```

### Ограничение диапазона (clamp)

```python
def clamp(value, min_value, max_value):
    return max(min_value, min(value, max_value))

clamp(5, 0, 10)                 # → 5
clamp(-5, 0, 10)                # → 0
clamp(15, 0, 10)                # → 10
```

### Линейная интерполяция

```python
def lerp(start, end, t):
    """t от 0.0 до 1.0"""
    return start + (end - start) * t

lerp(0, 100, 0.5)               # → 50.0
lerp(10, 20, 0.25)              # → 12.5
```

### Процент и процентное соотношение

```python
def percentage(value, total):
    return (value / total) * 100

percentage(25, 100)             # → 25.0
percentage(3, 4)                # → 75.0

def percent_of(percent, total):
    return (percent / 100) * total

percent_of(20, 150)             # → 30.0 (20% от 150)

def percent_change(old, new):
    return ((new - old) / old) * 100

percent_change(50, 75)          # → 50.0 (рост на 50%)
percent_change(100, 80)         # → -20.0 (падение на 20%)
```

### Округление до кратного

```python
def round_to_multiple(number, multiple):
    return round(number / multiple) * multiple

round_to_multiple(23, 5)        # → 25
round_to_multiple(22, 5)        # → 20
round_to_multiple(127, 10)      # → 130
```

### Проверка простого числа

```python
def is_prime(n):
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    for i in range(3, int(n**0.5) + 1, 2):
        if n % i == 0:
            return False
    return True

is_prime(17)                    # → True
is_prime(20)                    # → False
```

### Факториал

```python
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)

factorial(5)                    # → 120

# Или используйте math.factorial
import math
math.factorial(5)               # → 120
```

### Числа Фибоначчи

```python
def fibonacci(n):
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b

fibonacci(10)                   # → 55
```

### НОД и НОК

```python
import math

# НОД (наибольший общий делитель)
math.gcd(48, 18)                # → 6

# НОД для нескольких чисел
from functools import reduce
reduce(math.gcd, [48, 18, 24])  # → 6

# НОК (наименьшее общее кратное)
def lcm(a, b):
    return abs(a * b) // math.gcd(a, b)

lcm(12, 18)                     # → 36

# Или используйте math.lcm (Python 3.9+)
math.lcm(12, 18)                # → 36
```

### Конвертация температуры

```python
def celsius_to_fahrenheit(c):
    return (c * 9/5) + 32

def fahrenheit_to_celsius(f):
    return (f - 32) * 5/9

celsius_to_fahrenheit(0)        # → 32.0
fahrenheit_to_celsius(32)       # → 0.0
```

### Вычисление расстояния

```python
import math

def distance_2d(x1, y1, x2, y2):
    return math.sqrt((x2 - x1)**2 + (y2 - y1)**2)

distance_2d(0, 0, 3, 4)         # → 5.0

def distance_3d(x1, y1, z1, x2, y2, z2):
    return math.sqrt((x2-x1)**2 + (y2-y1)**2 + (z2-z1)**2)
```

### Форматирование чисел

```python
# Разделители тысяч
num = 1234567
f"{num:,}"                      # → "1,234,567"
f"{num:_}"                      # → "1_234_567"

# Фиксированное количество знаков
pi = 3.14159
f"{pi:.2f}"                     # → "3.14"
f"{pi:.5f}"                     # → "3.14159"

# Дополнение нулями
num = 42
f"{num:05d}"                    # → "00042"

# Научная нотация
num = 1234567
f"{num:.2e}"                    # → "1.23e+06"

# Процент
ratio = 0.756
f"{ratio:.1%}"                  # → "75.6%"

# Выравнивание
num = 42
f"{num:>10}"                    # → "        42" (вправо)
f"{num:<10}"                    # → "42        " (влево)
f"{num:^10}"                    # → "    42    " (центр)

# Знак всегда
num = 42
f"{num:+}"                      # → "+42"
f"{-num:+}"                     # → "-42"
```

## ⚠️ Частые ошибки и особенности

```python
# ❌ Деление на ноль
# 5 / 0                         # ZeroDivisionError!
# 5 // 0                        # ZeroDivisionError!
# 5 % 0                         # ZeroDivisionError!

# ✅ Проверка перед делением
if divisor != 0:
    result = number / divisor

# ❌ Сравнение float напрямую
0.1 + 0.2 == 0.3                # → False (из-за точности)

# ✅ Используйте isclose
import math
math.isclose(0.1 + 0.2, 0.3)    # → True

# ⚠️ Целочисленное деление с отрицательными
-7 // 3                         # → -3 (округление вниз к -∞)
-7 % 3                          # → 2 (знак как у делителя)

# ⚠️ Возведение в степень правоассоциативно
2 ** 3 ** 2                     # → 512 (= 2^(3^2), не (2^3)^2)

# ⚠️ Огромные float могут потерять точность
big = 10.0 ** 308
big + 1 == big                  # → может быть True!

# ✅ Для больших целых используйте int
big_int = 10 ** 1000            # → работает отлично
```

## 🎯 Когда использовать что

**Используйте int когда:**

- Нужны точные целые числа
- Счётчики, индексы
- Большие числа (неограниченная точность)
- Битовые операции
- Деньги в копейках/центах

**Используйте float когда:**

- Нужны дробные числа
- Научные вычисления
- Измерения (температура, расстояние)
- Быстрые приближённые вычисления

**Используйте Decimal когда:**

- Финансовые расчёты
- Нужна точность до определённого знака
- Критична точность округления

**Используйте Fraction когда:**

- Нужны точные дроби
- Математические вычисления с рациональными числами

```python
from decimal import Decimal
from fractions import Fraction

# Decimal для денег
price = Decimal("19.99")
quantity = 3
total = price * quantity        # → Decimal("59.97")

# Fraction для точных дробей
f = Fraction(1, 3)              # → 1/3
f * 3                           # → Fraction(1, 1) = 1 (точно!)
```