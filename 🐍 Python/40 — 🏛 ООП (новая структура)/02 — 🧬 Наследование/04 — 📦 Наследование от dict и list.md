---
tags:
  - python
  - ООП
  - наследование
  - статус/завершён
---

# 📦 Наследование от dict и list

## 📌 Коротко

> Встроенные `dict` и `list` спроектированы так, что их методы (`update`, `append` и др.) **не вызывают** переопределённый `__setitem__()`. Для надёжного наследования используй `UserDict`, `UserList`, `UserString` из модуля `collections`.

---

## 📖 Проблема: методы dict обходят __setitem__()

Создадим класс, который хранит все ключи в верхнем регистре:

```python
class UpperCaseDict(dict):
    def __setitem__(self, key, value):
        key = str(key).upper()
        super().__setitem__(key, value)

numbers = UpperCaseDict()
numbers['one'] = 1   # ✅ Вызывает наш __setitem__
print(numbers)       # {'ONE': 1}

numbers.update({'two': 2})  # ❌ Обходит __setitem__!
print(numbers)       # {'ONE': 1, 'two': 2} — ключ в нижнем регистре
```

Те же проблемы с `setdefault()` и инициализацией через `UpperCaseDict(one=1)`.

**Почему?** Встроенный `dict` реализован на C и его методы работают напрямую с внутренним хранилищем, **минуя Python-уровень** `__setitem__()`.

---

## 📖 Аналогичная проблема с list

```python
class StringList(list):
    def __setitem__(self, index, item):
        super().__setitem__(index, str(item))

sl = StringList([1, 2, 3])
sl[0] = 99        # ✅ Вызывает __setitem__
print(sl)         # ['99', 2, 3]

sl.append(4)      # ❌ Обходит __setitem__
sl.insert(0, 5)   # ❌ Обходит __setitem__
sl.extend([6, 7]) # ❌ Обходит __setitem__
print(sl)         # ['99', 2, 3, 4, 5, 6, 7] — числа, не строки
```

---

## 📖 Почему dict и list так устроены

Это **сознательное решение** разработчиков Python — принцип открытости/закрытости:

- **Открыты для расширения** — можно добавлять методы
- **Закрыты для модификации** — нельзя легко поменять внутреннюю логику

Причины:
1. Защита инвариантов встроенных типов
2. Ускорение работы (C-реализация без Python-прослойки)

---

## 📖 Решение: UserDict из collections

`UserDict` — обёртка вокруг обычного `dict`. Все методы работают через Python и вызывают переопределённый `__setitem__()`:

```python
from collections import UserDict

class UpperCaseDict(UserDict):
    def __setitem__(self, key, value):
        key = str(key).upper()
        self.data[key] = value  # self.data — это обычный dict внутри

numbers = UpperCaseDict({'one': 1, 'two': 2})
numbers['three'] = 3
numbers.update({'four': 4})
numbers.setdefault('five', 5)
print(numbers)
# {'ONE': 1, 'TWO': 2, 'THREE': 3, 'FOUR': 4, 'FIVE': 5} ✅ Всё работает!
```

**Ключевое отличие:** в `UserDict` все методы вызывают `self[key] = value`, что запускает твой `__setitem__()`.

### Атрибут data

`UserDict` хранит обычный словарь в `self.data`. Обращение к нему напрямую проще, чем постоянные вызовы `super()`:

```python
class UpperCaseDict(UserDict):
    def __setitem__(self, key, value):
        self.data[str(key).upper()] = value  # Напрямую в data
```

---

## 📖 UserList из collections

Аналогично для списков:

```python
from collections import UserList

class StringList(UserList):
    def __init__(self, iterable):
        super().__init__(str(item) for item in iterable)

    def __setitem__(self, index, item):
        self.data[index] = str(item)

    def insert(self, index, item):
        self.data.insert(index, str(item))

    def append(self, item):
        self.data.append(str(item))

    def extend(self, other):
        self.data.extend(str(item) for item in other)

lst = StringList([1, 2, 3])
lst.append(4)
lst.insert(0, 5)
lst.extend([6, 7])
print(lst)  # ['5', '1', '2', '3', '4', '6', '7'] ✅
```

> [!tip] Зачем super() в __init__, но self.data в остальных?
> `super().__init__()` используется один раз в инициализаторе — для корректной работы при дальнейшем наследовании. В остальных методах `self.data` удобнее и проще.

---

## 📖 UserString из collections

Для строк — аналогичная обёртка:

```python
from collections import UserString

class MutableString(UserString):
    def upper(self):
        self.data = self.data.upper()  # Меняем self.data напрямую

    def __setitem__(self, index, value):
        lst = list(self.data)
        lst[index] = value
        self.data = ''.join(lst)

ms = MutableString('hello')
ms.upper()
print(ms)     # HELLO

ms[0] = 'J'
print(ms)     # JELLO
```

---

## 📖 Когда наследоваться от dict/list всё же нормально

Прямое наследование от `dict` или `list` подходит, если переопределяешь **один специфический метод** без конфликта с остальными:

```python
class DefaultDict(dict):
    def __init__(self, *args, default=None, **kwargs):
        super().__init__(*args, **kwargs)
        self.default = default

    def __missing__(self, key):
        return self.default  # Вызывается __getitem__ при отсутствии ключа

dd = DefaultDict({'a': 1}, default='нет такого')
print(dd['a'])  # 1
print(dd['b'])  # нет такого  — не KeyError!
```

**Почему OK:** `__missing__()` — специальный хук, который `__getitem__()` вызывает сам. Никаких конфликтов с `update`, `setdefault` и др.

> [!tip] __missing__()
> По умолчанию `__missing__()` возбуждает `KeyError`. Переопределив его — можно вернуть значение по умолчанию вместо ошибки. Именно так устроен `collections.defaultdict`.

---

## 📖 Исторический контекст

`UserDict`, `UserList` и `UserString` появились в Python ещё **до того**, как стало возможным наследоваться от `list` и `dict` напрямую. Сейчас они остаются в стандартной библиотеке для удобства и обратной совместимости.

---

## 📊 Quick Reference

| Ситуация | Решение |
|---|---|
| Переопределить `__setitem__` и хочешь чтобы `update` его вызывал | `UserDict` |
| Переопределить `append`/`insert`/`extend` в списке | `UserList` |
| Расширить строку с изменяемым `data` | `UserString` |
| Переопределить только `__missing__` в словаре | Можно от `dict` |
| Добавить новые методы без изменения существующих | Можно от `dict`/`list` |

---

## 🔗 Связанные темы

- [[01 — 🧬 Введение в наследование]]
- [[02 — 🔁 super() и расширение методов]]
- [[03 — 🏗 Наследование от неизменяемых типов]]
- [[Наследование]] — общий справочник
