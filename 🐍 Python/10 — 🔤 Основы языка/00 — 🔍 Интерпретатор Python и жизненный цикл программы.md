---
tags:
  - python
  - тема/основы
  - статус/завершён
---

# 🔍 Интерпретатор Python и жизненный цикл программы

## 📌 Коротко

> Python — интерпретируемый язык, что означает что код выполняется построчно интерпретатором, а не компилируется в машинный код заранее. Но на самом деле там есть промежуточная компиляция в bytecode.

---

## 💡 Ключевые понятия

- **Интерпретатор** — программа которая читает и выполняет Python код
- **Компиляция в bytecode** — Python компилирует в промежуточный код
- **CPython** — основная реализация Python на языке C
- **Python Virtual Machine (PVM)** — машина которая выполняет bytecode
- **REPL** — интерактивная оболочка для ввода команд

---

## 🚀 Быстрая справка

| Этап | Что происходит | Результат |
|---|---|---|
| **1. Токенизация** | Код разбивается на токены | Поток токенов |
| **2. Парсинг** | Создаётся AST (синтаксическое дерево) | AST |
| **3. Компиляция** | AST компилируется в bytecode | .pyc файл |
| **4. Оптимизация** | peephole optimizer оптимизирует | Оптимизированный bytecode |
| **5. Выполнение** | PVM выполняет bytecode | Результат программы |

---

## 📖 Полное объяснение

### Что такое интерпретатор?

Интерпретатор — это программа которая:
1. Читает исходный код Python
2. Разбивает его на части
3. Проверяет синтаксис
4. Преобразует в промежуточный код
5. Выполняет этот код пошагово

```
Исходный код (.py) → Интерпретатор → Выполнение → Результат
```

### CPython vs Другие реализации

**CPython** (основной, 99% людей)
```python
# Установка по умолчанию с python.org
python3 hello.py
```

**PyPy** (быстрее благодаря JIT компиляции)
```bash
pypy3 hello.py
```

**Jython** (работает на Java Virtual Machine)
```bash
jython hello.py
```

**IronPython** (работает на .NET)
```bash
ipy hello.py
```

---

## 🔄 Жизненный цикл программы (5 этапов)

### Этап 1️⃣: ТОКЕНИЗАЦИЯ (Tokenization)

**Что это?** Исходный код разбивается на мелкие куски — токены.

```python
# Исходный код
x = 5 + 3
print(x)

# Токены:
# NAME 'x'
# EQUAL '='
# NUMBER '5'
# PLUS '+'
# NUMBER '3'
# NEWLINE '\n'
# NAME 'print'
# LPAR '('
# NAME 'x'
# RPAR ')'
# NEWLINE '\n'
```

**Модуль:** `tokenize`

```python
import tokenize
import io

code = "x = 5"
tokens = tokenize.generate_tokens(io.StringIO(code).readline)
for tok in tokens:
    print(tok)
```

---

### Этап 2️⃣: ПАРСИНГ (Parsing)

**Что это?** Токены преобразуются в AST (Abstract Syntax Tree) — синтаксическое дерево.

```python
# Код
x = 5 + 3

# AST:
# Module(
#   body=[
#     Assign(
#       targets=[Name(id='x')],
#       value=BinOp(
#         left=Constant(value=5),
#         op=Add(),
#         right=Constant(value=3)
#       )
#     )
#   ]
# )
```

**Модуль:** `ast`

```python
import ast

code = "x = 5 + 3"
tree = ast.parse(code)
print(ast.dump(tree, indent=2))
```

**Визуализация AST:**

```
        Assign
       /      \
     Name      BinOp
    (x=x)     /  |  \
           Const Add Const
           (5)      (3)
```

---

### Этап 3️⃣: КОМПИЛЯЦИЯ в BYTECODE (Compilation)

**Что это?** AST компилируется в bytecode — промежуточный код для Python Virtual Machine.

```python
# Код
x = 5 + 3

# Bytecode (примерно):
# 0 LOAD_CONST               1 (5)
# 2 LOAD_CONST               2 (3)
# 4 BINARY_ADD
# 6 STORE_NAME               0 (x)
# 8 LOAD_CONST               0 (None)
# 10 RETURN_VALUE
```

**Модуль:** `dis` (disassembler)

```python
import dis

def example():
    x = 5 + 3
    return x

# Показать bytecode
dis.dis(example)

# Вывод:
#   2           0 LOAD_CONST               1 (5)
#               2 LOAD_CONST               2 (3)
#               4 BINARY_ADD
#               6 STORE_FAST               0 (x)
#
#   3           8 LOAD_FAST                0 (x)
#              10 RETURN_VALUE
```

**Где хранится bytecode?**

```
project/
├── main.py
└── __pycache__/
    ├── main.cpython-39.pyc    # Bytecode для Python 3.9
    ├── main.cpython-310.pyc   # Bytecode для Python 3.10
    └── main.cpython-311.pyc   # Bytecode для Python 3.11
```

```python
# Пример .pyc файла
import marshal
import importlib.util

# Загрузить .pyc
spec = importlib.util.spec_from_file_location("module", "module.py")
code = spec.loader.get_code("module")
print(code)  # Объект Code
```

---

### Этап 4️⃣: ОПТИМИЗАЦИЯ (Peephole Optimization)

**Что это?** Bytecode оптимизируется для лучшей производительности.

```python
# Оптимизация 1: Константы складываются заранее
x = 5 + 3      # → x = 8 (вычислено во время компиляции!)

# Оптимизация 2: Мёртвый код удаляется
if False:
    expensive_function()  # → Полностью удалится

# Оптимизация 3: Вложенные функции инлайнируются
def f():
    return x

# Оптимизация 4: Условные переходы оптимизируются
if True:
    x = 5
# → x = 5 (if удалится)
```

**Просмотр оптимизации:**

```python
import dis

# Без оптимизации
code1 = compile("x = 5 + 3", "<string>", "exec")
dis.dis(code1)

# C оптимизацией (автоматически)
# LOAD_CONST 1 (8)  ← 5+3 уже вычислено!
# STORE_NAME 0 (x)
```

---

### Этап 5️⃣: ВЫПОЛНЕНИЕ (Execution by PVM)

**Что это?** Python Virtual Machine читает bytecode и выполняет инструкции.

```
Bytecode → PVM → Операции с памятью → Результат
```

**Как работает PVM:**

```python
# Bytecode
# 0 LOAD_CONST 1 (5)        ← Загрузить 5 в стек
# 2 LOAD_CONST 2 (3)        ← Загрузить 3 в стек
# 4 BINARY_ADD              ← Добавить верхние два элемента стека
# 6 STORE_NAME 0 (x)        ← Сохранить результат в переменную x

# Стек выполнения:
# Шаг 1: [5]
# Шаг 2: [5, 3]
# Шаг 3: [8]  ← результат сложения
# Шаг 4: [] ← результат сохранён в x
```

**Счётчик инструкций (Instruction Pointer):**

```python
import sys

def trace_calls(frame, event, arg):
    if event == 'line':
        filename = frame.f_code.co_filename
        lineno = frame.f_lineno
        print(f"Executing: {filename}:{lineno}")
    return trace_calls

sys.settrace(trace_calls)

# Ваш код здесь

sys.settrace(None)
```

---

## 🎯 Полный цикл на примере

```python
# Файл: hello.py
x = 5
y = 3
print(x + y)
```

**1️⃣ Пользователь запускает:**
```bash
python3 hello.py
```

**2️⃣ Интерпретатор запускается:**
- Читает файл `hello.py`
- Инициализирует виртуальную машину

**3️⃣ Токенизация:**
```
NAME(x), EQUAL(=), NUMBER(5), NEWLINE
NAME(y), EQUAL(=), NUMBER(3), NEWLINE
NAME(print), LPAR, NAME(x), PLUS, NAME(y), RPAR
```

**4️⃣ Парсинг:**
```
Module(
  body=[
    Assign(x = 5),
    Assign(y = 3),
    Expr(print(x + y))
  ]
)
```

**5️⃣ Компиляция:**
```
LOAD_CONST 1 (5)
STORE_NAME 0 (x)
LOAD_CONST 2 (3)
STORE_NAME 1 (y)
LOAD_NAME 1 (print)
LOAD_NAME 0 (x)
LOAD_NAME 2 (y)
BINARY_ADD
CALL_FUNCTION 1
POP_TOP
LOAD_CONST 0 (None)
RETURN_VALUE
```

**6️⃣ Сохранение в .pyc:**
```
__pycache__/hello.cpython-39.pyc
(если файл импортируется или режим оптимизации)
```

**7️⃣ Выполнение:**
```
→ x = 5 (в памяти)
→ y = 3 (в памяти)
→ print(5 + 3)
→ print(8)
Вывод: 8
```

---

## 🔍 Как смотреть что происходит

### 1. Посмотреть bytecode

```python
import dis

def calc(x, y):
    return x + y

# Показать bytecode функции
dis.dis(calc)

# Вывод:
#   3           0 LOAD_FAST                0 (x)
#               2 LOAD_FAST                1 (y)
#               4 BINARY_ADD
#               6 RETURN_VALUE
```

### 2. Посмотреть AST

```python
import ast

code = """
x = 5
y = 3
print(x + y)
"""

tree = ast.parse(code)
print(ast.dump(tree, indent=2))
```

### 3. Посмотреть токены

```python
import tokenize
import io

code = "x = 5 + 3"
readline = io.StringIO(code).readline
tokens = tokenize.generate_tokens(readline)

for tok in tokens:
    print(tok)
```

### 4. Посмотреть время выполнения

```python
import timeit

# Сколько времени на выполнение?
time = timeit.timeit("x = 5 + 3", number=1000000)
print(f"Time: {time:.6f} seconds")
```

### 5. Профилирование

```python
import cProfile
import pstats

def slow_function():
    total = 0
    for i in range(100000):
        total += i
    return total

# Профилировать
cProfile.run('slow_function()')
```

---

## ⚙️ Интерпретатор vs Компилятор

| Параметр | Интерпретатор (Python) | Компилятор (C, Java) |
|---|---|---|
| **Когда работает** | При запуске программы | Перед запуском (заранее) |
| **Скорость разработки** | ✅ Быстро (интерактивно) | ⚠️ Медленнее |
| **Скорость выполнения** | ⚠️ Медленнее | ✅ Быстрее |
| **Ошибки** | При выполнении | При компиляции |
| **Портативность** | ✅ На любой ОС с Python | ❌ Нужна компиляция для каждой ОС |
| **Примеры** | Python, JavaScript, Ruby | C, C++, Java |

```python
# Python (интерпретируемый)
print("Hello")
x = undefined_variable  # Ошибка при выполнении этой строки

# C (компилируемый)
# #include <stdio.h>
// int main() {
//     printf("Hello\n");
//     int x = undefined_variable;  // Ошибка при компиляции!
// }
```

---

## 🎨 Визуализация полного процесса

```
┌─────────────────────────────────────────────────────────────┐
│                  hello.py (исходный код)                    │
│              x = 5 + 3                                      │
│              print(x)                                        │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
        ┌────────────────┐
        │  ТОКЕНИЗАЦИЯ   │  (tokenize модуль)
        │ Разбор на      │
        │ токены         │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │   ПАРСИНГ      │  (ast модуль)
        │ Построение AST │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │  КОМПИЛЯЦИЯ    │  (compiler)
        │ В bytecode     │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │ ОПТИМИЗАЦИЯ    │  (peephole optimizer)
        │ Улучшение      │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │  __pycache__   │
        │ Сохранение     │
        │ .pyc файла     │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │      PVM       │  (Python Virtual Machine)
        │ Выполнение     │
        │ bytecode       │
        └────────┬───────┘
                 │
                 ▼
        ┌────────────────┐
        │    Результат   │
        │      8         │
        └────────────────┘
```

---

## 📊 Разница в исполнении

### Запуск нового скрипта

```bash
$ python3 script.py
1. Загрузить интерпретатор
2. Токенизация
3. Парсинг
4. Компиляция
5. Оптимизация
6. Выполнение
Время: ~100ms
```

### Импорт уже скомпилированного модуля

```python
import numpy  # Если есть numpy.cpython-39.pyc
# Пропускаются этапы 1-5
# Сразу выполнение
Время: ~10ms
```

---

## ⚠️ Что важно знать

```python
# ❌ Синтаксическая ошибка - поймёется при парсинге
if x = 5:     # SyntaxError (не выполнится вообще)
    pass

# ❌ Ошибка типа - поймётся при выполнении
x = "5"
print(x + 10)  # TypeError (выполнится до этой строки)

# ❌ Ошибка имени - поймётся при выполнении
print(undefined_variable)  # NameError

# ✅ Правильно
x = 5
print(x + 10)  # 15
```

### Проверка синтаксиса БЕЗ выполнения

```python
import py_compile

try:
    py_compile.compile('script.py', doraise=True)
    print("Syntax OK!")
except py_compile.PyCompileError as e:
    print(f"Syntax error: {e}")
```

---

## 🔗 Связанные темы

- [[00 — 📖 Введение в Python]]
- [[05 — 📝 Ввод и вывод]]

## ❓ Вопросы / Непонятное

- ...
