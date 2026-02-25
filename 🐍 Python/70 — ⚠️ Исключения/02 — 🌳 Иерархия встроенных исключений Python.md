---
tags:
  - python
  - тема/исключения
  - статус/завершён
---

# 🌳 Иерархия встроенных исключений Python

## 📌 Коротко

> Все исключения в Python организованы в иерархическую структуру с корневым классом `BaseException`. Понимание иерархии позволяет правильно перехватывать и обрабатывать исключения, используя их наследственные отношения.

---

## 💡 Полезные советы

- **BaseException — это корень иерархии**, не используйте его для перехвата обычных исключений
- **Exception — это базовый класс для большинства ошибок**, используйте его для общего перехвата
- **Наследование работает с `except`** — если перехватить родителя, поймаются и все дети
- **Порядок `except` блоков важен** — от дочерних классов к родительским
- **Используйте конкретные исключения** — это делает код более понятным и безопасным

---

## 🚀 Быстрая справка

| Группа | Примеры | Когда использовать |
|---|---|---|
| **ArithmeticError** | ZeroDivisionError, OverflowError | Математические ошибки |
| **LookupError** | IndexError, KeyError | Ошибки поиска элементов |
| **OSError** | FileNotFoundError, PermissionError | Ошибки файловой системы и сети |
| **ValueError** | Неправильное значение | Неверное значение (правильный тип) |
| **TypeError** | Неправильный тип | Неверный тип данных |
| **NameError** | Имя не определено | Переменная не определена |
| **AttributeError** | Атрибут не найден | Несуществующее свойство объекта |

---

## 📖 Теория

## 🌳 Полное дерево встроенных исключений

```
BaseException
 ├── SystemExit
 ├── KeyboardInterrupt
 ├── GeneratorExit
 └── Exception
      ├── StopIteration
      ├── StopAsyncIteration
      ├── ArithmeticError
      │    ├── FloatingPointError
      │    ├── OverflowError
      │    └── ZeroDivisionError
      ├── AssertionError
      ├── AttributeError
      ├── BufferError
      ├── EOFError
      ├── ImportError
      │    └── ModuleNotFoundError
      ├── LookupError
      │    ├── IndexError
      │    └── KeyError
      ├── MemoryError
      ├── NameError
      │    └── UnboundLocalError
      ├── OSError
      │    ├── BlockingIOError
      │    ├── ChildProcessError
      │    ├── ConnectionError
      │    │    ├── BrokenPipeError
      │    │    ├── ConnectionAbortedError
      │    │    ├── ConnectionRefusedError
      │    │    └── ConnectionResetError
      │    ├── FileExistsError
      │    ├── FileNotFoundError
      │    ├── InterruptedError
      │    ├── IsADirectoryError
      │    ├── NotADirectoryError
      │    ├── PermissionError
      │    ├── ProcessLookupError
      │    └── TimeoutError
      ├── ReferenceError
      ├── RuntimeError
      │    ├── NotImplementedError
      │    └── RecursionError
      ├── SyntaxError
      │    └── IndentationError
      │         └── TabError
      ├── SystemError
      ├── TypeError
      ├── ValueError
      │    └── UnicodeError
      │         ├── UnicodeDecodeError
      │         ├── UnicodeEncodeError
      │         └── UnicodeTranslateError
      └── Warning
           ├── DeprecationWarning
           ├── PendingDeprecationWarning
           ├── RuntimeWarning
           ├── SyntaxWarning
           ├── UserWarning
           ├── FutureWarning
           ├── ImportWarning
           ├── UnicodeWarning
           └── BytesWarning
```

---

## 💡 Ключевые концепции

### 1. Все исключения — это классы

Каждое исключение в Python является классом, унаследованным от `BaseException`.

```python
# Это классы:
IndexError
ValueError
TypeError
```

### 2. Выброс исключения создаёт экземпляр класса

Когда вы используете `raise`, создаётся объект (экземпляр) класса исключения:

```python
raise IndexError("Индекс вне диапазона")  # Создаётся экземпляр IndexError
```

### 3. Наследование работает через isinstance()

Экземпляр исключения принадлежит всем родительским классам:

```python
try:
    raise IndexError()
except LookupError:  # Поймает, т.к. IndexError наследуется от LookupError
    print("Поймано!")
```

### 4. BaseException — корень всего

Все исключения наследуются от `BaseException`, но большинство пользовательских исключений должны наследоваться от `Exception`.

---

## 📋 Описание основных групп исключений

### BaseException (корневой класс)

**Специальные исключения системного уровня:**

- `SystemExit` — выход из программы (вызов `sys.exit()`)
- `KeyboardInterrupt` — прерывание с клавиатуры (Ctrl+C)
- `GeneratorExit` — завершение генератора

⚠️ **Важно:** Обычно эти исключения **не перехватывают** в коде приложения.

---

### Exception (базовый класс для обычных исключений)

#### ArithmeticError — ошибки арифметических операций

- `ZeroDivisionError` — деление на ноль
- `OverflowError` — результат слишком большой для представления
- `FloatingPointError` — ошибка операции с плавающей точкой

```python
10 / 0  # ZeroDivisionError
```

#### LookupError — ошибки поиска/индексации

- `IndexError` — индекс вне диапазона
- `KeyError` — ключ не найден в словаре

```python
[1, 2, 3][10]  # IndexError
{'a': 1}['b']  # KeyError
```

#### OSError — ошибки операционной системы

Большая группа ошибок файловой системы и сети:

- `FileNotFoundError` — файл не найден
- `PermissionError` — недостаточно прав
- `ConnectionError` — проблемы с сетевым соединением
  - `ConnectionRefusedError` — соединение отклонено
  - `ConnectionResetError` — соединение сброшено

```python
open('несуществующий_файл.txt')  # FileNotFoundError
```

#### ValueError — неправильное значение

Возникает, когда тип правильный, но значение некорректно:

```python
int('abc')  # ValueError: invalid literal
```

**Подкласс:** `UnicodeError` — ошибки кодирования/декодирования Unicode

#### TypeError — неправильный тип

Операция применена к объекту неподходящего типа:

```python
'2' + 2  # TypeError: can only concatenate str
```

#### NameError — имя не определено

- `UnboundLocalError` — локальная переменная используется до присваивания

```python
print(неопределенная_переменная)  # NameError
```

#### AttributeError — атрибут не найден

```python
'строка'.несуществующий_метод()  # AttributeError
```

#### ImportError — ошибка импорта

- `ModuleNotFoundError` — модуль не найден

```python
import несуществующий_модуль  # ModuleNotFoundError
```

#### RuntimeError — общие ошибки выполнения

- `RecursionError` — превышена максимальная глубина рекурсии
- `NotImplementedError` — метод не реализован

#### SyntaxError — синтаксическая ошибка

- `IndentationError` — неправильные отступы
  - `TabError` — смешивание табуляций и пробелов

---

## 🎯 Практические рекомендации

### 1. Перехватывайте конкретные исключения

```python
# ❌ Плохо
try:
    result = 10 / x
except:
    print("Ошибка")

# ✅ Хорошо
try:
    result = 10 / x
except ZeroDivisionError:
    print("Деление на ноль")
except TypeError:
    print("x должен быть числом")
```

### 2. Используйте иерархию для группировки

```python
# Перехватит и IndexError, и KeyError
try:
    value = data[key]
except LookupError as e:
    print(f"Ошибка поиска: {e}")
```

### 3. Создавайте собственные исключения

```python
class ValidationError(ValueError):
    """Ошибка валидации данных"""
    pass

def validate_age(age):
    if age < 0:
        raise ValidationError("Возраст не может быть отрицательным")
```

### 4. Порядок обработчиков важен

```python
# ✅ Правильно: от специфичных к общим
try:
    # код
except IndexError:
    # обработка IndexError
except LookupError:
    # обработка других LookupError
except Exception:
    # обработка всех остальных

# ❌ Неправильно: общий перехватчик первым
try:
    # код
except Exception:  # Перехватит всё!
    pass
except IndexError:  # Никогда не выполнится
    pass
```

---

## 📌 Шпаргалка по частым исключениям

| Исключение | Когда возникает | Пример |
|---|---|---|
| `IndexError` | Индекс вне диапазона | `[1,2][5]` |
| `KeyError` | Ключа нет в словаре | `{}['key']` |
| `ValueError` | Неверное значение | `int('abc')` |
| `TypeError` | Неверный тип | `'2' + 2` |
| `AttributeError` | Атрибут не найден | `x.bad_attr` |
| `ZeroDivisionError` | Деление на ноль | `1 / 0` |
| `FileNotFoundError` | Файл не найден | `open('x.txt')` |
| `ImportError` | Ошибка импорта | `import bad_module` |
| `NameError` | Имя не определено | `print(xyz)` |

---

## 🔗 Связанные темы

- [[01 — 🎓 Основы обработки исключений в Python]]
- [[03 — ✨ Создание собственных исключений]]
- [[04 — ⚖️ Оператор assert]]

## ❓ Вопросы / Непонятное

- ...
