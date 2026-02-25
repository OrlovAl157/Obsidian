---
tags:
  - python
  - тема/файлы_и_потоки
  - тема/ввод_вывод
  - статус/завершён
---

# 🌊 Потоковый ввод и вывод (stdin, stdout)

## 📌 Коротко

> Работа с потоками ввода-вывода через `sys.stdin` и `sys.stdout` для обработки больших объёмов данных, конкурсов программирования и перенаправления потоков.

---

## 💡 Полезные советы

- **`sys.stdin` — итератор** — движется только вперёд, не может перемотаться назад
- **Прочитанные данные удаляются** — нельзя прочитать одну строку дважды
- **EOF (End of File)** — Ctrl+D (Unix/Mac) или Ctrl+Z→Enter (Windows)
- **`sys.stdout.flush()`** — принудительный вывод при буферизации
- **`sys.stdout.write()` требует `str()`** — для других типов используйте `str()` явно
- **Производительность** — `sys.stdin` в 2-3 раза быстрее `input()` для больших данных
- **ВСЕГДА `.strip()`** — удаляйте `\n` при чтении из stdin

---

## 🚀 Быстрая справка

| Операция | Синтаксис | Описание | Пример |
|---|---|---|---|
| Построчное чтение | `for line in sys.stdin:` | Итерация по строкам | `for line in sys.stdin: print(line)` |
| Все строки в список | `sys.stdin.readlines()` | С `\n` в конце | `lines = sys.stdin.readlines()` |
| Весь текст целиком | `sys.stdin.read()` | Одна большая строка | `text = sys.stdin.read()` |
| Одна строка | `sys.stdin.readline()` | С `\n` в конце | `line = sys.stdin.readline()` |
| **Через дескриптор** | **`open(0)` или `for line in open(0):`** | **Без импорта sys** | **`[x.strip() for x in open(0)]`** |
| Вывод текста | `sys.stdout.write()` | Без автоматического `\n` | `sys.stdout.write('Hi\n')` |
| Сброс буфера | `sys.stdout.flush()` | Принудительный вывод | `sys.stdout.flush()` |

---

## 📖 Теория

### Что такое потоки ввода-вывода?

**Поток (stream)** — абстракция для последовательной работы с данными. Представьте поток воды — она течёт в одном направлении.

В Python три стандартных потока:
- **stdin** (standard input) — ввод пользователя с клавиатуры
- **stdout** (standard output) — основной вывод на экран
- **stderr** (standard error) — вывод ошибок (отдельный от stdout)

### Особенности sys.stdin

```
📖 Однонаправленный — движется только вперёд
🗑️ Деструктивный — прочитанные данные исчезают
📝 С переносами — каждая строка заканчивается \n
⏹️ EOF сигнал — конец ввода
🔄 Ленивая обработка — читает по мере необходимости
```

### EOF — сигнал конца ввода

| Платформа | Комбинация |
|---|---|
| Linux / macOS | `Ctrl + D` |
| Windows | `Ctrl + Z` → `Enter` |
| PyCharm, VS Code | `Ctrl + D` |

### Связь с input() и print()

```python
# input() примерно равно:
# line = sys.stdin.readline()
# return line.rstrip('\n')

# print(x) примерно равно:
# sys.stdout.write(str(x) + '\n')

# Поэтому:
# - input() медленнее, но удобнее для интерактивного ввода
# - sys.stdin быстрее для больших данных
# - print() проще, чем sys.stdout.write()
```

---

## 💻 Чтение из stdin

### Способ 1: Построчное чтение (рекомендуется!)

```python
import sys

# Экономит память, обрабатывает по одной строке
for line in sys.stdin:
    line = line.strip()  # Убираем \n
    print(f"Прочитано: {line}")

# Пример работы:
# Ввод: Alice
# Ввод: Bob
# Ввод: (Ctrl+D или Ctrl+Z→Enter)
# Результат: Прочитано: Alice
#            Прочитано: Bob
```

### Способ 2: Все строки в список (без \n)

```python
import sys

# Списковое выражение
data = [line.strip() for line in sys.stdin]
print(data)  # ['строка1', 'строка2', ...]

# Или через map()
data = list(map(str.strip, sys.stdin))
print(data)  # То же самое
```

### Способ 3: readlines() — список с \n

```python
import sys

data = sys.stdin.readlines()
# ['строка1\n', 'строка2\n', ...]

# Если нужны без \n:
lines = [line.rstrip('\n') for line in data]
```

### Способ 4: read() — весь текст одной строкой

```python
import sys

text = sys.stdin.read()
# 'строка1\nстрока2\n...'

# Разбить по строкам:
lines = text.splitlines()  # ['строка1', 'строка2', ...]
```

### Способ 5: open(0) — прямой доступ через дескриптор файла (элегантно!)

```python
# open(0) читает из stdin напрямую через файловый дескриптор
# 0 — это зарезервированный дескриптор для stdin
# Не требует импорта sys!

# Читать все строки
for line in open(0):
    print(line.strip())

# Или преобразовать в список
lines = [line.strip() for line in open(0)]

# Практический пример: найти разницу между датами
from datetime import date

dates = [date(*map(int, d.split('-'))) for d in open(0)]
print((max(dates) - min(dates)).days)

# Ввод:
# 2023-01-01
# 2023-12-31
# 2023-06-15
# (Ctrl+D)
# Результат: 364
```

**Преимущества `open(0)`:**
- ✅ Не требует импорта `sys`
- ✅ Чистый и лаконичный синтаксис
- ✅ Прямой доступ к файловому дескриптору
- ✅ Работает с перенаправлением: `python script.py < input.txt`

**Когда использовать:**
- Краткие скрипты и однострочники
- Когда важна минимальность кода
-競賽программирование (Codeforces и т.д.)

**Файловые дескрипторы в Unix/Linux:**
- `0` — stdin (стандартный вход)
- `1` — stdout (стандартный выход)
- `2` — stderr (стандартная ошибка)

---

## 📤 Запись в stdout

### Основное отличие: print() vs sys.stdout.write()

```python
import sys

# print() — автоматически добавляет \n
print('Hello')              # → "Hello\n"

# sys.stdout.write() — НЕ добавляет \n
sys.stdout.write('World')   # → "World"
sys.stdout.write('\n')      # Вручную добавляем
```

### Сравнение методов

| Характеристика | `print()` | `sys.stdout.write()` |
|---|---|---|
| Добавляет `\n` | ✅ Автоматически | ❌ Вручную |
| Преобразует типы | ✅ Автоматически | ❌ Нужен `str()` |
| Несколько аргументов | ✅ Да | ❌ Нет |
| Простота | ✅ Просто | ⚠️ Сложнее |

### Примеры с типами данных

```python
import sys

# ❌ Ошибка — write() принимает только строки
try:
    sys.stdout.write(42)  # TypeError!
except TypeError:
    print("write() требует строку")

# ✅ Правильно — преобразуем
sys.stdout.write(str(42))
sys.stdout.write('\n')

# print() делает это автоматически
print(42)
```

### Сброс буфера (flush)

```python
import sys
import time

# С flush — выводит сразу
for i in range(5):
    sys.stdout.write(f'{i}...')
    sys.stdout.flush()  # Принудительный вывод
    time.sleep(1)

# Результат: 0...1...2...3...4... (с задержками)
```

---

## 💻 Практические примеры

### Суммирование чисел

```python
import sys

# Вариант 1: Построчное чтение
total = 0
for line in sys.stdin:
    total += int(line.strip())
print(total)

# Вариант 2: Список
numbers = [int(line.strip()) for line in sys.stdin]
print(sum(numbers))

# Вариант 3: map()
numbers = list(map(int, sys.stdin))
print(sum(numbers))

# Ввод:
# 10
# 20
# 30
# (Ctrl+D)
# Результат: 60
```

### Фильтрация строк

```python
import sys

# Оставить только строки длиннее 5 символов
text = sys.stdin.read()
lines = [line for line in text.splitlines() if len(line) > 5]

for line in lines:
    print(line)

# Ввод: hi
#       hello
#       world
#       a
#       python
# Результат: hello
#            python
```

### Обработка CSV из stdin

```python
import sys

for line in sys.stdin:
    values = line.strip().split(',')
    if len(values) >= 2:
        name, age = values[0], values[1]
        print(f'Имя: {name}, Возраст: {age}')

# Ввод: Alice,25
#       Bob,30
#       Charlie,35
```

### Подсчёт слов и строк

```python
import sys

text = sys.stdin.read()
lines = text.splitlines()
words = text.split()

print(f'Строк: {len(lines)}')
print(f'Слов: {len(words)}')
print(f'Символов: {len(text)}')
```

### Использование open(0) для краткого кода

```python
# Суммирование чисел (без импорта sys)
total = sum(int(line) for line in open(0))
print(total)

# Фильтрация строк (без импорта sys)
print('\n'.join(line.strip() for line in open(0) if len(line.strip()) > 5))

# Работа с датами (без импорта sys)
from datetime import date
dates = [date(*map(int, d.split('-'))) for d in open(0)]
print((max(dates) - min(dates)).days)  # Разница в днях
```

**Когда использовать `open(0)`:**
- ✅ Краткие скрипты и однострочники
- ✅ Конкурсы программирования
- ✅ Когда нужен минимальный код
- ❌ НЕ использовать в production коде (менее явный, чем `sys.stdin`)

### Чтение до ключевого слова

```python
import sys

lines = []
for line in sys.stdin:
    line = line.strip()
    
    if line == 'STOP':  # Выход по ключевому слову
        break
    
    lines.append(line)

print(f'Прочитано строк: {len(lines)}')
for line in lines:
    print(f"→ {line}")
```

---

## 🔄 Перенаправление потоков

### Перенаправление stdout в файл (в коде)

```python
import sys

# Сохраняем исходный поток
original_stdout = sys.stdout

# Перенаправляем в файл
with open('output.txt', 'w') as f:
    sys.stdout = f
    print('Эта строка запишется в файл')
    print('И эта тоже')

# Восстанавливаем вывод
sys.stdout = original_stdout
print('Теперь снова на экран')
```

### Перенаправление stdin из файла (в коде)

```python
import sys

original_stdin = sys.stdin

with open('input.txt', 'r') as f:
    sys.stdin = f
    data = [line.strip() for line in sys.stdin]

sys.stdin = original_stdin
print(data)
```

### Безопасный способ (контекстный менеджер)

```python
import sys
from contextlib import redirect_stdout, redirect_stdin

# Перенаправление stdout
with open('output.txt', 'w') as f:
    with redirect_stdout(f):
        print('В файл')
        print('Ещё в файл')

# Перенаправление stdin
with open('input.txt', 'r') as f:
    with redirect_stdin(f):
        data = input()
        print(data)
```

---

## ⚡ Производительность

```python
import time
import sys

# Способ 1: input() — медленнее
start = time.time()
n = int(input())
data1 = [input() for _ in range(n)]
time1 = time.time() - start

# Способ 2: sys.stdin — быстрее
start = time.time()
lines = sys.stdin.readlines()
n = int(lines[0])
data2 = [line.strip() for line in lines[1:n+1]]
time2 = time.time() - start

print(f'input(): {time1:.4f}s')
print(f'sys.stdin: {time2:.4f}s')
# sys.stdin обычно в 2-3 раза быстрее!
```

**Рекомендации:**
- Используйте `sys.stdin` для больших объёмов (> 10000 строк)
- Используйте `input()` для интерактивного режима

---

## 🎯 Когда использовать stdin vs input()

### ✅ Используйте `sys.stdin`, если:

1. **Не знаете количество строк заранее**
   ```python
   data = [line.strip() for line in sys.stdin]
   ```

2. **Большой объём данных**
   ```python
   for line in sys.stdin:
       process(line)
   ```

3. **Конкурсы программирования** (Codeforces, LeetCode)
   ```python
   n = int(sys.stdin.readline())
   ```

### ✅ Используйте `input()`, если:

1. **Знаете точное количество вызовов**
   ```python
   n = int(input())
   for _ in range(n):
       line = input()
   ```

2. **Интерактивный ввод с пользователем**
   ```python
   name = input("Введите имя: ")
   ```

---

## ⚠️ Частые ошибки

```python
import sys

# ❌ Ошибка 1: Забыли strip()
for line in sys.stdin:
    numbers.append(int(line))  # ValueError! \n в конце!

# ✅ Правильно
for line in sys.stdin:
    numbers.append(int(line.strip()))

# ❌ Ошибка 2: write() без str()
sys.stdout.write(42)  # TypeError!

# ✅ Правильно
sys.stdout.write(str(42))

# ❌ Ошибка 3: Забыли \n в write()
sys.stdout.write('Hello')
sys.stdout.write('World')
# Результат: HelloWorld (на одной строке!)

# ✅ Добавляйте \n
sys.stdout.write('Hello\n')
sys.stdout.write('World\n')

# ❌ Ошибка 4: Смешивание input() и sys.stdin
n = int(input())
data = [line.strip() for line in sys.stdin]  # Может не работать правильно!

# ✅ Используйте что-то одно
lines = sys.stdin.readlines()
n = int(lines[0])
data = [line.strip() for line in lines[1:]]
```

---

## 📝 Шпаргалка

```python
import sys

# ЧТЕНИЕ
# Построчно (экономит память)
for line in sys.stdin:
    line = line.strip()
    process(line)

# Весь текст в список (без \n)
data = [line.strip() for line in sys.stdin]

# Весь текст целиком
text = sys.stdin.read()

# ЧЕРЕЗ ДЕСКРИПТОР (минимальный код)
# Все строки
for line in open(0):
    process(line.strip())

# В список
data = [line.strip() for line in open(0)]

# ЗАПИСЬ
# Обычно используйте print()
print('text')

# Низкоуровневый контроль
sys.stdout.write('text\n')
sys.stdout.flush()
```

---

## 🔗 Связанные темы

- [[01_Работа_с_текстовыми_файлами|01 — Работа с текстовыми файлами]]
- [[03_Работа_с_CSV|03 — Работа с CSV]]
- [[04_JSON|04 — JSON]]
- [[05_Pickle|05 — Pickle]]

## ❓ Вопросы / Непонятное

**Q: Почему sys.stdin быстрее input()?**
A: Потому что input() делает больше работы — выводит приглашение, буферизует данные отдельно.

**Q: Как использовать stdin в PyCharm?**
A: Через "Configure input file" или напишите данные прямо в Run → Edit Configurations → Input redirect.

**Q: Что если забыл strip()?**
A: Получишь строку с `\n` в конце. При преобразовании в число — ValueError.
