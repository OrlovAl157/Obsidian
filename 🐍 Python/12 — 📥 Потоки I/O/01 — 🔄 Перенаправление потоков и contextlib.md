---
tags:
  - python
  - тема/потоки
  - тема/io
  - статус/завершён
уровень: начинающий
---

# 🔄 Перенаправление потоков и contextlib

## 🎯 ЗАДАЧА: СОХРАНЯТЬ ВЫВОД В ФАЙЛ

Часто нужно сохранять результаты программы в файл, а не выводить на экран.

Например:
- Программа обрабатывает данные → результаты в `results.txt`
- Программа выполняет операции → ошибки в `errors.txt`

Как это сделать?

---

## 📝 СПОСОБ 1: ПАРАМЕТР file В print()

Самый простой способ:

```python
import sys

with open('output.txt', 'w', encoding='utf-8') as file:
    print('Результат 1', file=file)
    print('Результат 2', file=file)

print('Это на экран')
```

Создаёт файл `output.txt`:
```
Результат 1
Результат 2
```

На экран выводит:
```
Это на экран
```

**Но это работает только если ты контролируешь весь код!**

Если ты используешь чужую функцию, которая внутри вызывает `print()` без параметра `file`, ты не сможешь перенаправить её вывод.

---

## 🔄 СПОСОБ 2: МЕНЯТЬ sys.stdout НАПРЯМУЮ

Если нельзя менять код функции, можно **переменить сам sys.stdout**!

```python
import sys

def foo():
    print('Привет!')
    print('Как дела?')
    print('Что делаешь?')

# Открываем файл
file = open('output.txt', 'w', encoding='utf-8')

# Сохраняем оригинальный stdout
orig_stdout = sys.stdout

# МЕНЯЕМ stdout на файл!
sys.stdout = file

foo()  # внутри foo используется print()
       # но теперь он пишет в файл!

# ВОССТАНАВЛИВАЕМ оригинальный stdout
sys.stdout = orig_stdout

file.close()

print('На экран!')
```

Файл `output.txt`:
```
Привет!
Как дела?
Что делаешь?
```

На экран:
```
На экран!
```

### Как это работает?

```python
sys.stdout = file  # ← КЛЮЧЕВОЙ МОМЕНТ!
```

Это просто **переменная**! Когда `print()` пишет что-то, он обращается к текущему `sys.stdout`:

```python
print("текст")
# Под капотом:
sys.stdout.write("текст\n")
```

Если ты сменил `sys.stdout`, то `print()` будет писать туда!

### ⚠️ ВАЖНО: Восстанавливать перед закрытием!

```python
# ❌ НЕПРАВИЛЬНО
sys.stdout = file
print('В файл')
file.close()
print('На экран')  # ❌ ОШИБКА! stdout ещё указывает на файл!
```

Выдаст:
```
ValueError: I/O operation on closed file
```

**Правильно:**

```python
# ✅ ПРАВИЛЬНО
orig_stdout = sys.stdout
sys.stdout = file
print('В файл')
sys.stdout = orig_stdout  # восстановили ПЕРЕД закрытием!
file.close()
print('На экран')  # ✅ работает!
```

### Точно так же для stderr

```python
import sys

def add(a, b):
    if a > 100:
        print('⚠️ Предупреждение!', file=sys.stderr)
    return a + b

file = open('errors.txt', 'w', encoding='utf-8')
orig_stderr = sys.stderr
sys.stderr = file

result = add(150, 20)
print(result)  # stdout, на экран

sys.stderr = orig_stderr
file.close()

print('Завершение')  # на экран
```

---

## 🎯 СПОСОБ 3: CONTEXTLIB (УМНЕЕ!)

Проблема с Способом 2: легко забыть восстановить stdout!

**Решение:** модуль `contextlib` с контекстными менеджерами.

### redirect_stdout

```python
from contextlib import redirect_stdout

def foo():
    print('Привет!')
    print('Как дела?')
    print('Что делаешь?')

with open('output.txt', 'w', encoding='utf-8') as file:
    with redirect_stdout(file):
        foo()  # всё пишется в файл

print('На экран!')  # автоматически восстановилось!
```

**Что происходит:**

1. `with redirect_stdout(file):` — "направь stdout в файл"
2. Весь код внутри блока `with` пишет в файл
3. При выходе из блока `with` — **автоматически восстановится** оригинальный stdout!

**Преимущества:**
- ✅ Не нужно помнить про восстановление
- ✅ Если произойдёт ошибка внутри блока, stdout всё равно восстановится
- ✅ Чище и понятнее код

### redirect_stderr

Работает точно так же, но для stderr:

```python
import sys
from contextlib import redirect_stderr

def add(a, b):
    if a > 100:
        print('⚠️ Предупреждение!', file=sys.stderr)
    return a + b

with open('errors.txt', 'w', encoding='utf-8') as file:
    with redirect_stderr(file):
        result = add(150, 20)
        print(result)  # это stdout, на экран

print('На экран')
```

---

## 🔴 ПРИМЕЧАНИЕ 1: РАЗЛИЧИЕ МЕЖДУ stdout И stderr

**stdout** — это нормальный вывод результатов:
```python
print('Обработано 100 файлов')  # stdout
```

**stderr** — это ошибки и предупреждения:
```python
print('⚠️ Файл corrupted.txt пропущен', file=sys.stderr)  # stderr
```

**Оперативная система позволяет перенаправлять их отдельно!**

Например, в терминале (shell):
```bash
python program.py > results.txt 2> errors.txt
```

- `> results.txt` — перенаправляет stdout в results.txt
- `2> errors.txt` — перенаправляет stderr (номер 2) в errors.txt

---

## 🔴 ПРИМЕЧАНИЕ 2: БУФЕРИЗАЦИЯ И flush()

Когда программа пишет в поток, данные могут скапливаться в **буфере** и не выводиться сразу!

```python
import time
import sys

for i in range(5, 0, -1):
    sys.stdout.write(str(i) + '...')
    time.sleep(1)
```

Ничего не выводится 5 секунд, потом всё сразу: `5...4...3...2...1...`

**Решение 1: flush()**

```python
import time
import sys

for i in range(5, 0, -1):
    sys.stdout.write(str(i) + '...')
    sys.stdout.flush()  # очистить буфер
    time.sleep(1)
```

Теперь выводит каждую секунду: `5...` → `4...` → `3...`

**Решение 2: добавить \n**

```python
import time
import sys

for i in range(5, 0, -1):
    sys.stdout.write(str(i) + '...\n')  # с переносом строки
    time.sleep(1)
```

Перенос строки автоматически очищает буфер!

**Решение 3: параметр flush в print()**

```python
import time

for i in range(5, 0, -1):
    print(str(i) + '...', end='', flush=True)
    time.sleep(1)
```

---

## 🔴 ПРИМЕЧАНИЕ 3: WARNINGS ДЛЯ ПРЕДУПРЕЖДЕНИЙ

Есть специальный модуль для предупреждений:

```python
import warnings

def add(a, b):
    if a > 100:
        warnings.warn('Функция может работать нестабильно!')
    return a + b

print(add(150, 20))
print('Завершение')
```

Выводит:
```
C:\main.py:7: UserWarning: Функция может работать нестабильно!
  warnings.warn('...')
170
Завершение
```

**Преимущества warnings над print():**
- ✅ Показывает номер строки кода
- ✅ Можно отключить все предупреждения сразу
- ✅ Можно отфильтровать определённые предупреждения
- ✅ Стандартный способ в Python

---

## 🔴 ПРИМЕЧАНИЕ 4: СОХРАНЕНИЕ TRACEBACK

Если нужно сохранить информацию об ошибке (traceback) в файл:

```python
import traceback

def foo():
    raise ValueError('Произошла ошибка')

with open('error.txt', 'w', encoding='utf-8') as file:
    try:
        foo()
    except ValueError:
        traceback.print_exc(file=file)  # пишет traceback в файл!
```

Файл `error.txt`:
```
Traceback (most recent call last):
  File "main.py", line 9, in <module>
    foo()
  File "main.py", line 5, in foo
    raise ValueError('Произошла ошибка')
ValueError: Произошла ошибка
```

**Зачем это нужно:**
- ✅ Сохраняешь полную информацию об ошибке
- ✅ Можно позже проанализировать
- ✅ Вся информация в одном файле

---

## ⚠️ ПРИМЕЧАНИЕ 5: БУДЬ ОСТОРОЖЕН С open(0/1/2)

Можно открыть потоки напрямую через дескрипторы:

```python
stdin = open(0, encoding='utf-8')   # поток ввода
stdout = open(1, 'w', encoding='utf-8')  # поток вывода
stderr = open(2, 'w', encoding='utf-8')  # поток ошибок
```

**НО БУДЬ ОСТОРОЖЕН:**

```python
with open(1, 'w'):
    pass

print('Это не выведется!')  # ❌ ОШИБКА!
```

`with` закроет поток при выходе, и `print()` перестанет работать!

**По возможности избегай открывать потоки через open()!**

---

## 📊 СРАВНЕНИЕ ВСЕХ СПОСОБОВ

| Способ | Удобство | Гибкость | Безопасность |
|---|---|---|---|
| `print(..., file=file)` | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐ |
| `sys.stdout = file` | ⭐⭐ | ⭐⭐⭐ | ⭐ (нужна осторожность) |
| `redirect_stdout()` | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ |

**Рекомендация:** используй `redirect_stdout()` и `redirect_stderr()` из contextlib!

---

## ✅ ГЛАВНЫЕ ПРАВИЛА

✅ Восстанавливай stdout/stderr ПЕРЕД закрытием файла  
✅ Используй `contextlib.redirect_stdout()` и `redirect_stderr()` для безопасности  
✅ Используй `warnings.warn()` для предупреждений  
✅ Используй `traceback.print_exc(file=file)` для сохранения ошибок  
✅ Помни про буферизацию — используй `flush()` или `\n`  
✅ stdout и stderr работают независимо  
✅ Избегай открывать потоки через `open(0/1/2)`  

---

## 🔗 Связанные темы

- [[00 — 📥 stdin, stdout, stderr]]
- [[Работа с файлами]]
- [[Модуль sys]]
- [[Обработка исключений]]

---

#python/io #streams #contextlib #redirect #advanced
