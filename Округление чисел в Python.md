
## 1. Функция round()

### Базовое использование

```python
round(число, количество_знаков)
```

### Примеры

```python
round(3.14159)      # 3 (по умолчанию до целого)
round(3.14159, 2)   # 3.14 (два знака после запятой)
round(3.14159, 3)   # 3.142
round(1234.5678, 1) # 1234.6
round(1234.5678, -1) # 1230.0 (округление до десятков)
round(1234.5678, -2) # 1200.0 (до сотен)
```

### ⚠️ Особенность round() в Python

Python использует **банковское округление** (round half to even):

```python
round(2.5)  # 2 (округление к ближайшему четному)
round(3.5)  # 4 (округление к ближайшему четному)
round(4.5)  # 4
round(5.5)  # 6
```

---

## 2. Математическое округление

### Модуль math

```python
import math

# Округление вверх (к большему)
math.ceil(3.1)   # 4
math.ceil(3.9)   # 4
math.ceil(-3.1)  # -3

# Округление вниз (к меньшему)
math.floor(3.1)  # 3
math.floor(3.9)  # 3
math.floor(-3.1) # -4

# Отбрасывание дробной части
math.trunc(3.9)  # 3
math.trunc(-3.9) # -3
```

---

## 3. Округление до целого числа

```python
int(3.7)        # 3 (просто отбрасывает дробную часть)
int(round(3.7)) # 4 (сначала округляет, потом конвертирует)
```

---

## 4. Округление с форматированием

### f-строки (Python 3.6+)

```python
number = 3.14159

f"{number:.2f}"    # '3.14' (2 знака после запятой)
f"{number:.3f}"    # '3.142'
f"{number:.0f}"    # '3'

# С выравниванием
f"{number:10.2f}"  # '      3.14' (ширина 10 символов)
```

### format()

```python
"{:.2f}".format(3.14159)  # '3.14'
"{:.3f}".format(3.14159)  # '3.142'
```

### Старый способ (%)

```python
"%.2f" % 3.14159  # '3.14'
"%.3f" % 3.14159  # '3.142'
```

---

## 5. Модуль decimal для точного округления

```python
from decimal import Decimal, ROUND_HALF_UP, ROUND_DOWN, ROUND_UP

# Точное математическое округление
number = Decimal('3.14159')

# Округление по стандартным правилам
number.quantize(Decimal('0.01'), rounding=ROUND_HALF_UP)  # 3.14

# Разные режимы округления
Decimal('2.5').quantize(Decimal('1'), rounding=ROUND_HALF_UP)  # 3
Decimal('2.5').quantize(Decimal('1'), rounding=ROUND_DOWN)     # 2
Decimal('2.5').quantize(Decimal('1'), rounding=ROUND_UP)       # 3
```

---

## 6. NumPy округление (для массивов)

```python
import numpy as np

arr = np.array([3.14159, 2.71828, 1.41421])

np.round(arr, 2)     # [3.14, 2.72, 1.41]
np.ceil(arr)         # [4., 3., 2.]
np.floor(arr)        # [3., 2., 1.]
np.trunc(arr)        # [3., 2., 1.]
```

---

## 7. Пользовательская функция для классического округления

Если нужно стандартное математическое округление (0.5 → 1):

```python
import math

def round_half_up(n, decimals=0):
    multiplier = 10 ** decimals
    return math.floor(n * multiplier + 0.5) / multiplier

round_half_up(2.5)    # 3.0
round_half_up(3.5)    # 4.0
round_half_up(2.445, 2)  # 2.45
```

---

## 8. Сравнительная таблица

|Функция|3.4|3.5|3.6|-3.4|-3.5|
|---|---|---|---|---|---|
|`round()`|3|4|4|-3|-4|
|`math.floor()`|3|3|3|-4|-4|
|`math.ceil()`|4|4|4|-3|-3|
|`math.trunc()`|3|3|3|-3|-3|
|`int()`|3|3|3|-3|-3|

---

## 9. Практические примеры

```python
# Округление денежных сумм
price = 19.99
round(price, 2)  # 19.99

# Округление процентов
percentage = 45.6789
round(percentage, 1)  # 45.7

# Округление до ближайшего 5
def round_to_5(x):
    return round(x / 5) * 5

round_to_5(23)  # 25
round_to_5(22)  # 20

# Округление до ближайшего 10
def round_to_10(x):
    return round(x / 10) * 10

round_to_10(234)  # 230
round_to_10(237)  # 240
```

---

## 💡 Рекомендации

- **Для обычных задач** → используйте `round()`
- **Для финансовых расчетов** → используйте `decimal.Decimal`
- **Для всегда вверх/вниз** → используйте `math.ceil()` / `math.floor()`
- **Для массивов данных** → используйте `numpy.round()`