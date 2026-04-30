---

tags:

- python
- ООП
- перечисления
- статус/завершён

---

# 🏳 Битовые флаги (Flag)

## 📌 Коротко

> `Flag` — подкласс `Enum`, элементы которого поддерживают **битовые операции**: `|`, `&`, `^`, `~`. Используется когда объект может одновременно находиться в **нескольких состояниях**.

---

## 📖 Зачем нужен Flag

`Enum` — объект в **одном** состоянии из фиксированного набора. `Flag` — объект может быть в **нескольких** состояниях одновременно.

```python
# Enum — только одно состояние
class Status(Enum):
    ACTIVE = 1
    BANNED = 2
# Пользователь либо активен, либо забанен

# Flag — несколько состояний сразу
class Permission(Flag):
    READ = 1
    WRITE = 2
    EXECUTE = 4
# Пользователь может иметь READ + WRITE одновременно
```

---

## 📖 Почему значения — степени двойки

Значения должны быть степенями двойки (`1, 2, 4, 8, 16, ...`), чтобы каждый элемент занимал **уникальную позицию** в двоичной записи:

```python
from enum import Flag

class Color(Flag):
    RED   = 1   # 001
    GREEN = 2   # 010
    BLUE  = 4   # 100
```

Тогда комбинация `RED | GREEN` даёт `011` — единица на позиции RED **и** на позиции GREEN. Это позволяет однозначно восстановить состав комбинации.

> \[!tip\] `auto()` сам расставляет степени двойки для `Flag`:
>
> ```python
> from enum import Flag, auto
> 
> class Color(Flag):
>     RED   = auto()   # 1
>     GREEN = auto()   # 2
>     BLUE  = auto()   # 4
> ```

---

## 📖 Оператор | — объединение

```python
from enum import Flag, auto

class Color(Flag):
    RED   = auto()
    GREEN = auto()
    BLUE  = auto()

combined = Color.RED | Color.GREEN
print(combined)          # Color.RED|GREEN
print(type(combined))    # <flag 'Color'>
```

Результат объединения — тоже объект типа `Color`. Если комбинация **не определена** в классе явно — у неё нет имени:

```python
print(combined.name)    # None  (в Python 3.10)
                        # 'RED|GREEN'  (в Python 3.11+)
print(combined.value)   # 3
```

---

## 📖 Оператор & — пересечение

Возвращает **общие** элементы двух комбинаций:

```python
combined1 = Color.RED | Color.GREEN | Color.BLUE
combined2 = Color.GREEN | Color.BLUE

print(combined1 & combined2)   # Color.GREEN|BLUE
print(combined1 & Color.RED)   # Color.RED
```

Практический смысл: «есть ли у пользователя эти конкретные права?»

```python
required = Permission.READ | Permission.WRITE
user_perms = Permission.READ | Permission.WRITE | Permission.EXECUTE

# Проверка: есть ли у пользователя все нужные права?
has_required = (user_perms & required) == required
print(has_required)   # True
```

---

## 📖 Оператор ^ — исключение

Убирает указанные элементы из комбинации:

```python
combined = Color.RED | Color.GREEN | Color.BLUE

print(combined ^ Color.RED)                  # Color.GREEN|BLUE
print(combined ^ Color.GREEN)                # Color.RED|BLUE
print(combined ^ (Color.BLUE | Color.RED))   # Color.GREEN
```

---

## 📖 Оператор \~ — инверсия

Возвращает **все элементы**, кроме указанных:

```python
print(~Color.RED)                  # Color.GREEN|BLUE
print(~Color.GREEN)                # Color.RED|BLUE
print(~(Color.RED | Color.GREEN))  # Color.BLUE
```

---

## 📖 Проверка на принадлежность — in

```python
combined = Color.RED | Color.GREEN

print(Color.RED in combined)    # True
print(Color.BLUE in combined)   # False

# Проверка подмножества
all_colors = Color.RED | Color.GREEN | Color.BLUE
print(combined in all_colors)   # True
```

---

## 📖 Нулевой элемент

Когда битовые операции дают `0` — флаг автоматически создаёт элемент с именем `None` и значением `0`. Лучше определить его явно:

```python
class Color(Flag):
    NONE  = 0    # явный нулевой элемент
    RED   = auto()
    GREEN = auto()
    BLUE  = auto()

zero = ~(Color.RED | Color.GREEN | Color.BLUE)

print(zero)        # Color.NONE
print(zero.name)   # 'NONE'
print(zero.value)  # 0
```

---

## 📖 Python 3.10 vs Python 3.11

Возможность3.103.11`name` у комбинации`None'RED|GREEN'len(element)TypeError`работает`*element` (распаковка)`TypeError`работаетНулевой элемент`Color.0Color(0)`

```python
# Python 3.11+
combined = Color.RED | Color.GREEN | Color.BLUE
print(len(combined))   # 3
print(*combined)       # Color.RED Color.GREEN Color.BLUE
```

---

## 📖 Пример из жизни — FSM для бота

`Flag` отлично подходит для **машины состояний** (FSM) когда пользователь может **проходить несколько состояний** и нужно помнить историю:

```python
from enum import Flag, auto

class UserState(Flag):
    NONE         = 0
    REGISTERED   = auto()   # 0001 — зарегистрировался
    ADDED_CART   = auto()   # 0010 — добавил в корзину
    COMPLAINED   = auto()   # 0100 — подал жалобу
    PURCHASED    = auto()   # 1000 — совершил покупку

# Право подать жалобу — только кто зарегистрировался И купил
CAN_COMPLAIN = UserState.REGISTERED | UserState.PURCHASED

user_a = UserState.REGISTERED | UserState.ADDED_CART | UserState.PURCHASED
user_b = UserState.REGISTERED | UserState.ADDED_CART

# Проверка: есть ли у пользователя все нужные состояния?
def can_complain(user_state: UserState) -> bool:
    return (user_state & CAN_COMPLAIN) == CAN_COMPLAIN

print(can_complain(user_a))   # True  — 1011 содержит 1001
print(can_complain(user_b))   # False — 0011 не содержит 1001
```

> \[!tip\] Преимущество перед bool-флагами Добавление нового состояния — просто одна строка в `Flag`. В варианте с bool-полями — новый атрибут + новый `if` в каждой проверке.

---

## 📊 Quick Reference

```python
from enum import Flag, auto

class Color(Flag):
    NONE  = 0
    RED   = auto()   # 1
    GREEN = auto()   # 2
    BLUE  = auto()   # 4

# Объединение
Color.RED | Color.GREEN          # Color.RED|GREEN (value=3)

# Пересечение
(Color.RED | Color.GREEN) & Color.RED   # Color.RED

# Исключение
(Color.RED | Color.GREEN) ^ Color.RED   # Color.GREEN

# Инверсия
~Color.RED                       # Color.GREEN|BLUE

# Проверка вхождения
Color.RED in (Color.RED | Color.GREEN)   # True

# Нулевой элемент
~(Color.RED | Color.GREEN | Color.BLUE)  # Color.NONE (value=0)

# Python 3.11+
combined = Color.RED | Color.GREEN
len(combined)   # 2
list(combined)  # [Color.RED, Color.GREEN]
```

---

## 🔗 Связанные темы

- [09 — 🔢 Перечисления (Enum)](./09%20%E2%80%94%20%F0%9F%94%A2%20%D0%9F%D0%B5%D1%80%D0%B5%D1%87%D0%B8%D1%81%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F%20\(Enum\).md "mcp-wiki:%5B%5B09%20%E2%80%94%20%F0%9F%94%A2%20%D0%9F%D0%B5%D1%80%D0%B5%D1%87%D0%B8%D1%81%D0%BB%D0%B5%D0%BD%D0%B8%D1%8F%20(Enum)%5D%5D")
- [[Битовые операции]]
