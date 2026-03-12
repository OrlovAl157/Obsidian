---
tags:
  - python
  - тема/исключения
  - статус/завершён
уровень: начальный
---

# 🎓 Основы обработки исключений в Python

## 📌 Коротко

> Исключения (exceptions) — это события, которые происходят во время выполнения программы и нарушают её нормальный ход. Python предоставляет механизм `try-except-else-finally` для их перехвата и обработки.

---

## 💡 Полезные советы

- **Всегда используйте конкретные исключения** — не пустой `except:`
- **Минимизируйте код в блоке `try`** — только код, который может вызвать ошибку
- **Порядок `except` блоков важен** — от конкретных к общим
- **Используйте `finally` для очистки ресурсов** — закрытие файлов, соединений
- **Или используйте `with`** — автоматическое управление ресурсами
- **Не игнорируйте ошибки** — `except: pass` это плохая практика
- **Документируйте исключения** — какие может выбросить ваша функция

---

## 🚀 Быстрая справка

|Конструкция|Синтаксис|Когда выполняется|
|---|---|---|
|**try**|`try: код`|Контролируемый код (может вызвать ошибку)|
|**except (один тип)**|`except TypeError: код`|При возникновении TypeError|
|**except (несколько)**|`except (ValueError, TypeError): код`|При любом из указанных исключений|
|**except (все)**|`except: код`|При любом исключении (должен быть последним)|
|**except as**|`except ValueError as e: код`|Доступ к объекту исключения|
|**else**|`else: код`|Если исключений НЕ было|
|**finally**|`finally: код`|Всегда (даже при return, break, continue)|

---

## 📖 Теория

## Пример для быстрой справки

```python
try:
    num1 = int(input("Введите число: "))
    num2 = int(input("Введите делитель: "))
    result = num1 / num2
    print(f"Результат: {result}")
except ValueError:
    print("Ошибка: введите число!")
except ZeroDivisionError:
    print("Ошибка: деление на ноль!")
else:
    print("Операция выполнена успешно")
finally:
    print("Программа завершена")
```

## Быстрая справка

|Конструкция|Синтаксис|Когда выполняется|
|---|---|---|
|**try**|`try: код`|Контролируемый код (может вызвать ошибку)|
|**except (один тип)**|`except TypeError: код`|При возникновении TypeError|
|**except (несколько)**|`except (ValueError, TypeError): код`|При любом из указанных исключений|
|**except (все)**|`except: код`|При любом исключении (должен быть последним)|
|**except as**|`except ValueError as e: код`|Доступ к объекту исключения|
|**else**|`else: код`|Если исключений НЕ было|
|**finally**|`finally: код`|Всегда (даже при return, break, continue)|

## Типы ошибок в Python

### 1. Синтаксические ошибки (SyntaxError)

Возникают из-за нарушения синтаксиса языка Python:

```python
# ❌ Пропущена закрывающая скобка
print('Hello, world!'
# SyntaxError: '(' was never closed

# ❌ Пропущено двоеточие
def square(num)
    return num ** 2
# SyntaxError: expected ':'

# ❌ Неправильный отступ
def func():
print("Hello")
# IndentationError: expected an indented block
```

**Особенности:**

- Отлавливаются интерпретатором **до** выполнения программы
- Программа не запустится, пока ошибка не исправлена
- Легко находятся и исправляются

### 2. Логические ошибки

Программа работает, но дает неверный результат из-за недостатка в логике:

```python
# ❌ Логическая ошибка - забыли скобки
def avg(a, b):
    return a + b / 2  # Делится только b, а не сумма!

print(avg(6, 14))  # 13.0 вместо ожидаемых 10.0

# ✅ Правильно
def avg(a, b):
    return (a + b) / 2

print(avg(6, 14))  # 10.0
```

**Важно:** Логические ошибки могут проявляться не всегда:

```python
def avg(a, b):
    return a + b / 2

print(avg(0, 7))  # 3.5 - ПРАВИЛЬНО (случайно)
print(avg(6, 14)) # 13.0 - НЕПРАВИЛЬНО
```

**Особенности:**

- Интерпретатор их **не обнаруживает**
- Проявляются не всегда (зависят от данных)
- Находятся только через тестирование
- Самые опасные и сложные

**Пример известной логической ошибки** (бинарный поиск в Java):

```python
# ❌ Может привести к переполнению в Java
mid = (low + high) // 2

# ✅ Правильно
mid = low + (high - low) // 2
```

### 3. Исключения (Runtime Errors)

Ошибки времени выполнения, возникающие из-за некорректных данных или условий:

```python
# ZeroDivisionError
num1 = 10
num2 = 0
print(num1 / num2)  # ZeroDivisionError: division by zero

# ValueError
num = int('abc')  # ValueError: invalid literal for int()

# IndexError
nums = [1, 2, 3]
print(nums[10])  # IndexError: list index out of range
```

**Особенности:**

- Возникают **во время выполнения**
- Можно обработать с помощью `try-except`
- Без обработки — аварийное завершение программы

## Работа с кодами возврата (старый подход)

До появления исключений использовались коды возврата:

```python
# Метод find() использует коды возврата
text = 'Hello, world!'
print(text.find('w'))  # 7 (позиция найдена)
print(text.find('a'))  # -1 (не найдено - код возврата)

# Метод index() использует исключения
print(text.index('w'))  # 7
print(text.index('a'))  # ValueError: substring not found
```

**Проблемы с кодами возврата:**

- Много проверок в коде
- Цепочка возвратов через функции
- Менее читаемый код
- Легко забыть проверить код

**Сравнение подходов (C vs Python):**

```c
// C - код возврата (громоздко)
doStuff() {
  if (doFirstThing() == ERROR)
    return ERROR;
  if (doNextThing() == ERROR)
    return ERROR;
  return doLastThing();
}

main() {
  if (doStuff() == ERROR)
    badEnding();
  else
    goodEnding();
}
```

```python
# Python - исключения (лаконично)
def doStuff():
    doFirstThing()
    doNextThing()
    doLastThing()

try:
    doStuff()
except:
    badEnding()
else:
    goodEnding()
```

## Конструкция try-except

### Базовый синтаксис

```python
try:
    # Контролируемый код (может вызвать ошибку)
    risky_code()
except:
    # Код обработки любого исключения
    handle_error()
```

**Как работает:**

1. Выполняется код в блоке `try`
2. Если ошибка — переход в блок `except`
3. Если ошибки нет — блок `except` пропускается
4. Программа продолжает работу после конструкции

### Простой пример

```python
try:
    num1 = int(input())
    num2 = int(input())
    print('Частное чисел равно', num1 / num2)
except:
    print('Вы ввели некорректные данные!')

print('Работа программы завершена!')
```

**Результаты:**

- Ввод: `10` и `2` → `Частное чисел равно 5.0`
- Ввод: `abc` и `qwerty` → `Вы ввели некорректные данные!`
- Ввод: `10` и `0` → `Вы ввели некорректные данные!`

**Недостаток:** Все ошибки обрабатываются одинаково.

## Обработка конкретных исключений

### Несколько блоков except

```python
try:
    num1 = int(input())
    num2 = int(input())
    print('Частное чисел равно', num1 / num2)
except ValueError:
    print('Нужно было ввести числа!')
except ZeroDivisionError:
    print('На ноль делить нельзя!')

print('Работа программы завершена!')
```

**Как работает:**

1. Если возникла ошибка в `try` — начинается проверка блоков `except`
2. Проверяется **по порядку** совпадение типа ошибки
3. Выполняется **первый** подходящий блок `except`
4. Если совпадений нет — программа завершается с ошибкой

### Несколько типов в одном блоке

**Python 3.13 и ранее — нужны скобки:**

```python
try:
    num1 = int(input())
    num2 = int(input())
    print('Частное чисел равно', num1 / num2)
except (ValueError, TypeError):
    print('Ошибка типа или значения!')
except ZeroDivisionError:
    print('На ноль делить нельзя!')
```

**Python 3.14+ — скобки необязательны:**

```python
except ValueError, TypeError:  # Теперь работает без скобок
    print('Ошибка типа или значения!')
```

### Универсальный обработчик

```python
try:
    num1 = int(input())
    num2 = int(input())
    print('Частное чисел равно', num1 / num2)
except (ValueError, IndexError, KeyError):
    print('Тут обрабатываются три типа ошибок!')
except ZeroDivisionError:
    print('На ноль делить нельзя!')
except:
    print('Если не сработал ни один из предыдущих блоков.')

print('Работа программы завершена!')
```

**⚠️ Важно:**

- Универсальный `except:` должен быть **последним**
- Должен быть только **один** такой блок
- Иначе `SyntaxError`

## Основные типы исключений

|Исключение|Когда возникает|Пример|
|---|---|---|
|**ValueError**|Неподходящее значение (правильный тип)|`int('abc')`|
|**TypeError**|Неподходящий тип|`'text' + 5`|
|**ZeroDivisionError**|Деление на ноль|`10 / 0`|
|**IndexError**|Неверный индекс последовательности|`[1, 2, 3][10]`|
|**KeyError**|Неверный ключ словаря|`{'a': 1}['b']`|
|**NameError**|Необъявленная переменная|`print(x)` (x не существует)|
|**FileNotFoundError**|Файл не найден|`open('missing.txt')`|
|**AttributeError**|Несуществующий атрибут|`'text'.missing_method()`|
|**SyntaxError**|Синтаксическая ошибка|`def func(`|

### Разница между TypeError и ValueError

**TypeError** — неподходящий **тип**:

```python
# Нельзя складывать строку и число
print('beegeek' + 2022)  # TypeError
```

**ValueError** — неподходящее **значение** (правильный тип):

```python
# Тип правильный (строка), но значение неподходящее
num = int('beegeek')  # ValueError
```

### Примеры исключений

```python
# ValueError
try:
    age = int('двадцать')
except ValueError:
    print("Введите число цифрами!")

# TypeError
try:
    result = 'hello' + 5
except TypeError:
    print("Нельзя складывать строку и число!")

# IndexError
try:
    nums = [1, 2, 3]
    print(nums[7])
except IndexError:
    print("Индекс выходит за границы!")

# KeyError
try:
    data = {'name': 'John'}
    print(data['age'])
except KeyError:
    print("Ключ не найден!")
```

## Блок else

Выполняется, если в блоке `try` исключений **не было**:

```python
try:
    # Контролируемый код
    code()
except SomeError:
    # Обработка ошибки
    handle()
else:
    # Если ошибки не было
    success()
```

### Пример

```python
try:
    num = int(input())
    print('Квадрат числа равен:', num ** 2)
except ValueError:
    print('Вы ввели некорректные данные!')
else:
    print('Ошибки не произошло!')

print('Работа программы завершена!')
```

**Ввод: `10`**

```
Квадрат числа равен: 100
Ошибки не произошло!
Работа программы завершена!
```

**Ввод: `abc`**

```
Вы ввели некорректные данные!
Работа программы завершена!
```

**Аналогия с циклами:**

- `else` в `try-except` → если не было исключений
- `else` в `for/while` → если не было `break`

## Блок finally

Выполняется **всегда**, независимо от наличия ошибок:

```python
try:
    # Контролируемый код
    code()
except SomeError:
    # Обработка ошибки
    handle()
finally:
    # Выполняется ВСЕГДА
    cleanup()
```

### Пример

```python
try:
    num = int(input())
    print('Квадрат числа равен:', num ** 2)
except ValueError:
    print('Вы ввели некорректные данные!')
finally:
    print('Блок кода выполняется всегда!')

print('Работа программы завершена!')
```

**Ввод: `10`**

```
Квадрат числа равен: 100
Блок кода выполняется всегда!
Работа программы завершена!
```

**Ввод: `abc`**

```
Вы ввели некорректные данные!
Блок кода выполняется всегда!
Работа программы завершена!
```

### Основное применение — закрытие ресурсов

```python
try:
    file = open('data.txt', encoding='utf-8')
    try:
        text = file.read()
    except:
        print('При чтении из файла произошла ошибка!')
    else:
        print('Чтение из файла прошло успешно!')
    finally:
        file.close()  # Закроется в ЛЮБОМ случае!
except FileNotFoundError:
    print('Файл с указанным именем не найден!')
```

### finally без except

`finally` можно использовать без `except`:

```python
try:
    file = open('data.txt', encoding='utf-8')
    try:
        text = file.read()
    finally:
        file.close()  # Закроется всегда
except FileNotFoundError:
    print('Файл не найден!')
except:
    print('Произошла ошибка!')
```

Если в внутреннем `try` возникнет ошибка:

1. Сначала выполнится `finally` (файл закроется)
2. Затем ошибка "всплывет" к внешнему обработчику

### Когда выполняется finally

`finally` выполняется **даже если:**

- В `try` произошла ошибка
- В `try` есть `return`
- В `try` есть `break` или `continue`
- Ошибка не была обработана

```python
# finally выполнится даже при необработанной ошибке
try:
    x = 10 / 0
finally:
    print('Блок finally')
# Вывод:
# Блок finally
# ZeroDivisionError: division by zero
```

### finally и return

`finally` выполняется **до** `return`:

```python
def func():
    try:
        return 10
    finally:
        print('Выполняется блок finally!')

print(func())
# Выполняется блок finally!
# 10
```

**Если `return` в обоих блоках:**

```python
def func():
    try:
        return 10
    finally:
        return 20  # Этот return перезапишет!

print(func())  # 20
```

### finally vs else

```python
try:
    code()
except Error:
    handle()
else:
    # Только если НЕ было ошибок
    success()
finally:
    # ВСЕГДА
    cleanup()
```

## Полная структура try-except

```python
try:
    # Контролируемый код
    num = int(input())
    result = 100 / num
    
except ValueError:
    # Обработка ValueError
    print("Введите число!")
    
except ZeroDivisionError:
    # Обработка ZeroDivisionError
    print("На ноль делить нельзя!")
    
except (TypeError, KeyError):
    # Обработка нескольких типов
    print("Ошибка типа или ключа!")
    
except:
    # Обработка всех остальных
    print("Неизвестная ошибка!")
    
else:
    # Если исключений не было
    print(f"Результат: {result}")
    
finally:
    # Выполняется всегда
    print("Операция завершена")
```

## Вложенные try-except

Python позволяет вкладывать конструкции друг в друга:

```python
try:
    file = open('data.txt', encoding='utf-8')
    try:
        text = file.read()
    except:
        print('Ошибка чтения!')
    else:
        print('Чтение успешно!')
    finally:
        file.close()
except FileNotFoundError:
    print('Файл не найден!')
```

**Ограничений на вложенность нет.**

## Обработка исключений в функциях

Обработчики ловят исключения не только в блоке `try`, но и в вызываемых функциях:

```python
def this_fails():
    num = 1 / 0  # Ошибка внутри функции

try:
    this_fails()  # Вызываем функцию
except ZeroDivisionError:
    print('Деление на ноль')  # Ошибка поймана!
```

## Доступ к объекту исключения (as)

Для получения доступа к объекту исключения используется синтаксис `as`:

```python
try:
    nums = [10, 5, 20, 25]
    print(nums[100])
except IndexError as err:  # Записываем исключение в переменную err
    print(err)             # list index out of range
    print(type(err))       # <class 'IndexError'>
```

### Что содержит объект исключения

```python
try:
    print(1 / 0)
except ZeroDivisionError as err:
    print(dir(err))  # Все атрибуты объекта
```

**Основные атрибуты:**

- `args` — кортеж с аргументами исключения
- `__str__()` — строковое представление
- `__traceback__` — информация о трассировке

### Атрибут args

```python
try:
    x, y = 10, 0
    if y == 0:
        raise ZeroDivisionError('Произошло деление на ноль.')
except ZeroDivisionError as err:
    print(err)           # Произошло деление на ноль.
    print(err.args)      # ('Произошло деление на ноль.',)
    print(type(err.args)) # <class 'tuple'>
```

**При печати объекта исключения автоматически вызывается `str()`:**

```python
try:
    raise ValueError('Ой', 'Произошла ошибка')
except ValueError as e:
    print(e)  # ('Ой', 'Произошла ошибка')
```

### Примеры с разными исключениями

```python
# ValueError
try:
    num = int('abc')
except ValueError as e:
    print(f"Ошибка: {e}")
    print(f"Тип: {type(e).__name__}")
# Ошибка: invalid literal for int() with base 10: 'abc'
# Тип: ValueError

# ZeroDivisionError
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Ошибка: {e}")
    print(f"Аргументы: {e.args}")
# Ошибка: division by zero
# Аргументы: ('division by zero',)

# FileNotFoundError
try:
    file = open('missing.txt')
except FileNotFoundError as e:
    print(f"Файл не найден: {e}")
```

### Обработка нескольких типов с доступом к объекту

**Python 3.13 и ранее — скобки обязательны:**

```python
try:
    nums = [10, 5, 20, 25]
    print(nums[100])
except (KeyError, IndexError) as err:
    print(f"Ошибка: {err}")
    print(f"Тип: {type(err).__name__}")
# Ошибка: list index out of range
# Тип: IndexError
```

**Python 3.14+ — скобки нужны при использовании `as`:**

```python
# ❌ Без скобок — ошибка
try:
    nums = [10, 5, 20, 25]
    print(nums[100])
except KeyError, IndexError as err:  # SyntaxError!
    print(err)

# ✅ Со скобками — работает
except (KeyError, IndexError) as err:
    print(err)
```

### Обработка всех исключений с доступом к объекту

Для обработки любого исключения нужно указать `Exception`:

```python
# ❌ Синтаксически неверно
try:
    x = 1 / 0
except as err:  # SyntaxError!
    print(err)

# ✅ Правильно
try:
    x = 1 / 0
except Exception as err:
    print(f"Произошла ошибка: {err}")
    print(f"Тип ошибки: {type(err).__name__}")
```

### Использование информации об ошибке

```python
def safe_divide(a, b):
    """Деление с подробным логированием ошибок"""
    try:
        return a / b
    except ZeroDivisionError as e:
        print(f"[ERROR] {type(e).__name__}: {e}")
        print(f"[INFO] Попытка деления {a} на {b}")
        return None
    except TypeError as e:
        print(f"[ERROR] {type(e).__name__}: {e}")
        print(f"[INFO] Аргументы должны быть числами")
        print(f"[DEBUG] a={a} (type: {type(a).__name__})")
        print(f"[DEBUG] b={b} (type: {type(b).__name__})")
        return None

# Тестирование
safe_divide(10, 0)
# [ERROR] ZeroDivisionError: division by zero
# [INFO] Попытка деления 10 на 0

safe_divide(10, '5')
# [ERROR] TypeError: unsupported operand type(s) for /: 'int' and 'str'
# [INFO] Аргументы должны быть числами
# [DEBUG] a=10 (type: int)
# [DEBUG] b=5 (type: str)
```

### Модуль sys.exc_info()

Для получения полной информации об исключении:

```python
from sys import exc_info

try:
    x = 1 / 0
except Exception as err:
    exc_type, exc_value, exc_traceback = exc_info()
    print(f"Тип: {exc_type}")
    print(f"Значение: {exc_value}")
    print(f"Трассировка: {exc_traceback}")
# Тип: <class 'ZeroDivisionError'>
# Значение: division by zero
# Трассировка: <traceback object at 0x...>
```

### Принятые имена переменных

Обычно используют короткие имена:

```python
# Популярные варианты
except ValueError as e:      # Самый распространенный
except ValueError as err:    # Тоже часто
except ValueError as ex:     # Реже
except ValueError as error:  # Длинный вариант
```

**Рекомендация:** Используйте `e` или `err` для краткости.

## Порядок обработки исключений

**⚠️ Критически важно:** Обработчики проверяются **сверху вниз**!

```python
# ❌ НЕПРАВИЛЬНО - универсальный except первым
try:
    nums = [1, 2, 3]
    print(nums[100])
except:
    print("Любая ошибка")  # Сработает первым!
except IndexError:
    print("Эта строка НИКОГДА не выполнится!")

# ✅ ПРАВИЛЬНО - от конкретного к общему
try:
    nums = [1, 2, 3]
    print(nums[100])
except IndexError:
    print("Ошибка индекса")
except KeyError:
    print("Ошибка ключа")
except:
    print("Любая другая ошибка")
```

## Оператор pass

Используется как заглушка в обязательных блоках:

```python
# В функциях
def do_something():
    pass  # Пока не реализовано

# В except
try:
    print('beegeek')
except:
    pass  # Игнорируем все ошибки (не рекомендуется!)

# С with
with open('beegeek.txt') as file:
    pass  # Просто открываем и закрываем
```

**⚠️ Осторожно:** `except: pass` скрывает все ошибки — используйте только если действительно нужно!

## Когда использовать try-except?

### ✅ Используйте:

1. **Ввод пользователя**

```python
num = int(input())
```

2. **Работа с файлами**

```python
with open('file.txt') as f:
    content = f.read()
```

3. **Сетевые операции**

```python
response = requests.get(url)
```

4. **Преобразования типов**

```python
num = int(user_input)
```

5. **Обращение к элементам**

```python
value = dictionary[key]
item = list[index]
```

### ❌ Не используйте:

1. **Для логических ошибок** — исправляйте код!
    
2. **Когда можно проверить заранее**
    

```python
# ❌ Плохо
try:
    result = 10 / x
except ZeroDivisionError:
    pass

# ✅ Хорошо
if x != 0:
    result = 10 / x
```

3. **Слишком широкий охват**

```python
# ❌ Плохо - что именно вызвало ошибку?
try:
    # 100 строк кода
except:
    pass
```

## Распространенные ошибки

### ❌ Ошибка 1: Пустой except

```python
# Плохо - скрывает проблемы
try:
    do_something()
except:
    pass  # Что пошло не так? Неизвестно!

# Хорошо
try:
    do_something()
except ValueError as e:
    print(f"Ошибка значения: {e}")
```

### ❌ Ошибка 2: Слишком много кода в try

```python
# Плохо - непонятно, что вызвало ошибку
try:
    data = read_file()
    processed = process_data(data)
    result = analyze(processed)
    save_to_db(result)
except:
    print("Что-то пошло не так")

# Хорошо - разделяем
try:
    data = read_file()
except FileNotFoundError:
    print("Файл не найден")
    return

try:
    processed = process_data(data)
except ValueError:
    print("Ошибка обработки")
```

### ❌ Ошибка 3: Неправильный порядок

```python
# Плохо
try:
    num = int(input())
except:
    print("Любая ошибка")
except ValueError:  # НИКОГДА не выполнится!
    print("Не число")

# Хорошо
try:
    num = int(input())
except ValueError:
    print("Не число")
except:
    print("Другая ошибка")
```

## Полезные советы

1. ✅ **Обрабатывайте конкретные исключения** — не `except:`
2. ✅ **Минимизируйте код в try** — только проблемный код
3. ✅ **Не игнорируйте ошибки** — `except: pass` опасен
4. ✅ **Используйте finally для очистки** — закрытие ресурсов
5. ✅ **Или используйте `with`** — он автоматичен и безопаснее
6. ✅ **Порядок важен** — от конкретных к общим
7. ✅ **Документируйте** — какие исключения может выбросить функция

## HTTP коды ошибок (аналогия)

В веб-разработке используются похожие концепции:

- **200** — OK (успех)
- **404** — Not Found (не найдено)
- **503** — Service Unavailable (сервис недоступен)

Это тоже коды возврата, но в контексте HTTP.

## Следующие темы

📚 **Продвинутая работа с исключениями:**

- Иерархия исключений
- Работа с объектом исключения (`as err`)
- Возбуждение исключений (`raise`)
- Цепочки исключений

📚 **Пользовательские исключения и best practices:**

- Создание своих исключений
- LBYL vs EAFP подходы
- Оператор `assert`
- Лучшие практики

## Полезные ссылки

- [Документация: Errors and Exceptions](https://docs.python.org/3/tutorial/errors.html)
- [Built-in Exceptions](https://docs.python.org/3/library/exceptions.html)
- [PEP 3134 — Exception Chaining](https://www.python.org/dev/peps/pep-3134/)

---

## 🔗 Связанные темы

- [[01 — ❓ Что такое исключение]]
- [[03 — 🌳 Иерархия встроенных исключений Python]]
- [[04 — ✨ Создание собственных исключений]]
- [[05 — 📋 Таблица основных типов исключений]]
- [[01 — ⚖️ Оператор assert]]

## ❓ Вопросы / Непонятное

- ...