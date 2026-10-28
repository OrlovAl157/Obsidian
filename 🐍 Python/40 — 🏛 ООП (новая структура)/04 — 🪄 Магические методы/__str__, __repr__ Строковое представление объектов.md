---
tags:
  - python
  - тема/ооп
  - тема/магические_методы
  - статус/завершён
уровень: начальный
---

# 🖨️ Строковое представление объектов — `__str__` и `__repr__`

## 📌 Коротко

> Для строкового представления объектов используются функции `str()` и `repr()`. Первая возвращает **неформальное** представление (для человека), вторая — **формальное** (для интерпретатора). За их работу отвечают магические методы `__str__()` и `__repr__()`.

---

## 📖 Теория

### Функции `str()` и `repr()`

| Функция | Представление | Для кого |
|---|---|---|
| `str()` | Неформальное | Человек |
| `repr()` | Формальное | Интерпретатор / разработчик |

```python
from datetime import date

dt = date(2022, 10, 23)

print(str(dt))    # → 2022-10-23
print(repr(dt))   # → datetime.date(2022, 10, 23)
```

Строка, возвращаемая `repr()`, является корректным Python-выражением — её можно преобразовать обратно в объект с помощью `eval()`:

```python
import datetime

dt1 = datetime.date(2022, 10, 23)
dt2 = eval(repr(dt1))

print(dt2)        # → 2022-10-23
print(type(dt2))  # → <class 'datetime.date'>
```

> 💡 Если объект передаётся в `print()` как самостоятельный аргумент — автоматически вызывается `str()`.

---

### Магические методы `__str__()` и `__repr__()`

`str()` и `repr()` — это просто обёртки над магическими методами:

```python
from datetime import date

dt = date(2022, 10, 23)

print(str(dt) == dt.__str__())    # → True
print(repr(dt) == dt.__repr__())  # → True
```

Если методы не определены в классе — используются базовые реализации, которые возвращают **одно и то же**:

```python
class MyClass:
    pass

obj = MyClass()

print(str(obj))   # → <__main__.MyClass object at 0x...>
print(repr(obj))  # → <__main__.MyClass object at 0x...>
```

---

### Определение `__str__()` и `__repr__()` в своём классе

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f'Кот по имени {self.name}'

    def __repr__(self):
        return f"Cat('{self.name}')"


cat = Cat('Кемаль')

print(str(cat))    # → Кот по имени Кемаль
print(repr(cat))   # → Cat('Кемаль')
```

> 💡 Принято, что `__repr__()` должен возвращать строку, которую можно преобразовать обратно в объект через `eval()`.

---

## 📝 Примечания

### `str()` и `repr()` в `format()` и f-строках

Аналогично `print()`, если объект передаётся в `format()` или f-строку как самостоятельный аргумент — вызывается `str()`:

```python
from datetime import date

dt = date(2022, 10, 23)

print('Дата: {}'.format(dt))   # → Дата: 2022-10-23
print(f'Дата: {dt}')           # → Дата: 2022-10-23
```

Если же объект является **элементом коллекции** — вызывается `repr()`:

```python
from datetime import date

dates = [date(2022, 10, 23)]

print(dates)                       # → [datetime.date(2022, 10, 23)]
print('Даты: {}'.format(dates))    # → Даты: [datetime.date(2022, 10, 23)]
print(f'Даты: {dates}')            # → Даты: [datetime.date(2022, 10, 23)]
```

---

### Поведение при частичной реализации

Если в классе есть `__repr__()`, но нет `__str__()` — `str()` вызовет `__repr__()`:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return f"Cat('{self.name}')"


cat = Cat('Кемаль')

print(str(cat))    # → Cat('Кемаль')   ← вызвался __repr__
print(repr(cat))   # → Cat('Кемаль')
```

Обратное **не работает** — если есть `__str__()`, но нет `__repr__()`, то `repr()` вернёт базовую реализацию:

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return f'Кот по имени {self.name}'


cat = Cat('Кемаль')

print(str(cat))    # → Кот по имени Кемаль
print(repr(cat))   # → <__main__.Cat object at 0x...>  ← базовая реализация
```

> ⚠️ `__repr__()` — запасной вариант для `str()`, но не наоборот.

---

### Тип возвращаемого значения

> ⚠️ Методы `__str__()` и `__repr__()` **всегда должны возвращать `str`**. Иначе будет возбуждено исключение `TypeError`.

---

## 💻 Быстрая шпаргалка

```python
class Cat:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

    def __str__(self):
        return f'Кот по имени {self.name}'        # для человека

    def __repr__(self):
        return f"Cat('{self.name}', '{self.breed}')"  # для интерпретатора


cat = Cat('Кемаль', 'Британский')

print(str(cat))          # → Кот по имени Кемаль
print(repr(cat))         # → Cat('Кемаль', 'Британский')
print(cat)               # → Кот по имени Кемаль  (print вызывает str())
print([cat])             # → [Cat('Кемаль', 'Британский')]  (коллекция вызывает repr())
print(eval(repr(cat)))   # → Кот по имени Кемаль  (repr → eval → новый объект)
```

| Ситуация | Какой метод вызывается |
|---|---|
| `str(obj)` | `__str__()`, при отсутствии — `__repr__()` |
| `repr(obj)` | `__repr__()`, при отсутствии — базовая реализация |
| `print(obj)` | `__str__()` |
| `f'{obj}'`, `format(obj)` | `__str__()` |
| Объект в коллекции (`[obj]`) | `__repr__()` |

---

## 🔗 Связанные темы

- [[00 Магические методы]]
- [[00 — 📖 Основы строк 🔹]]
- [[04 — 🔍 Представление строк (str и repr)]]

## ❓ Вопросы / Непонятное

- ...
