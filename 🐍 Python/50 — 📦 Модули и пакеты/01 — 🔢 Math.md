---
tags:
  - python
  - тема/модули
  - тема/числа
  - статус/завершён
уровень: начальный
стандартная_библиотека: true
сторонний_пакет: false
---

## 📥 Импорт модуля

```python
# Стандартный импорт
import math
math.sqrt(16)                   # → использование через math.

# Импорт конкретных функций
from math import sqrt, pi, ceil
sqrt(16)                        # → без префикса math.

# Импорт всех функций (не рекомендуется)
from math import *
sqrt(16)                        # → может привести к конфликтам имён
```

## 💡 Полезные советы

- **Только для чисел**: math работает только с int и float, не с комплексными (используйте cmath)
- **Радианы**: тригонометрия работает в радианах, не в градусах
- **Преобразование**: `radians()` и `degrees()` для конвертации
- **Точность**: функции math точнее ручных вычислений
- **NaN и inf**: `isnan()`, `isinf()` для проверки специальных значений
- **Константы**: `pi`, `e`, `tau`, `inf`, `nan`
- **Округление**: `floor()`, `ceil()`, `trunc()` для разных типов округления
- **Факториал**: `factorial()` быстрее рекурсивных реализаций

## 🚀 Быстрая справка

|Задача|Функция|Пример|
|---|---|---|
|Квадратный корень|`sqrt()`|`math.sqrt(16)` → `4.0`|
|Степень|`pow()`|`math.pow(2, 3)` → `8.0`|
|Абсолютное значение|`fabs()`|`math.fabs(-5)` → `5.0`|
|Округление вниз|`floor()`|`math.floor(3.7)` → `3`|
|Округление вверх|`ceil()`|`math.ceil(3.2)` → `4`|
|Отбросить дробную часть|`trunc()`|`math.trunc(3.7)` → `3`|
|НОД|`gcd()`|`math.gcd(48, 18)` → `6`|
|Факториал|`factorial()`|`math.factorial(5)` → `120`|
|Синус|`sin()`|`math.sin(math.pi/2)` → `1.0`|
|Косинус|`cos()`|`math.cos(0)` → `1.0`|
|Тангенс|`tan()`|`math.tan(math.pi/4)` → `1.0`|
|Натуральный логарифм|`log()`|`math.log(math.e)` → `1.0`|
|Логарифм по основанию 10|`log10()`|`math.log10(100)` → `2.0`|
|Экспонента|`exp()`|`math.exp(1)` → `2.718...`|

## 📐 Константы

```python
import math

# Основные константы
math.pi                         # → 3.141592653589793 (π)
math.e                          # → 2.718281828459045 (e)
math.tau                        # → 6.283185307179586 (τ = 2π)

# Бесконечность и NaN
math.inf                        # → бесконечность (∞)
-math.inf                       # → минус бесконечность (-∞)
math.nan                        # → Not a Number (NaN)

# Примеры использования
radius = 5
circumference = 2 * math.pi * radius        # → 31.41...
area = math.pi * radius ** 2                # → 78.53...

# Полный круг в радианах
full_circle = math.tau                      # → 2π = 6.28... радиан
```

## 🔢 Округление

```python
import math

# Округление вниз (floor) - к -∞
math.floor(3.7)                 # → 3
math.floor(-3.7)                # → -4
math.floor(3.0)                 # → 3

# Округление вверх (ceil) - к +∞
math.ceil(3.2)                  # → 4
math.ceil(-3.2)                 # → -3
math.ceil(3.0)                  # → 3

# Отбросить дробную часть (trunc) - к 0
math.trunc(3.7)                 # → 3
math.trunc(-3.7)                # → -3
math.trunc(3.0)                 # → 3

# Сравнение с round()
round(3.5)                      # → 4 (к ближайшему чётному)
math.floor(3.5)                 # → 3
math.ceil(3.5)                  # → 4
math.trunc(3.5)                 # → 3

# Практический пример: округление вверх
def round_up_pages(items, items_per_page):
    return math.ceil(items / items_per_page)

round_up_pages(25, 10)          # → 3 страницы
```

## 🔄 Степени и корни

```python
import math

# Квадратный корень
math.sqrt(16)                   # → 4.0
math.sqrt(2)                    # → 1.4142135623730951
math.sqrt(0)                    # → 0.0
# math.sqrt(-1)                 # ❌ ValueError!

# Возведение в степень
math.pow(2, 3)                  # → 8.0 (всегда возвращает float)
math.pow(2, -1)                 # → 0.5
math.pow(4, 0.5)                # → 2.0 (корень квадратный)

# Экспонента (e^x)
math.exp(0)                     # → 1.0
math.exp(1)                     # → 2.718... (e)
math.exp(2)                     # → 7.389...

# Корень n-ой степени (через степень)
def nth_root(x, n):
    return math.pow(x, 1/n)

nth_root(27, 3)                 # → 3.0 (кубический корень)
nth_root(16, 4)                 # → 2.0 (корень 4-й степени)
```

## 📊 Логарифмы

```python
import math

# Натуральный логарифм (ln, по основанию e)
math.log(math.e)                # → 1.0
math.log(1)                     # → 0.0
math.log(10)                    # → 2.302585...
# math.log(0)                   # ❌ ValueError!
# math.log(-1)                  # ❌ ValueError!

# Логарифм по основанию 10
math.log10(100)                 # → 2.0
math.log10(1000)                # → 3.0
math.log10(1)                   # → 0.0

# Логарифм по основанию 2
math.log2(8)                    # → 3.0
math.log2(1024)                 # → 10.0

# Логарифм по произвольному основанию
math.log(8, 2)                  # → 3.0 (log₂(8))
math.log(125, 5)                # → 3.0 (log₅(125))

# log1p(x) = log(1 + x) - точнее для малых x
math.log1p(0)                   # → 0.0
math.log1p(0.001)               # → 0.0009995...

# Практический пример: количество битов для числа
def bits_needed(n):
    return math.ceil(math.log2(n + 1))

bits_needed(255)                # → 8 бит
```

## 📐 Тригонометрия (радианы!)

```python
import math

# Синус
math.sin(0)                     # → 0.0
math.sin(math.pi / 2)           # → 1.0
math.sin(math.pi)               # → 0.0 (на самом деле ~1.2e-16)
math.sin(math.pi * 2)           # → 0.0

# Косинус
math.cos(0)                     # → 1.0
math.cos(math.pi / 2)           # → 0.0 (на самом деле ~6e-17)
math.cos(math.pi)               # → -1.0

# Тангенс
math.tan(0)                     # → 0.0
math.tan(math.pi / 4)           # → 1.0
# math.tan(math.pi / 2)         # → очень большое число (~1.6e16)

# Преобразование градусов в радианы
math.radians(0)                 # → 0.0
math.radians(90)                # → 1.5707... (π/2)
math.radians(180)               # → 3.1415... (π)
math.radians(360)               # → 6.2831... (2π)

# Преобразование радиан в градусы
math.degrees(0)                 # → 0.0
math.degrees(math.pi / 2)       # → 90.0
math.degrees(math.pi)           # → 180.0
math.degrees(math.tau)          # → 360.0

# Практический пример: синус угла в градусах
def sin_degrees(angle):
    return math.sin(math.radians(angle))

sin_degrees(30)                 # → 0.5
sin_degrees(45)                 # → 0.707... (√2/2)
sin_degrees(90)                 # → 1.0
```

## 🔺 Обратные тригонометрические функции

```python
import math

# Арксинус (sin⁻¹) - результат в радианах [-π/2, π/2]
math.asin(0)                    # → 0.0
math.asin(0.5)                  # → 0.523... (30°)
math.asin(1)                    # → 1.5707... (π/2 = 90°)
# math.asin(2)                  # ❌ ValueError! (вне диапазона [-1, 1])

# Арккосинус (cos⁻¹) - результат в радианах [0, π]
math.acos(1)                    # → 0.0
math.acos(0.5)                  # → 1.047... (60°)
math.acos(0)                    # → 1.5707... (π/2 = 90°)
math.acos(-1)                   # → 3.1415... (π = 180°)

# Арктангенс (tan⁻¹) - результат в радианах [-π/2, π/2]
math.atan(0)                    # → 0.0
math.atan(1)                    # → 0.785... (π/4 = 45°)
math.atan(math.inf)             # → 1.5707... (π/2 = 90°)

# Арктангенс двух аргументов (учитывает квадрант)
math.atan2(1, 1)                # → 0.785... (45°)
math.atan2(1, 0)                # → 1.5707... (90°)
math.atan2(0, 1)                # → 0.0
math.atan2(-1, -1)              # → -2.356... (-135°)

# Практический пример: угол между точками
def angle_between_points(x1, y1, x2, y2):
    """Угол в градусах от точки 1 к точке 2"""
    rad = math.atan2(y2 - y1, x2 - x1)
    return math.degrees(rad)

angle_between_points(0, 0, 1, 1)    # → 45.0°
```

## 🔢 Математические операции

### Абсолютное значение и знак

```python
import math

# Абсолютное значение (всегда возвращает float)
math.fabs(-5)                   # → 5.0
math.fabs(3.7)                  # → 3.7

# Сравнение с abs()
abs(-5)                         # → 5 (int)
math.fabs(-5)                   # → 5.0 (float)

# Копирование знака
math.copysign(5, -1)            # → -5.0 (знак от второго аргумента)
math.copysign(-5, 1)            # → 5.0
math.copysign(3.5, -2.7)        # → -3.5
```

### НОД и НОК

```python
import math

# НОД (наибольший общий делитель)
math.gcd(48, 18)                # → 6
math.gcd(100, 50)               # → 50
math.gcd(17, 19)                # → 1 (взаимно простые)

# НОД нескольких чисел
from functools import reduce
reduce(math.gcd, [48, 18, 24])  # → 6

# НОК (наименьшее общее кратное) Python 3.9+
math.lcm(12, 18)                # → 36
math.lcm(4, 6, 8)               # → 24

# НОК для Python < 3.9
def lcm(a, b):
    return abs(a * b) // math.gcd(a, b)

lcm(12, 18)                     # → 36
```

### Факториал и комбинаторика

```python
import math

# Факториал
math.factorial(0)               # → 1
math.factorial(5)               # → 120
math.factorial(10)              # → 3628800
# math.factorial(-1)            # ❌ ValueError!
# math.factorial(3.5)           # ❌ ValueError! (только целые)

# Количество сочетаний C(n, k) = n! / (k! * (n-k)!) (Python 3.8+)
math.comb(5, 2)                 # → 10 (C из 5 по 2)
math.comb(10, 3)                # → 120

# Количество размещений P(n, k) = n! / (n-k)! (Python 3.8+)
math.perm(5, 2)                 # → 20 (A из 5 по 2)
math.perm(10, 3)                # → 720

# Ручное вычисление сочетаний
def combinations(n, k):
    return math.factorial(n) // (math.factorial(k) * math.factorial(n - k))

combinations(5, 2)              # → 10
```

## 🌊 Гиперболические функции

```python
import math

# Гиперболический синус
math.sinh(0)                    # → 0.0
math.sinh(1)                    # → 1.175...

# Гиперболический косинус
math.cosh(0)                    # → 1.0
math.cosh(1)                    # → 1.543...

# Гиперболический тангенс
math.tanh(0)                    # → 0.0
math.tanh(1)                    # → 0.761...

# Обратные гиперболические функции
math.asinh(0)                   # → 0.0
math.acosh(1)                   # → 0.0
math.atanh(0)                   # → 0.0

# Гиперболические функции полезны в ML и физике
# Например, tanh используется как функция активации
```

## ✅ Проверка значений

```python
import math

# Проверка на конечность
math.isfinite(42)               # → True
math.isfinite(math.inf)         # → False
math.isfinite(math.nan)         # → False

# Проверка на бесконечность
math.isinf(math.inf)            # → True
math.isinf(-math.inf)           # → True
math.isinf(42)                  # → False

# Проверка на NaN
math.isnan(math.nan)            # → True
math.isnan(42)                  # → False
math.isnan(math.inf)            # → False

# NaN не равен ничему, даже самому себе!
math.nan == math.nan            # → False (!)
math.isnan(math.nan)            # → True (правильная проверка)

# Проверка близости значений (Python 3.5+)
math.isclose(0.1 + 0.2, 0.3)    # → True
math.isclose(1.0, 1.0000001)    # → False
math.isclose(1.0, 1.0000001, rel_tol=1e-6)  # → True
```

## 📏 Специальные функции

```python
import math

# Модуль и остаток
math.fmod(7, 3)                 # → 1.0 (остаток как в C)
7 % 3                           # → 1 (остаток в Python)
# Разница проявляется с отрицательными числами
math.fmod(-7, 3)                # → -1.0
-7 % 3                          # → 2

# Разложение на мантиссу и экспоненту
math.frexp(8)                   # → (0.5, 4) то есть 0.5 * 2^4 = 8
math.frexp(12.5)                # → (0.78125, 4)

# Обратная операция
math.ldexp(0.5, 4)              # → 8.0 (0.5 * 2^4)

# Разложение на целую и дробную части
math.modf(3.75)                 # → (0.75, 3.0)
math.modf(-3.75)                # → (-0.75, -3.0)

# Гамма-функция (обобщение факториала)
math.gamma(5)                   # → 24.0 (= 4!)
math.gamma(0.5)                 # → 1.772... (= √π)

# Логарифм гамма-функции
math.lgamma(100)                # → 359.134... (log(99!))

# Функция ошибок (важна в статистике)
math.erf(0)                     # → 0.0
math.erf(1)                     # → 0.842...
math.erfc(0)                    # → 1.0 (дополнение к erf)
```

## 🎯 Практические примеры

### Расстояние между точками

```python
import math

def distance_2d(x1, y1, x2, y2):
    """Расстояние между двумя точками на плоскости"""
    return math.sqrt((x2 - x1)**2 + (y2 - y1)**2)

distance_2d(0, 0, 3, 4)         # → 5.0

def distance_3d(x1, y1, z1, x2, y2, z2):
    """Расстояние в 3D пространстве"""
    return math.sqrt((x2-x1)**2 + (y2-y1)**2 + (z2-z1)**2)

distance_3d(0, 0, 0, 1, 1, 1)   # → 1.732... (√3)

# Через hypot (Python 3.8+)
math.hypot(3, 4)                # → 5.0 (2D)
math.hypot(1, 1, 1)             # → 1.732... (3D)
```

### Площадь круга и длина окружности

```python
import math

def circle_area(radius):
    """Площадь круга"""
    return math.pi * radius ** 2

def circle_circumference(radius):
    """Длина окружности"""
    return 2 * math.pi * radius

circle_area(5)                  # → 78.539...
circle_circumference(5)         # → 31.415...
```

### Конвертация координат

```python
import math

def polar_to_cartesian(r, theta):
    """Полярные координаты в декартовы"""
    x = r * math.cos(theta)
    y = r * math.sin(theta)
    return x, y

def cartesian_to_polar(x, y):
    """Декартовы координаты в полярные"""
    r = math.sqrt(x**2 + y**2)
    theta = math.atan2(y, x)
    return r, theta

polar_to_cartesian(5, math.pi/4)       # → (3.535..., 3.535...)
cartesian_to_polar(3, 4)               # → (5.0, 0.927...)
```

### Проверка делимости на целые части

```python
import math

def can_distribute_equally(total, parts):
    """Можно ли разделить поровну"""
    return total % parts == 0

def distribute_with_pages(total, per_page):
    """Сколько страниц нужно"""
    return math.ceil(total / per_page)

distribute_with_pages(25, 10)   # → 3 страницы
```

### Нормализация углов

```python
import math

def normalize_angle(angle_deg):
    """Привести угол к диапазону [0, 360)"""
    return angle_deg % 360

def normalize_radians(angle_rad):
    """Привести угол к диапазону [0, 2π)"""
    return angle_rad % math.tau

normalize_angle(370)            # → 10.0°
normalize_angle(-30)            # → 330.0°
```

## ⚠️ Частые ошибки

```python
import math

# ❌ Градусы вместо радиан
math.sin(90)                    # → 0.893... (НЕПРАВИЛЬНО!)
math.sin(math.radians(90))      # → 1.0 (ПРАВИЛЬНО!)

# ❌ Корень из отрицательного числа
# math.sqrt(-1)                 # ValueError!
# Используйте cmath для комплексных чисел

# ❌ Логарифм нуля или отрицательного
# math.log(0)                   # ValueError!
# math.log(-1)                  # ValueError!

# ❌ Деление на ноль
# math.log(0)                   # ValueError!
# Используйте проверки

# ⚠️ Неточность float
math.sin(math.pi)               # → 1.2e-16 (не 0!)
math.isclose(math.sin(math.pi), 0)  # → True (правильная проверка)

# ⚠️ Очень большие факториалы медленны
# math.factorial(100000)        # Будет долго!

# ⚠️ NaN распространяется
math.sqrt(math.nan)             # → nan
math.sin(math.nan)              # → nan
1 + math.nan                    # → nan
```

## 🎯 Когда использовать math

**✅ Используйте math когда:**

- Нужны математические функции (корни, логарифмы, тригонометрия)
- Работаете с геометрией, физикой, инженерными расчётами
- Нужна высокая точность (лучше ручных формул)
- Работаете с вещественными числами
- Нужны константы (π, e, τ)
- Требуется быстродействие

**✅ НЕ используйте math когда:**

- Работаете с комплексными числами (используйте cmath)
- Нужны только базовые операции (+, -, *, /)
- Работаете со статистикой (используйте statistics)
- Нужна случайность (используйте random)
- Нужны матрицы и векторы (используйте numpy)

## 🔗 Связанные темы

- [[Целые числа (int) 🔹]]
- [[Числа с плавающей точкой (float) 🔹]]
- [[Округление чисел]]
