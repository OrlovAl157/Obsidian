---
tags: python, oop, decorators
уровень: средний
статус: готово
раздел: декораторы
---

# 🧩 Декораторы — часть 1 (ПОЛНАЯ ТЕОРИЯ + разбор limiter)

---

# 📚 Декораторы

Декораторы являются инструментом, позволяющим расширять функциональность функций без изменения их кода.

---

## Простой декоратор

```python
import functools

def do_twice(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        for _ in range(2):
            value = func(*args, **kwargs)
        return value
    return wrapper
```

📌 Важно:
- принимает функцию
- возвращает новую функцию
- использует wraps для сохранения метаданных

---

## Ручное применение

```python
greet = do_twice(greet)
```

---

## Декоратор с аргументами

```python
def do_n_times(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(n):
                value = func(*args, **kwargs)
            return value
        return wrapper
    return decorator
```

📌 Раскрытие:

```
@do_n_times(4)
def f():
    pass

↓

f = do_n_times(4)(f)
```

---

# 📚 Декорирование методов

```python
class Cat:
    @do_n_times(4)
    def say(self):
        print('Мяу')
```

📌 Работает так же, как и с функциями.

---

# 📚 Классы-декораторы

```python
class do_twice:
    def __init__(self, func):
        self.func = func

    def __call__(self, *args, **kwargs):
        for _ in range(2):
            value = self.func(*args, **kwargs)
        return value
```

📌 Ключевая идея:
- объект становится вызываемым (__call__)
- хранит функцию внутри

---

## Сохранение метаданных

```python
import functools

functools.update_wrapper(self, func)
```

---

# 📚 Класс-декоратор с аргументами

```python
class do_n_times:
    def __init__(self, n):
        self.n = n

    def __call__(self, func):
        def wrapper(*args, **kwargs):
            for _ in range(self.n):
                value = func(*args, **kwargs)
            return value
        return wrapper
```

📌 Логика:
- __init__ принимает аргументы
- __call__ принимает функцию

---

# 📚 Декораторы классов

```python
@decorator
class A:
    pass

A = decorator(A)
```

📌 Особенность:
- чаще модифицируем класс и возвращаем его

---

# 🔥 Разбор limiter

```python
def limiter(limit, unique, lookup):
    def wrapper(cls):
        cls._limit = limit
        cls._instances = {}
        cls._lookup = lookup

        def wrapper2(*args, **kwaegs):
            obj = cls(*args, **kwaegs)

            if getattr(obj, unique) in cls._instances:
                return cls._instances[getattr(obj, unique)]
            elif len(cls._instances) < cls._limit:
                cls._instances[getattr(obj, unique)] = obj
                return obj
            else:
                return cls._instances[list(cls._instances)[0 if cls._lookup == 'FIRST' else -1]]

        return wrapper2

    return wrapper
```

---

# ⚙️ Полная схема

```
@limiter(...)
class User
  │
  ▼
User = limiter(...)(User)
  │
  ▼
limiter(...) → wrapper
  │
  ▼
wrapper(User)
  │
  ▼
cls = User
  │
  ▼
wrapper возвращает wrapper2
  │
  ▼
User = wrapper2
```

---

# 🚀 Вызов

```
User(...) → wrapper2(...) → cls(...)
```

---

# 🧠 Логика limiter

1. Проверка дубликата через getattr
2. Проверка лимита
3. Возврат первого или последнего объекта

---

# 🧠 Ключевые идеи

- декоратор может заменить класс
- используется замыкание
- cls хранит оригинальный класс
- реализован паттерн multiton

---

# ⚠️ Важно

- декораторы добавляют уровень вызова
- могут влиять на производительность
- без wraps теряются метаданные

---

# 🧪 Проверка себя

1. Почему User перестаёт быть классом?
2. Где хранится оригинальный класс?
3. Почему getattr используется вместо obj.attr?
