-----

tags:

- python
- тема/ооп
- тема/магические-методы
- статус/готово

-----

# 🔍 Магические методы атрибутов

## 📌 Коротко

> Атрибуты объектов управляются четырьмя магическими методами. `__getattribute__` вызывается **всегда** при любом обращении. `__getattr__` — только если атрибут **не найден**. `__setattr__` и `__delattr__` — при записи и удалении. Внутри этих методов нельзя использовать точечную нотацию — только `object.*` или `self.__dict__`.

-----

## 📖 Теория

### Три уровня работы с атрибутами

```
1. Встроенные функции     getattr() / setattr() / delattr() / hasattr()
         ↓  вызывают
2. Магические методы      __getattribute__ / __getattr__ / __setattr__ / __delattr__
         ↓  могут наткнуться на
3. Дескрипторы            @property / __get__ / __set__ / __delete__
```

> 💡 `getattr(obj, 'name')` — это то же самое что `obj.name`, только имя атрибута передаётся строкой. Главный плюс — имя можно вычислять динамически.

```python
attr_name = 'name'
obj.attr_name          # ❌ ищет атрибут буквально "attr_name"
getattr(obj, attr_name) # ✅ ищет атрибут "name"

# Динамический перебор атрибутов:
for field in ['name', 'age', 'breed']:
    print(getattr(cat, field))
```

-----

### Четыре операции с атрибутами

```python
obj.attr = 1      # установка → __setattr__
obj.attr          # получение → __getattribute__
obj.attr = 2      # изменение → __setattr__
del obj.attr      # удаление  → __delattr__
```

-----

### `__getattribute__` — перехват любого обращения

Вызывается **всегда** при любом обращении к атрибуту через точку или `getattr()`.

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __getattribute__(self, attr):
        print(f'Обращение к атрибуту: {attr}')
        return object.__getattribute__(self, attr)  # обязательно!


cat = Cat('Кемаль')
print(cat.name)
# → Обращение к атрибуту: name
# → Кемаль
```

#### ⚠️ Почему нельзя использовать `self.__dict__` внутри

`self.__dict__` — это тоже обращение к атрибуту → вызов `__getattribute__` → бесконечная рекурсия:

```python
def __getattribute__(self, attr):
    return self.__dict__[attr]   # ❌ RecursionError!

# Правильно — через object:
def __getattribute__(self, attr):
    return object.__getattribute__(self, attr)   # ✅

# Или через super():
def __getattribute__(self, attr):
    return super().__getattribute__(attr)        # ✅
```

#### `object.__getattribute__` vs `super().__getattribute__`

```python
# object — прямой вызов, нужно передавать self явно:
object.__getattribute__(self, attr)

# super() — уже привязан к self, передавать не нужно:
super().__getattribute__(attr)
```

При множественном наследовании `super()` идёт по MRO-цепочке — это правильный способ. `object.__getattribute__` всегда прыгает прямо к `object`, пропуская промежуточных родителей.

-----

### `__getattr__` — запасной план

Вызывается **только** в двух случаях:

1. `__getattribute__` возбудил `AttributeError`
2. `__getattr__` был явно вызван внутри `__getattribute__`

```python
class Cat:
    def __init__(self, name):
        self.name = name

    def __getattr__(self, attr):
        return None   # вместо AttributeError возвращаем None


cat = Cat('Кемаль')
print(cat.name)    # → Кемаль  (через __getattribute__)
print(cat.age)     # → None    (через __getattr__, атрибута нет)
```

#### Имитация динамических атрибутов

```python
class Cat:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

    def __getattr__(self, attr):
        if attr == 'info':
            return f'Имя: {self.name}\nПорода: {self.breed}'
        raise AttributeError(f"Атрибут '{attr}' не найден")


cat = Cat('Кемаль', 'Британский')
print(cat.info)
# → Имя: Кемаль
# → Порода: Британский
```

#### Цепочка вызовов при обращении к атрибуту

```
cat.age
  ↓
__getattribute__("age")
  ↓
атрибут найден?
  ├── ДА → вернуть значение
  │         (если это property → вызвать __get__)
  └── НЕТ → AttributeError
               ↓
           __getattr__("age")
               ↓
           вернуть значение по умолчанию или raise AttributeError
```

-----

### `__setattr__` — перехват записи

Вызывается при **любой** установке или изменении атрибута.

```python
class Cat:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

    def __setattr__(self, attr, value):
        # валидация:
        if attr == 'age' and value < 0:
            raise ValueError("Возраст не может быть отрицательным")
        object.__setattr__(self, attr, value)   # обязательно!
```

#### ⚠️ Нельзя использовать точку внутри `__setattr__`

```python
def __setattr__(self, attr, value):
    self.attr = value   # ❌ RecursionError! self.attr снова вызывает __setattr__

# Правильно — через __dict__ напрямую:
def __setattr__(self, attr, value):
    self.__dict__[attr] = value   # ✅

# Или через object:
def __setattr__(self, attr, value):
    object.__setattr__(self, attr, value)   # ✅
```

#### Пример — автоматически защищённые атрибуты

```python
class Cat:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

    def __setattr__(self, attr, value):
        self.__dict__['_' + attr] = value   # все атрибуты становятся _защищёнными


cat = Cat('Кемаль', 'Британский')
print(cat.__dict__)   # → {'_name': 'Кемаль', '_breed': 'Британский'}
```

-----

### `__delattr__` — перехват удаления

Вызывается при `del obj.attr` или `delattr(obj, 'attr')`.

```python
class Cat:
    def __init__(self, name, breed):
        self.name = name
        self.breed = breed

    def __delattr__(self, attr):
        print(f'Удаляю атрибут {attr}')
        del self.__dict__[attr]   # или object.__delattr__(self, attr)


cat = Cat('Кемаль', 'Британский')
del cat.name
print(cat.__dict__)   # → {'breed': 'Британский'}
```

-----

### `@property` — контроль конкретного атрибута

Если магические методы работают для **всех** атрибутов сразу, то `@property` даёт особую логику для **одного конкретного** атрибута.

```python
class Cat:
    def __init__(self, age):
        self._age = age   # приватный

    @property
    def age(self):        # публичный интерфейс
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Возраст не может быть отрицательным")
        self._age = value


cat = Cat(5)
print(cat.age)   # → 5   (выглядит как атрибут, работает как метод)
cat.age = 3      # → сеттер с валидацией
cat.age = -1     # → ValueError
```

#### Как `@property` связан с `__getattribute__`

```
cat.age
  ↓
__getattribute__("age")
  ↓
ищет "age" в cat.__dict__ → не найдено
  ↓
ищет "age" в Cat.__dict__ → найдено! Это property объект
  ↓
property — это дескриптор (есть __get__)
  ↓
вызывает property.__get__(cat, Cat)
  ↓
выполняется твоя функция → return self._age
  ↓
self._age → снова __getattribute__("_age") → найдено в cat.__dict__ → 5
```

-----

## 📊 Сравнение подходов

|                   |`__getattribute__` |`__getattr__`           |`@property`                    |
|-------------------|-------------------|------------------------|-------------------------------|
|Когда вызывается   |Всегда             |Только если нет атрибута|Для конкретного атрибута       |
|Область действия   |Все атрибуты       |Все несуществующие      |Один атрибут                   |
|Основное применение|Логирование, прокси|Значения по умолчанию   |Валидация, вычисляемые атрибуты|

-----

## ⚠️ Главные ловушки

```python
# ❌ Рекурсия в __getattribute__:
def __getattribute__(self, attr):
    return self.__dict__[attr]   # self.__dict__ → снова __getattribute__!

# ❌ Рекурсия в __setattr__:
def __setattr__(self, attr, value):
    self.attr = value   # снова __setattr__!

# ✅ Всегда используй object.* или self.__dict__ напрямую
```

-----

## 💻 Быстрая шпаргалка

```python
class MyClass:

    # Перехват ЛЮБОГО обращения к атрибуту
    def __getattribute__(self, attr):
        print(f'get: {attr}')
        return object.__getattribute__(self, attr)   # или super().__getattribute__(attr)

    # Запасной план — атрибут не найден
    def __getattr__(self, attr):
        return None   # или raise AttributeError

    # Перехват записи
    def __setattr__(self, attr, value):
        object.__setattr__(self, attr, value)        # или self.__dict__[attr] = value

    # Перехват удаления
    def __delattr__(self, attr):
        object.__delattr__(self, attr)               # или del self.__dict__[attr]


# Встроенные функции (вызывают те же магические методы):
getattr(obj, 'name')           # → obj.name
getattr(obj, 'name', None)     # → obj.name или None если нет
setattr(obj, 'name', 'value')  # → obj.name = 'value'
delattr(obj, 'name')           # → del obj.name
hasattr(obj, 'name')           # → True/False
```

-----

## 🔗 Связанные темы

- [[Дескрипторы **get** **set** **delete**]]
- [[Свойства property() и @property]]
- [[type, object, классы и экземпляры]]
- [[Атрибуты объектов и классов]]

## ❓ Вопросы / Непонятное

- …