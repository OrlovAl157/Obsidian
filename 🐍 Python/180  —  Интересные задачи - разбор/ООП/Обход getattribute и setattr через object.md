---

tags:

- python
- тема/ооп
- тема/магические-методы
- тема/рецепты
- статус/готово

---

# 🔓 Обход **getattribute** и **setattr** через object

## 📌 Суть

> Когда в классе переопределены `__getattribute__` или `__setattr__`, любое обращение к атрибутам через `self.` вызывает эти методы. Чтобы обойти их и обратиться к атрибутам **напрямую** — используют `object.__getattribute__`, `object.__setattr__` или `self.__dict__`.

---

## 🧩 Почему это важно — реальный пример

Задача: класс `ProtectedObject` запрещает доступ к защищённым атрибутам (начинающимся с `_`). Но при создании объекта нужно **установить** защищённые атрибуты — в обход запрета.

```python
class ProtectedObject:

    @staticmethod
    def check_attr(attr):
        if attr.startswith('_'):
            raise AttributeError('Доступ к защищенному атрибуту невозможен')

    def __init__(self, **kwargs):
        # ❌ Так не работает — вызывает __setattr__ → check_attr → AttributeError!
        # for name, value in kwargs.items():
        #     self.__dict__[name] = value   # self.__dict__ → __getattribute__ → check_attr!

        # ✅ Правильно — обходим __getattribute__ и __setattr__ напрямую:
        object.__getattribute__(self, '__dict__').update(kwargs)

    def __getattribute__(self, attr):
        object.__getattribute__(self, 'check_attr')(attr)   # ← тоже через object!
        return object.__getattribute__(self, attr)

    def __setattr__(self, attr, value):
        self.check_attr(attr)
        object.__setattr__(self, attr, value)

    def __delattr__(self, attr):
        self.check_attr(attr)
        object.__delattr__(self, attr)
```

---

## 🔍 Три способа обойти магические методы

### Способ 1 — `object.__getattribute__(self, attr)`

Прямой вызов базовой реализации — полностью обходит переопределённый `__getattribute__`:

```python
# Вместо self.attr (вызвало бы __getattribute__):
value = object.__getattribute__(self, 'attr')   # ✅ напрямую

# Применение — получить __dict__ в обход проверок:
d = object.__getattribute__(self, '__dict__')   # ✅
```

---

### Способ 2 — `object.__setattr__(self, attr, value)`

Прямой вызов базовой реализации — обходит переопределённый `__setattr__`:

```python
# Вместо self.attr = value (вызвало бы __setattr__):
object.__setattr__(self, 'attr', value)   # ✅ напрямую
```

---

### Способ 3 — `self.__dict__[attr] = value`

Прямая запись в словарь атрибутов — обходит `__setattr__`:

```python
self.__dict__['attr'] = value   # ✅ обходит __setattr__
```

> ⚠️ Но внутри переопределённого `__getattribute__` — `self.__dict__` сам вызовет `__getattribute__`! Рекурсия! Используй `object.__getattribute__(self, '__dict__')`.

---

## 🔄 Цепочка вызовов — где возникает рекурсия

```
self._password = 'x'
  ↓
__setattr__('_password', 'x')
  ↓
check_attr('_password') → AttributeError ❌


# А в __init__ нам НУЖНО установить _password:
object.__getattribute__(self, '__dict__').update({'_password': 'x'})
  ↓
object.__getattribute__ → обходит наш __getattribute__ ✅
  ↓
получаем __dict__ напрямую
  ↓
.update() → записываем в словарь напрямую, минуя __setattr__ ✅
```

---

## 💡 Почему в `__getattribute__` вызываем `check_attr` через `object`

```python
def __getattribute__(self, attr):
    # ❌ Так вызовет рекурсию:
    # self.check_attr(attr)   # self.check_attr → снова __getattribute__!

    # ✅ Правильно:
    object.__getattribute__(self, 'check_attr')(attr)
```

`self.check_attr` — это тоже обращение к атрибуту → вызов `__getattribute__` → бесконечная рекурсия.

---

## 📊 Когда что использовать

|Ситуация|Решение|
|---|---|
|Нужно получить атрибут в обход `__getattribute__`|`object.__getattribute__(self, attr)`|
|Нужно установить атрибут в обход `__setattr__`|`object.__setattr__(self, attr, value)` или `self.__dict__[attr] = value`|
|Нужно установить много атрибутов сразу|`object.__getattribute__(self, '__dict__').update(kwargs)`|
|Внутри `__getattribute__` нужен `__dict__`|`object.__getattribute__(self, '__dict__')` — никогда не `self.__dict__`!|

---

## ✅ Итоговый паттерн

```python
class MyClass:
    def __init__(self, **kwargs):
        # Устанавливаем атрибуты в обход __setattr__:
        object.__getattribute__(self, '__dict__').update(kwargs)
        # или:
        # object.__setattr__(self, 'attr', value)

    def __getattribute__(self, attr):
        # Вызываем методы класса через object — без рекурсии:
        object.__getattribute__(self, 'some_method')(attr)
        return object.__getattribute__(self, attr)

    def __setattr__(self, attr, value):
        # Устанавливаем через object — без рекурсии:
        object.__setattr__(self, attr, value)
```

---

## 🔗 Связанные темы

- [[Магические методы атрибутов]]
- [[DefaultObject — динамические атрибуты]]
- [[type, object, классы и экземпляры]]

## ❓ Вопросы / Непонятное

- ...