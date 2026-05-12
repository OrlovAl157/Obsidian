---

tags:

- python
- тема/ооп
- тема/магические-методы
- тема/задачи
- статус/готово

---

# 🏛️ RomanNumeral — класс римских цифр

## 📌 Суть

> Класс для работы с римскими цифрами. Принимает как число так и строку. Поддерживает арифметику, сравнение и двустороннюю конвертацию.

---

## 💻 Полный код

```python
from functools import total_ordering

@total_ordering
class RomanNumeral:
    @staticmethod
    def convert_to_roman(num):
        num_roman = {
            1: 'I', 4: 'IV', 5: 'V', 9: 'IX',
            10: 'X', 40: 'XL', 50: 'L', 90: 'XC',
            100: 'C', 400: 'CD', 500: 'D', 900: 'CM', 1000: 'M'
        }
        result = ''
        while num != 0:
            for el in list(num_roman)[::-1]:
                if num >= el:
                    result += num_roman[el]
                    num -= el
                    break
        return result

    @staticmethod
    def convert_to_num(num):
        roman_num = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000}
        pre = 0
        result = 0
        for i in range(len(num) - 1, -1, -1):
            if roman_num[num[i]] >= pre:
                result += roman_num[num[i]]
                pre = roman_num[num[i]]
            else:
                result -= roman_num[num[i]]
                pre = roman_num[num[i]]
        return result

    def __init__(self, number):
        if isinstance(number, str):
            self.number = self.convert_to_num(number)
        else:
            self.number = number

    def __str__(self):
        return f'{self.convert_to_roman(self.number)}'

    def __int__(self):
        return self.number

    def __add__(self, other):
        if isinstance(other, self.__class__):
            return self.__class__(self.number + other.number)
        return NotImplemented

    def __sub__(self, other):
        if isinstance(other, self.__class__):
            return self.__class__(self.number - other.number)
        return NotImplemented

    def __eq__(self, other):
        if isinstance(other, self.__class__):
            return self.number == other.number
        return NotImplemented

    def __gt__(self, other):
        if isinstance(other, self.__class__):
            return self.number > other.number
        return NotImplemented
```

---

## 🔍 Разбор по частям

### `convert_to_roman` — жадный алгоритм

**Идея:** идём по словарю с конца (от большего к меньшему), жадно вычитаем максимально возможное значение.

```python
num_roman = {
    1: 'I', 4: 'IV', 5: 'V', 9: 'IX',
    10: 'X', 40: 'XL', 50: 'L', 90: 'XC',
    100: 'C', 400: 'CD', 500: 'D', 900: 'CM', 1000: 'M'
}
```

> 💡 Словарь содержит не только "чистые" значения (1, 5, 10...) но и **составные** (4, 9, 40, 90...) — это избавляет от отдельной обработки вычитательной нотации.

**Трассировка для 469:**

```
469 >= 400 → 'CD', остаток 69
 69 >= 50  → 'L',  остаток 19
 19 >= 10  → 'X',  остаток 9
  9 >= 9   → 'IX', остаток 0
результат: 'CDLXIX' ✅
```

**Почему `list(num_roman)[::-1]`?**

```python
list(num_roman)[::-1]  # разворачиваем ключи словаря
# [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
```

Нужно идти от большего к меньшему — чтобы жадно брать максимальное значение.

---

### `convert_to_num` — алгоритм с предыдущим значением

**Идея:** идём справа налево, сравниваем текущее значение с предыдущим.

- `текущее >= предыдущего` → **прибавляем**
- `текущее < предыдущего` → **вычитаем**

**Трассировка для "XIV":**

```
i=2: V=5,  pre=0  → 5>=0  → result+=5=5,   pre=5
i=1: I=1,  pre=5  → 1<5   → result-=1=4,   pre=1
i=0: X=10, pre=1  → 10>=1 → result+=10=14, pre=10
результат: 14 ✅
```

**Трассировка для "IX":**

```
i=1: X=10, pre=0  → 10>=0 → result+=10=10, pre=10
i=0: I=1,  pre=10 → 1<10  → result-=1=9,   pre=1
результат: 9 ✅
```

> 💡 Словарь только с базовыми символами — составные (`IV`, `IX`) не нужны, алгоритм сам их обрабатывает через вычитание.

> 💡 Идём с конца через `range(len(num)-1, -1, -1)` — нет риска `IndexError`, не нужны срезы.

---

### `__init__` — умный конструктор

```python
def __init__(self, number):
    if isinstance(number, str):
        self.number = self.convert_to_num(number)   # строка → число
    else:
        self.number = number                         # число → число
```

```python
RomanNumeral(14)      # число напрямую
RomanNumeral("XIV")   # строка → конвертируем
```

---

### `@total_ordering` — магия декоратора

Определены только `__eq__` и `__gt__` — остальные методы сравнения `@total_ordering` реализует автоматически:

```python
# Определено вручную:
__eq__   # ==
__gt__   # >

# Получено бесплатно от @total_ordering:
__lt__   # <
__le__   # <=
__ge__   # >=
__ne__   # != (автоматически из __eq__)
```

---

### Арифметика — возвращает новый объект

```python
def __add__(self, other):
    if isinstance(other, self.__class__):
        return self.__class__(self.number + other.number)  # новый RomanNumeral!
    return NotImplemented
```

> 💡 `self.__class__` вместо `RomanNumeral` — правильный способ, работает корректно при наследовании.

---

## 🎯 Использование

```python
a = RomanNumeral(14)
b = RomanNumeral("IX")

print(a)          # → XIV
print(int(a))     # → 14
print(a + b)      # → XXIII  (14 + 9 = 23)
print(a - b)      # → V      (14 - 9 = 5)
print(a > b)      # → True
print(a < b)      # → True  (от @total_ordering)
print(sorted([RomanNumeral(5), RomanNumeral(1), RomanNumeral(10)]))
# → [I, V, X]
```

---

## 💡 Ключевые решения

|Задача|Решение|Почему|
|---|---|---|
|Вычитательная нотация (IV, IX)|Включить в словарь|Не нужна отдельная логика|
|Конвертация в число|Алгоритм с `pre` справа налево|Чисто, без IndexError|
|Все операторы сравнения|`@total_ordering`|Меньше кода|
|Принимать строку и число|`isinstance` в `__init__`|Гибкий интерфейс|

---

## 🔗 Связанные темы

- [[Магические методы **eq** и **ne**]]
- [[Арифметические операции]]
- [[Декораторы]]
- [[Статические методы @staticmethod]]

## ❓ Вопросы / Непонятное

- ...