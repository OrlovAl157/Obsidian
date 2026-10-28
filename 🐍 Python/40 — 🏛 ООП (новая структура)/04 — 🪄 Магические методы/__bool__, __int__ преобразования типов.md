---

tags:

- python
- тема/ооп
- тема/магические-методы
- статус/готово

---

# 🔄 Магические методы преобразования типов

## 📌 Коротко

> Магические методы преобразования типов определяют поведение объекта при передаче в функции `bool()`, `int()`, `float()`, `complex()`. Без них все экземпляры своих классов считаются `True`, а преобразование в числа недоступно.

---

## 📖 Теория

### Приоритет методов при `bool()`

```
bool(obj)
  ↓
__bool__() определён? → ДА → вызвать __bool__()
                      → НЕТ → __len__() определён?
                                  → ДА → вызвать __len__()
                                         ненулевое? → True
                                         ноль?      → False
                                  → НЕТ → всегда True
```

> ⚠️ Если определены оба — всегда используется `__bool__()`.

---

## 🔧 Методы

### `__bool__()` — приведение к логическому типу

Вызывается при `bool(obj)`, в условии `if`, в `all()`, `any()`.

```python
class Angle:
    def __init__(self, value):
        self.value = value

    def __bool__(self):
        return self.value != 0


print(bool(Angle(0)))     # → False
print(bool(Angle(90)))    # → True

# Работает в if:
angle = Angle(90)
if angle:
    print('ненулевой угол')   # → ненулевой угол

# Работает в all() и any():
angles = [Angle(100), Angle(0), Angle(-50)]
print(all(angles))   # → False  (есть нулевой)
print(any(angles))   # → True   (есть ненулевые)
```

> 💡 Без `__bool__()` все экземпляры своих классов всегда `True`.

---

### `__len__()` как запасной для `bool()`

Если `__bool__()` не определён — Python пробует `__len__()`:

```python
class Angle:
    def __init__(self, value):
        self.value = value

    def __len__(self):
        return self.value != 0   # 0 → False, 1 → True


print(bool(Angle(0)))    # → False  (через __len__)
print(bool(Angle(90)))   # → True   (через __len__)
```

---

### `__int__()`, `__float__()`, `__complex__()` — числовые преобразования

```python
class Angle:
    def __init__(self, value):
        self.value = value

    def __int__(self):
        return int(self.value)

    def __float__(self):
        return float(self.value)

    def __complex__(self):
        return complex(self.value)


angle = Angle(100.7)

print(int(angle))      # → 100
print(float(angle))    # → 100.7
print(complex(angle))  # → (100.7+0j)
```

---

### `__index__()` — целочисленный индекс

Особый метод — сигнализирует что объект **является целым числом по сути**. Используется там где нужен именно `int`:

```python
class Angle:
    def __init__(self, value):
        self.value = value

    def __index__(self):
        return self.value        # должен возвращать int!


angle = Angle(100)

print(int(angle))      # → 100   (fallback если нет __int__)
print(float(angle))    # → 100.0 (fallback если нет __float__)
print(bin(angle))      # → 0b1100100
print(oct(angle))      # → 0o144
print(hex(angle))      # → 0x64

# Позволяет использовать объект как индекс списка:
lst = [10, 20, 30, 40, 50]
i = Angle(2)
print(lst[i])          # → 30  ← без __index__ было бы TypeError!

# Позволяет использовать в срезах:
print(lst[Angle(1):Angle(4)])  # → [20, 30, 40]
```

> ⚠️ `__index__()` должен возвращать строго `int`, иначе `TypeError`.

> 💡 `__index__()` — это fallback для `int()` и `float()` если `__int__()` и `__float__()` не определены. Но не наоборот — наличие `__int__()` не даёт доступ к `bin()`, `oct()`, `hex()` и индексации.

---

## 📊 Сравнение методов

|Метод|Вызывается при|Должен вернуть|Особенности|
|---|---|---|---|
|`__bool__()`|`bool()`, `if`, `all()`, `any()`|`bool`|Приоритет над `__len__`|
|`__len__()`|`len()`, `bool()` (запасной)|`int >= 0`|Используется для bool если нет `__bool__`|
|`__int__()`|`int()`|`int`||
|`__float__()`|`float()`|`float`||
|`__complex__()`|`complex()`|`complex`||
|`__index__()`|`int()`, `float()`, `bin()`, `oct()`, `hex()`, индексация|`int`|Fallback для числовых преобразований + доступ к bin/oct/hex|

---

## ⚠️ Частые ошибки

```python
# ❌ __bool__ возвращает не bool:
def __bool__(self):
    return self.value   # если value = 5, вернёт int!

# ✅ Правильно:
def __bool__(self):
    return bool(self.value)   # или self.value != 0

# ❌ __index__ возвращает float:
def __index__(self):
    return float(self.value)   # TypeError!

# ✅ Правильно:
def __index__(self):
    return int(self.value)
```

---

## 💻 Быстрая шпаргалка

```python
class MyClass:
    def __init__(self, value):
        self.value = value

    # Логическое приведение:
    def __bool__(self):
        return bool(self.value)         # или self.value != 0

    # Числовые преобразования:
    def __int__(self):
        return int(self.value)

    def __float__(self):
        return float(self.value)

    def __complex__(self):
        return complex(self.value)

    # Целочисленный индекс (даёт bin/oct/hex и индексацию):
    def __index__(self):
        return int(self.value)          # строго int!
```

---

## 🔗 Связанные темы

- [[Магические методы **len** и **bool**]]
- [[Унарные операторы **pos** **neg**]]
- [[00 Магические методы — шпаргалка]]

## ❓ Вопросы / Непонятное

- ...