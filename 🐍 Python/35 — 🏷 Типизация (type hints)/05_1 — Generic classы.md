-----

## tags: [python, типизация, generic, класс, typevar]
difficulty: advanced

# 🏗️ Generic классы

> **Generic классы** — классы, параметризованные типами. Например, `Container[T]` — контейнер, который хранит элементы типа T. Generic классы позволяют писать универсальные классы, которые работают с любыми типами, сохраняя информацию о типе. Это расширение TypeVar на уровень классов.

## Содержание

- [[#Справка|Справка]]
- [[#🔧 Базовый Generic класс|Базовый Generic класс]]
- [[#📦 Generic с несколькими параметрами|Generic с несколькими параметрами]]
- [[#🎯 Generic наследование|Generic наследование]]
- [[#💻 Примеры|Примеры]]
- [[#⚠️ Частые ошибки|Частые ошибки]]

-----

## Справка

|Что                     |Синтаксис                   |Пример                     |
|------------------------|----------------------------|---------------------------|
|**Generic класс**       |`class MyClass(Generic[T]):`|`class Box(Generic[T])`    |
|**Несколько параметров**|`Generic[T, U]`             |`class Pair(Generic[T, U])`|
|**Использование**       |`Box[int]`                  |`box: Box[int] = Box()`    |

-----

## 🔧 Базовый Generic класс

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Box(Generic[T]):
    """Контейнер, хранящий один элемент типа T"""
    
    def __init__(self):
        self.item: T | None = None
    
    def put(self, item: T) -> None:
        """Положить элемент в коробку"""
        self.item = item
    
    def get(self) -> T | None:
        """Получить элемент из коробки"""
        return self.item

# Использование
int_box: Box[int] = Box()
int_box.put(42)
value = int_box.get()  # int | None

str_box: Box[str] = Box()
str_box.put("hello")
message = str_box.get()  # str | None
```

-----

## 📦 Generic с несколькими параметрами

```python
from typing import Generic, TypeVar

K = TypeVar('K')
V = TypeVar('V')

class Pair(Generic[K, V]):
    """Пара ключ-значение"""
    
    def __init__(self, key: K, value: V):
        self.key = key
        self.value = value
    
    def get_key(self) -> K:
        return self.key
    
    def get_value(self) -> V:
        return self.value

# Использование
pair: Pair[str, int] = Pair("age", 30)
key = pair.get_key()    # str
value = pair.get_value()  # int
```

-----

## 🎯 Generic наследование

Generic класс может наследоваться, и наследник может быть Generic или конкретным:

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Stack(Generic[T]):
    """Стек элементов"""
    
    def __init__(self):
        self.items: list[T] = []
    
    def push(self, item: T) -> None:
        self.items.append(item)
    
    def pop(self) -> T | None:
        return self.items.pop() if self.items else None

# Generic подкласс
U = TypeVar('U')

class LimitedStack(Stack[U], Generic[U]):
    """Стек с лимитом"""
    
    def __init__(self, max_size: int):
        super().__init__()
        self.max_size = max_size
    
    def push(self, item: U) -> bool:
        if len(self.items) < self.max_size:
            super().push(item)
            return True
        return False

# Конкретный подкласс
class IntStack(Stack[int]):
    """Стек целых чисел"""
    pass

# Использование
stack: LimitedStack[str] = LimitedStack(5)
stack.push("hello")
stack.push("world")

int_stack: IntStack = IntStack()
int_stack.push(1)
int_stack.push(2)
```

-----

## 💻 Примеры

### Пример 1: Generic хранилище данных

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Cache(Generic[T]):
    """Простой кэш для данных"""
    
    def __init__(self):
        self.data: dict[str, T] = {}
    
    def set(self, key: str, value: T) -> None:
        self.data[key] = value
    
    def get(self, key: str) -> T | None:
        return self.data.get(key)
    
    def clear(self) -> None:
        self.data.clear()

# Использование
int_cache: Cache[int] = Cache()
int_cache.set("answer", 42)
answer = int_cache.get("answer")  # int | None

user_cache: Cache[dict[str, str]] = Cache()
user_cache.set("user1", {"name": "Alice", "email": "alice@example.com"})
user = user_cache.get("user1")  # dict[str, str] | None
```

### Пример 2: Generic очередь

```python
from typing import Generic, TypeVar

T = TypeVar('T')

class Queue(Generic[T]):
    """Очередь FIFO"""
    
    def __init__(self):
        self.items: list[T] = []
    
    def enqueue(self, item: T) -> None:
        """Добавить элемент в конец"""
        self.items.append(item)
    
    def dequeue(self) -> T | None:
        """Получить элемент из начала"""
        return self.items.pop(0) if self.items else None
    
    def is_empty(self) -> bool:
        return len(self.items) == 0

# Использование
int_queue: Queue[int] = Queue()
int_queue.enqueue(1)
int_queue.enqueue(2)
int_queue.enqueue(3)

first = int_queue.dequeue()  # 1 (int)
second = int_queue.dequeue()  # 2 (int)
```

### Пример 3: Generic репозиторий

```python
from typing import Generic, TypeVar

T = TypeVar('T')
ID = TypeVar('ID')

class Repository(Generic[T, ID]):
    """Репозиторий для сохранения объектов"""
    
    def __init__(self):
        self.items: dict[ID, T] = {}
    
    def save(self, id: ID, item: T) -> None:
        self.items[id] = item
    
    def find(self, id: ID) -> T | None:
        return self.items.get(id)
    
    def delete(self, id: ID) -> bool:
        if id in self.items:
            del self.items[id]
            return True
        return False

# Использование
class User:
    def __init__(self, name: str):
        self.name = name

user_repo: Repository[User, int] = Repository()
user_repo.save(1, User("Alice"))
user_repo.save(2, User("Bob"))

user = user_repo.find(1)  # User | None
```

-----

## ⚠️ Частые ошибки

```python
from typing import Generic, TypeVar

# ❌ Забыли Generic в наследовании
T = TypeVar('T')

class Box:  # ❌ Не Generic!
    def __init__(self, item: T) -> None:
        self.item = item

# ✅ Правильно
class Box(Generic[T]):
    def __init__(self, item: T) -> None:
        self.item = item

# ❌ Использование Generic без типаривов
class MyClass(Generic):  # ❌ Нужны параметры!
    pass

# ✅ Правильно
class MyClass(Generic[T]):
    pass

# ❌ Забыли указать тип при создании
box: Box = Box(42)  # ❌ Потеряется информация о типе

# ✅ Правильно
box: Box[int] = Box(42)
```

-----

## ✅ Главные правила

✅ **Generic классы наследуются от Generic[T]** — это обязательно  
✅ **Несколько параметров OK** — `Generic[T, U, V]`  
✅ **Всегда указывайте тип при использовании** — `Box[int]`, не просто `Box`  
✅ **Сохраняется информация о типе** — mypy будет проверять операции  
✅ **Наследование Generic’ов возможно** — подклассы могут быть Generic или конкретными

-----

## 🔗 Связанные темы

- [[05 — TypeVar]]
- [[02 — 📦 Generics и контейнеры]]
- [[06 — 📋 Протоколы и абстрактные классы]]