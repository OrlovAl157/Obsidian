
#ооп #python

---

## Методы экземпляра

Если атрибуты описывают **состояние** объекта, то методы определяют его **поведение**. Метод экземпляра — это функция, определённая внутри класса, которая принадлежит объекту и имеет доступ к его атрибутам.

```python
class Cat:
    def say(self):
        print('Мяу')

    def eat(self):
        print('Мням')


cat = Cat()
cat.say()    # → Мяу
cat.eat()    # → Мням
```

Методы экземпляра доступны **всем** объектам класса — можно создать сколько угодно экземпляров, и у каждого будет одинаковый набор методов:

```python
cat1 = Cat()
cat2 = Cat()

cat1.say()   # → Мяу
cat2.eat()   # → Мням
```

**Два обязательных правила при создании метода:**

- метод определяется внутри класса (с отступом)
- первый параметр метода **всегда** называется `self`

> 💡 Названия методов — глаголы в `snake_case`: `get_name`, `add_coins`, `set_value`.

---

## Параметр self

`self` — это ссылка на конкретный объект, через который вызывается метод. Python передаёт его **автоматически** — мы не передаём `self` вручную при вызове.

```python
class Cat:
    def say(self):
        print('Мяу')

cat = Cat()

# Эти два вызова полностью эквивалентны:
cat.say()        # привычная запись
Cat.say(cat)     # то, во что Python преобразует вызов выше
```

То есть при вызове `cat.say()` Python сам подставляет `cat` в качестве аргумента `self`. Именно поэтому `self` — это всегда первый параметр.

> 💡 `self` часто называют **контекстным объектом**. Это самое строгое соглашение в Python — называть его иначе технически можно, но другие программисты просто не поймут твой код.

Это работает не только с пользовательскими классами — так устроены все объекты в Python:

```python
nums = [1, 2, 3]
text = 'hello'

# Эти записи эквивалентны:
nums.append(4)        →    list.append(nums, 4)
text.lower()          →    str.lower(text)

# Это удобно использовать с map():
names = ['timur', 'arthur', 'dima']
print(*map(str.title, names))   # → Timur Arthur Dima
```

---

## Методы с аргументами

Методы — это функции, поэтому они могут принимать любые дополнительные аргументы помимо `self`.

```python
class Cat:
    def say(self, sound):
        print(sound)

    def eat(self, meal):
        print(f'{meal} - это очень вкусно!')


cat1 = Cat()
cat2 = Cat()

cat1.say('Мяу')       # → Мяу
cat1.eat('Молоко')    # → Молоко - это очень вкусно!

cat2.say('Мяяяу!')    # → Мяяяу!
cat2.eat('Рыба')      # → Рыба - это очень вкусно!
```

---

## Метод **init**()

### Зачем он нужен?

Без `__init__` после создания объекта у него нет никаких атрибутов — их приходится задавать вручную для каждого объекта. Это неудобно и ненадёжно.

```python
# ❌ Без __init__ — задаём атрибуты вручную каждый раз
class Cat:
    pass

cat1 = Cat()
cat1.name = 'Кемаль'
cat1.breed = 'Британский'

cat2 = Cat()
cat2.name = 'Роджер'
# забыли задать breed — у cat2 его нет, возникнет ошибка при обращении
```

Метод `__init__()` решает эту проблему — он **автоматически** вызывается сразу после создания объекта и инициализирует его атрибуты.

```python
# ✅ С __init__ — атрибуты задаются при создании объекта
class Cat:
    def __init__(self, breed, name):
        self.breed = breed   # порода кошки
        self.name = name     # имя кошки


cat1 = Cat('Британский', 'Кемаль')
cat2 = Cat('Манчкин', 'Роджер')

print(cat1.breed, cat1.name)   # → Британский Кемаль
print(cat2.breed, cat2.name)   # → Манчкин Роджер
```

> ⚠️ `__init__` **не создаёт** объект — он лишь инициализирует уже созданный. Создание объекта — задача метода `__new__()` (об этом в отдельной теме).

---

### Аргументы обязательны

Если в `__init__` определены параметры без значений по умолчанию — их нужно передавать при создании объекта:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name


cat = Cat()   # ❌ TypeError: missing 2 required positional arguments: 'breed' and 'name'
```

---

### Значения по умолчанию

Сигнатура `__init__` полностью повторяет сигнатуру обычной функции — можно задавать значения по умолчанию, использовать `*args` и `**kwargs`.

```python
class Cat:
    def __init__(self, breed, name=None):
        self.breed = breed
        self.name = name


cat1 = Cat('Британский', 'Кемаль')
cat2 = Cat('Манчкин')               # name не указан — будет None

print(cat1.breed, cat1.name)   # → Британский Кемаль
print(cat2.breed, cat2.name)   # → Манчкин None
```

---

### Фиксированные атрибуты в **init**

Помимо аргументов, в `__init__` можно задавать атрибуты с фиксированными значениями — они будут одинаковы у всех объектов:

```python
class Cat:
    def __init__(self, breed, name):
        self.breed = breed
        self.name = name
        self.night_vision = True   # у всех кошек — всегда True


cat1 = Cat('Британский', 'Кемаль')
cat2 = Cat('Манчкин', 'Роджер')

print(cat1.night_vision)   # → True
print(cat2.night_vision)   # → True
```

> 💡 Разница с атрибутом класса: здесь `night_vision` записывается в `__dict__` каждого экземпляра отдельно, а не в `__dict__` класса.

---

### Важные ограничения **init**

**1. `__init__` не должен ничего возвращать** — только `None`, иначе будет ошибка:

```python
class Cat:
    def __init__(self, name):
        self.name = name
        return self   # ❌ TypeError: __init__() should return None, not 'Cat'
```

**2. У класса может быть только один `__init__`** — если определить два, второй просто перезапишет первый:

```python
class Cat:
    def __init__(self, breed):      # этот будет перезаписан
        self.breed = breed

    def __init__(self, breed, name):  # будет использован этот
        self.breed = breed
        self.name = name


cat = Cat('Британский', 'Кемаль')
print(cat.breed, cat.name)   # → Британский Кемаль
```

---

## Пример проектирования класса — PiggyBank

Хороший способ понять методы и `__init__` — проследить, как класс развивается шаг за шагом.

### Шаг 1 — минимальный класс

```python
class PiggyBank:
    def __init__(self, balance=0):
        self.balance = balance   # количество монет, по умолчанию 0
```

### Шаг 2 — добавляем методы

```python
class PiggyBank:
    def __init__(self, balance=0):
        self.balance = balance

    def add_coins(self, coins):
        self.balance += coins

    def remove_coins(self, coins):
        self.balance -= coins


piggybank = PiggyBank()

piggybank.add_coins(4)
piggybank.add_coins(3)
print(piggybank.balance)    # → 7

piggybank.remove_coins(2)
piggybank.remove_coins(2)
print(piggybank.balance)    # → 3
```

### Шаг 3 — добавляем валидацию (через print)

Проблема: можно добавить больше монет чем влезает, и вынуть больше чем есть.

```python
class PiggyBank:
    def __init__(self, balance=0, volume=400):
        self.balance = balance
        self.volume = volume       # максимальная вместимость

    def add_coins(self, coins):
        if self.balance + coins > self.volume:
            print('Копилка слишком мала')
        else:
            self.balance += coins

    def remove_coins(self, coins):
        if self.balance - coins < 0:
            print('В копилке недостаточно монет')
        else:
            self.balance -= coins


piggybank = PiggyBank(0, 10)

piggybank.remove_coins(20)   # → В копилке недостаточно монет
piggybank.add_coins(20)      # → Копилка слишком мала
print(piggybank.balance)     # → 0
```

### Шаг 4 — заменяем print на исключения (правильный подход)

```python
class PiggyBank:
    def __init__(self, balance=0, volume=400):
        self.balance = balance
        self.volume = volume

    def add_coins(self, coins):
        if self.balance + coins > self.volume:
            raise ValueError('Копилка слишком мала')
        self.balance += coins

    def remove_coins(self, coins):
        if self.balance - coins < 0:
            raise ValueError('В копилке недостаточно монет')
        self.balance -= coins
```

> 💡 Возбуждение исключения — стандартная практика при некорректных данных. Это лучше чем `print`, потому что исключение нельзя проигнорировать — оно остановит программу и сообщит об ошибке.

---

## Быстрая шпаргалка

```python
class Cat:
    def __init__(self, breed, name='Без имени'):
        # self — ссылка на создаваемый объект
        self.breed = breed           # атрибут из аргумента
        self.name = name             # атрибут со значением по умолчанию
        self.night_vision = True     # фиксированный атрибут

    def describe(self):              # метод без доп. аргументов
        print(f'{self.name} — {self.breed}')

    def say(self, sound='Мяу'):      # метод с доп. аргументом
        print(f'{self.name}: {sound}')


cat = Cat('Британский', 'Кемаль')
cat.describe()        # → Кемаль — Британский
cat.say()             # → Кемаль: Мяу
cat.say('Мяяяу!')     # → Кемаль: Мяяяу!

# Вызов через класс (эквивалентно):
Cat.describe(cat)     # → Кемаль — Британский
```

|Что|Как|
|---|---|
|Определить метод|`def method(self, ...)` внутри класса|
|Вызвать метод|`obj.method(args)`|
|Инициализатор|`def __init__(self, ...)`|
|Значение по умолчанию|`def __init__(self, name=None)`|
|`__init__` возвращает|всегда `None`|
|Количество `__init__`|только один|