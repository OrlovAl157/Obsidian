---
tags:
  - python
  - тема/ооп
  - тема/под_капотом
  - статус/завершён
уровень: продвинутый
---

## 🧩 Проблема на первый взгляд

На первый взгляд звучит парадоксально:

- `type` создаёт `object`
- Но сам `type` наследуется от `object`

Это как будто **курица создала яйцо, а яйцо — создало курицу** 🐣🥚

И всё же это работает! Разберёмся как.

---

## 🧠 1. Что значит «создаёт»

Когда мы пишем:

```python
type(object)
```

и получаем:

```python
<class 'type'>
```

Это значит, что **`object` — это класс**, и этот класс был **создан метаклассом `type`**.

### Ключевая идея:

- В Python **все классы создаются `type`**, даже сам `object`
- `type` отвечает за **"форму"** всех классов — он строит их структуру в памяти Python
- `type` — это **фабрика классов**, конструктор, который делает новые типы

```python
# type создаёт все классы
print(type(int))        # <class 'type'>
print(type(str))        # <class 'type'>
print(type(list))       # <class 'type'>
print(type(object))     # <class 'type'> ← даже object!

# Даже пользовательские классы
class MyClass:
    pass

print(type(MyClass))    # <class 'type'>
```

---

## 🧱 2. Что значит «наследуется»

Когда мы пишем:

```python
issubclass(type, object)
```

и получаем:

```python
True
```

Это значит:

- `type` — **тоже объект** (в Python всё является объектом!)
- У `type` есть всё то, что присуще любому объекту: `__str__`, `__repr__`, `__hash__` и т.д.
- Поэтому `type` **наследуется от `object`** — как и всё остальное в Python

```python
# type наследуется от object
print(isinstance(type, object))     # True
print(issubclass(type, object))     # True

# У type есть методы от object
print(hasattr(type, '__str__'))     # True
print(hasattr(type, '__repr__'))    # True
print(hasattr(type, '__hash__'))    # True
```

---

## ⚙️ 3. Как это сочетается — пошаговая логика

Вот как Python решает эту головоломку:

### Шаг 1️⃣

**Python должен иметь "базовую форму" для всех вещей**

- Это `object`
- `object` описывает, что значит **быть объектом**
- Даёт базовые методы: `__init__`, `__str__`, `__repr__`, `__eq__` и т.д.

### Шаг 2️⃣

**Но кто создаёт "форму" самого `object`?**

- Для этого нужен **создатель классов** → `type`
- `type` — это метакласс, фабрика классов
- `type` создаёт `object` (и все остальные классы)

### Шаг 3️⃣

**Но сам `type` — тоже объект!**

- Ведь **всё в Python — объект**
- Значит, `type` должен иметь базовые свойства объекта
- Поэтому `type` **наследуется от `object`**

### Шаг 4️⃣

**Чтобы не было бесконечной цепочки...**

- Python **замыкает круг**: `type(type) == type`
- `type` создаёт сам себя!
- Это специальное исключение в системе типов Python

```python
# Доказательство замкнутого круга
print(type(object))             # <class 'type'> ← type создаёт object
print(type(type))               # <class 'type'> ← type создаёт сам себя!
print(isinstance(type, object)) # True ← type наследуется от object
print(isinstance(object, type)) # True ← object является экземпляром type
```

---

## 🔁 Схема взаимосвязи

```
    type ──────создаёт──────> object
     ↑                           │
     │                           │
     └───────наследует───────────┘
     
    И ещё:
    type ──────создаёт──────> type (сам себя!)
```

### В виде кода:

```python
# Круг замыкается:
print("1. type создаёт object:")
print(f"   type(object) = {type(object)}")              # type

print("\n2. type создаёт сам себя:")
print(f"   type(type) = {type(type)}")                  # type

print("\n3. type наследуется от object:")
print(f"   isinstance(type, object) = {isinstance(type, object)}")   # True

print("\n4. object создан type:")
print(f"   isinstance(object, type) = {isinstance(object, type)}")   # True

print("\n🔁 Замкнутый круг!")
```

---

## 🧩 4. Упрощённая аналогия

Представь завод по производству форм для печенья:

### `object` — это базовая форма

- Это **"формат"** любой вещи
- У всех есть имя, можно напечатать, можно сравнить и т.д.
- Это минимальный набор свойств, который есть у всего

### `type` — это станок

- Это **"станок"**, который делает новые формы (классы)
- Он создаёт `int`, `str`, `list`, и даже сам `object`

### Парадокс:

Но **станок сам — тоже вещь!**

- Чтобы станок имел форму и свойства, он тоже должен быть сделан "по чертежу"
- А базовый чертеж для всех вещей — это `object`

### Поэтому:

✅ `type` **создаёт** все классы, включая `object`  
✅ Но чтобы сам `type` существовал как объект, он должен **наследовать** `object`  
✅ И чтобы не уйти в бесконечность, `type` **создаёт сам себя**

---

## 💻 Живые примеры в коде

### Пример 1: Цепочка создания

```python
# Создадим свой класс
class Dog:
    def bark(self):
        return "Woof!"

# Создадим экземпляр
my_dog = Dog()

# Теперь посмотрим на цепочку:
print("Экземпляр → Класс → Метакласс → ...?")
print(f"type(my_dog) = {type(my_dog)}")           # <class 'Dog'>
print(f"type(Dog) = {type(Dog)}")                 # <class 'type'>
print(f"type(type) = {type(type)}")               # <class 'type'> ← круг!

print("\nА теперь через isinstance:")
print(f"isinstance(my_dog, Dog) = {isinstance(my_dog, Dog)}")         # True
print(f"isinstance(my_dog, object) = {isinstance(my_dog, object)}")   # True
print(f"isinstance(Dog, type) = {isinstance(Dog, type)}")             # True
print(f"isinstance(type, object) = {isinstance(type, object)}")       # True
```

### Пример 2: Все дороги ведут к object

```python
# Проверим разные типы
things = [5, "hello", [1, 2, 3], {'a': 1}, lambda x: x, Dog]

print("Всё в Python — это object:")
for thing in things:
    print(f"isinstance({thing!r:20s}, object) = {isinstance(thing, object)}")

# Вывод:
# isinstance(5                 , object) = True
# isinstance('hello'           , object) = True
# isinstance([1, 2, 3]         , object) = True
# isinstance({'a': 1}          , object) = True
# isinstance(<lambda>          , object) = True
# isinstance(<class 'Dog'>     , object) = True
```

### Пример 3: Все классы созданы type

```python
# Проверим типы классов
classes = [int, str, list, dict, Dog, object, type]

print("Все классы созданы type:")
for cls in classes:
    print(f"type({cls.__name__:10s}) = {type(cls)}")

# Вывод:
# type(int       ) = <class 'type'>
# type(str       ) = <class 'type'>
# type(list      ) = <class 'type'>
# type(dict      ) = <class 'type'>
# type(Dog       ) = <class 'type'>
# type(object    ) = <class 'type'>
# type(type      ) = <class 'type'>  ← и даже type!
```

### Пример 4: Создание класса через type

```python
# Обычное создание класса
class Cat:
    sound = "Meow"
    
    def make_sound(self):
        return self.sound

# Создание через type (эквивалентно)
Dog = type('Dog', (), {
    'sound': 'Woof',
    'make_sound': lambda self: self.sound
})

# Они работают одинаково
cat = Cat()
dog = Dog()

print(cat.make_sound())     # Meow
print(dog.make_sound())     # Woof

# И оба созданы type
print(type(Cat))            # <class 'type'>
print(type(Dog))            # <class 'type'>
```

---

## 🎯 Практическое понимание

### Что это даёт нам?

1. **Единообразие**: всё в Python — объекты, даже классы и функции
2. **Рефлексия**: можем проверять типы во время выполнения
3. **Метапрограммирование**: можем создавать классы динамически
4. **Гибкость**: можем менять поведение классов через метаклассы

### Когда это важно?

В обычной разработке редко нужно думать об этом напрямую, но понимание помогает:

- При создании фреймворков
- При использовании декораторов классов
- При работе с метаклассами (ORM, валидация и т.д.)
- При отладке сложных иерархий классов

---

## 📝 Резюме

### Ключевые правила:

1. **`object`** — базовый класс для **всех объектов**
    
    - Даёт минимальный набор методов
    - От него наследуется всё
2. **`type`** — метакласс, создающий **все классы**
    
    - Создаёт `int`, `str`, `list`, даже `object`
    - Даже создаёт сам себя!
3. **Замкнутый круг:**
    
    - `type` создаёт `object`
    - `type` наследуется от `object`
    - `type` создаёт сам себя
    - Это специальное исключение в Python, сделанное разработчиками языка

### Практический вывод:

```python
# Запомни эти 4 строки:
type(object)             # <class 'type'>    ← type создаёт object
type(type)               # <class 'type'>    ← type создаёт себя
isinstance(type, object) # True              ← type наследует object
isinstance(object, type) # True              ← object создан type

# Они описывают фундамент Python! 🐍
```

---

## ⚠️ Важное уточнение

В реальности, конечно, это не курица и яйцо — это не биология.

Разработчики Python (Гвидо ван Россум и команда) **специально реализовали эту систему** в коде интерпретатора на уровне C. Они создали это «замыкание» вручную:

- Сначала в памяти создаётся структура для `type`
- Затем структура для `object`
- Потом устанавливаются связи между ними
- И всё это происходит при запуске интерпретатора Python

Так что это не магия, а **архитектурное решение** для создания элегантной и единообразной системы типов!

## 🔗 Связанные темы

- [[Всё в Python — объект]]
- [[02 — 🧬 type, object, классы и экземпляры]]
- [[Обьекты в Python  классы и мета классы]]
