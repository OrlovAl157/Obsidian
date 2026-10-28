---

tags:

- python
- тема/ООП
- тема/метаклассы
- статус/завершён

---

# 🏭 Создание класса — `type.__call__`

## 📌 Коротко

> Каждый класс в Python — это объект типа `type`. Когда Python видит `class Cat:` — он вызывает `type.__call__` который запускает `type.__new__` (создаёт класс) и `type.__init__` (инициализирует). Всё в Python создаётся по одному механизму.

---

## 🔢 Что происходит когда Python видит `class Cat:`

```
class Cat:
    def sound(self):
        return 'мяу'
  │
  ▼
Python собирает тело класса в словарь
namespace = {'sound': <function sound>}
  │
  ▼
type.__call__(type, 'Cat', (object,), namespace)
  │
  ├── type.__new__(type, 'Cat', (object,), namespace)
  │     → выделяет память для класса
  │     → создаёт объект типа type
  │     → записывает имя, базовые классы, методы
  │
  ├── type.__init__(Cat, 'Cat', (object,), namespace)
  │     → финальная инициализация класса
  │
  └── возвращает готовый класс Cat
  │
  ▼
имя 'Cat' в пространстве имён
привязывается к объекту класса
```

---

## 🔍 Создание класса вручную

```python
# Эти два способа эквивалентны:

# Способ 1 — через синтаксис class
class Cat:
    def sound(self):
        return 'мяу'

# Способ 2 — через type напрямую
Cat = type('Cat', (object,), {'sound': lambda self: 'мяу'})

cat = Cat()
print(cat.sound())   # → мяу
```

---

## ⚖️ Создание класса vs создание экземпляра

```
Создание экземпляра:          Создание класса:
Cat('Кемаль')                 class Cat: ...
    ↓                             ↓
type.__call__(Cat)             type.__call__(type)
    ↓                             ↓
Cat.__new__(Cat)               type.__new__(type)
Cat.__init__(obj)              type.__init__(cls)
    ↓                             ↓
экземпляр Cat                  класс Cat
```

> Всё в Python создаётся по одному принципу — `__new__` + `__init__`!

---

## 📖 Теория

### Как создаётся `object`

`object` — самый базовый класс. Он создаётся **на уровне интерпретатора CPython**, то есть на C, не на Python.

```
Запуск CPython
    ↓
C-код создаёт object и type одновременно
    ↓
type  — метакласс для всего
object — базовый класс для всего
    ↓
дальше уже запускается Python код
```

> Нельзя сказать "object создаётся через type.**call**" — это уровень ниже Python, это C. `object` и `type` — фундамент, созданный до запуска любого Python кода.

### `type` и `object` — курица и яйцо

```python
print(type(object))              # → <class 'type'>   — object создан через type
print(type(type))                # → <class 'type'>   — type создан через себя!

print(issubclass(type, object))  # → True  — type наследует object
print(issubclass(object, type))  # → False
```

- `type` является экземпляром самого себя
- `type` наследует от `object`
- `object` создан через `type`

### Все классы — экземпляры `type`

```python
print(type(int))    # → <class 'type'>
print(type(str))    # → <class 'type'>
print(type(list))   # → <class 'type'>

class Cat:
    pass

print(type(Cat))    # → <class 'type'>
```

### Методы `type`

|Метод|Назначение|
|---|---|
|`__call__`|вызывается при `Cat()` — запускает создание экземпляра|
|`__new__`|переопределён от `object` — создаёт класс|
|`__init__`|переопределён от `object` — инициализирует класс|

> `__call__` — собственный метод `type`. `__new__` и `__init__` — **переопределены** от `object` под создание классов.

### `type.__new__` vs `type.__init__`

||`type.__new__`|`type.__init__`|
|---|---|---|
|Принимает|`mcs, name, bases, namespace`|`cls, name, bases, namespace`|
|Роль|создаёт объект класса в памяти|финальная инициализация|
|Аналог|`object.__new__` для экземпляров|`object.__init__` для экземпляров|

---

## 🗃 Что хранит созданный класс

```python
class Cat:
    sound = 'мяу'
    def info(self):
        return self.sound

print(Cat.__name__)    # → 'Cat'
print(Cat.__bases__)   # → (<class 'object'>,)
print(Cat.__dict__)    # → {'sound': 'мяу', 'info': <function>, ...}
```

---

## 💻 Примеры

### Создание класса через `type` с наследованием

```python
class Animal:
    def __init__(self, name):
        self.name = name

# создаём Cat наследующий Animal
Cat = type('Cat', (Animal,), {
    'sound': lambda self: 'мяу',
    'info': lambda self: f'{self.name} говорит {self.sound()}'
})

cat = Cat('Кемаль')
print(cat.info())   # → Кемаль говорит мяу
```

### Метакласс — свой `type`

```python
# Можно создать свой метакласс — наследник type
class MyMeta(type):
    def __new__(mcs, name, bases, namespace):
        print(f'Создаётся класс {name}')
        return super().__new__(mcs, name, bases, namespace)

class Cat(metaclass=MyMeta):   # используем свой метакласс
    pass
# → Создаётся класс Cat
```

---

## 🔗 Связанные темы

- [[Порядок создания экземпляра — type.**call**]]
- [[type, object, классы и экземпляры]]
- [[Магические методы **new** и **init**]]