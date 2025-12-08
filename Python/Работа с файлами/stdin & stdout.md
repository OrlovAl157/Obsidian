# Памятка по sys.stdin и sys.stdout в Python

## Быстрая справка

|Операция               |Синтаксис                             |Описание                    |
|-----------------------|--------------------------------------|----------------------------|
|**Импорт**             |`import sys`                          |Подключение модуля          |
|**Построчное чтение**  |`for line in sys.stdin`               |Итерация по строкам         |
|**Все строки (без \n)**|`[line.strip() for line in sys.stdin]`|Список без переносов        |
|**Все строки (с \n)**  |`sys.stdin.readlines()`               |Список с переносами         |
|**Весь текст**         |`sys.stdin.read()`                    |Одна большая строка         |
|**Запись в stdout**    |`sys.stdout.write('text\n')`          |Вывод без автоформатирования|
|**Сброс буфера**       |`sys.stdout.flush()`                  |Принудительный вывод        |

## Теория

### Что такое потоки ввода-вывода?

**Поток (stream)** — это абстракция для работы с данными, которые поступают последовательно (как поток воды).

В Python есть три стандартных потока:

- **stdin** (standard input) — поток ввода
- **stdout** (standard output) — поток вывода
- **stderr** (standard error) — поток ошибок

### Что такое sys.stdin?

**sys.stdin** — объект файлового типа для чтения пользовательского ввода. Это итератор, который:

**Особенности:**

- 📖 **Движется только вперёд** — нельзя “перемотать назад”
- 🗑️ **Прочитанные данные удаляются** — нельзя прочитать дважды
- 📝 **Выдает строки с `\n`** — каждая строка заканчивается символом перевода
- ⏹️ **Заканчивается сигналом EOF** (End Of File)
- 🔄 **Ленивая обработка** — данные читаются по мере необходимости

### Что такое sys.stdout?

**sys.stdout** — объект файлового типа для вывода данных. Это то место, куда `print()` отправляет свои данные.

**Особенности:**

- 📤 **Поток вывода** — данные идут на экран или в файл
- 🔄 **Буферизация** — данные могут накапливаться перед выводом
- ✍️ **Низкоуровневый контроль** — можно управлять каждым символом
- 🔀 **Перенаправление** — можно отправить вывод в файл

### Сигнал EOF (End Of File)

**EOF** — специальный сигнал, означающий конец ввода.

|Платформа            |Комбинация клавиш     |
|---------------------|----------------------|
|**Linux / macOS**    |`Ctrl + D`            |
|**Windows (консоль)**|`Ctrl + Z` → `Enter`  |
|**PyCharm**          |`Ctrl + D`            |
|**VS Code**          |`Ctrl + D`            |
|**Wing IDE**         |ПКМ → Send EOF → Enter|

### Буферизация

**Буферизация** — накопление данных перед отправкой для повышения производительности.

**Виды буферизации:**

- **Построчная** — вывод после каждого `\n`
- **Полная** — вывод при заполнении буфера или завершении программы
- **Без буферизации** — немедленный вывод каждого символа

```python
import sys

# Принудительный сброс буфера
sys.stdout.flush()
```

### Связь с input() и print()

```python
# input() — это обёртка над sys.stdin
input()  ≈  sys.stdin.readline().strip()

# print() — это обёртка над sys.stdout
print(x)  ≈  sys.stdout.write(str(x) + '\n')
```

## Чтение из sys.stdin

### Импорт модуля

```python
import sys
```

### Способ 1: Построчное чтение через цикл

```python
import sys

for line in sys.stdin:
    line = line.strip()  # Убираем \n в конце
    print(f"Прочитано: {line}")
```

**Когда использовать:**

- ✅ Обработка каждой строки отдельно
- ✅ Большой объём данных
- ✅ Экономия памяти

### Способ 2: Все строки в список (без \n)

**Через списковое выражение:**

```python
import sys

data = [line.strip() for line in sys.stdin]
print(data)  # ['строка1', 'строка2', ...]
```

**Через map():**

```python
import sys

data = list(map(str.strip, sys.stdin))
print(data)  # ['строка1', 'строка2', ...]
```

**Когда использовать:**

- ✅ Нужен весь ввод сразу
- ✅ Небольшой объём данных
- ✅ Нужна индексация или сортировка

### Способ 3: readlines() — список строк с \n

```python
import sys

data = sys.stdin.readlines()
print(data)  # ['строка1\n', 'строка2\n', ...]
```

**Особенности:**

- Сохраняет символы `\n`
- Читает весь ввод в память
- Возвращает список

### Способ 4: read() — весь текст целиком

```python
import sys

data = sys.stdin.read()
print(data)  # 'строка1\nстрока2\n...'
```

**Особенности:**

- Возвращает одну большую строку
- Все символы `\n` сохраняются
- Можно разбить: `data.splitlines()`

### Способ 5: readline() — одна строка

```python
import sys

line = sys.stdin.readline()  # Читает одну строку с \n
line = line.strip()           # Убираем \n
print(line)
```

**Когда использовать:**

- ✅ Нужна только первая строка
- ✅ Построчная обработка с условием остановки

## Запись в sys.stdout

### Базовый вывод

```python
import sys

print('Hello')              # Автоматически добавляет \n
sys.stdout.write('World')   # НЕ добавляет \n
sys.stdout.write('\n')      # Добавляем вручную
```

**Результат:**

```
Hello
World
```

### Отличия print() от sys.stdout.write()

|Характеристика          |print()        |sys.stdout.write()            |
|------------------------|---------------|------------------------------|
|**Добавляет \n**        |✅ Автоматически|❌ Вручную                     |
|**Преобразует типы**    |✅ Автоматически|❌ Нужен str()                 |
|**Несколько аргументов**|✅ Да           |❌ Только один                 |
|**Разделитель**         |✅ sep=’ ’      |❌ Нет                         |
|**Простота**            |✅ Удобнее      |⚠️ Сложнее                     |
|**Контроль**            |⚠️ Меньше       |✅ Больше                      |
|**Возвращает**          |None           |Количество записанных символов|

### Примеры с типами данных

```python
import sys

# ❌ Ошибка — write() принимает только строки
try:
    sys.stdout.write(42)
except TypeError as e:
    print(e)  # write() argument must be str, not int

# ✅ Правильно — преобразуем в строку
sys.stdout.write(str(42))
sys.stdout.write('\n')

# print() делает это автоматически
print(42)
```

### Сброс буфера

```python
import sys
import time

# Без flush — может не вывести сразу
for i in range(5):
    sys.stdout.write(f'{i}...')
    time.sleep(1)

print()

# С flush — выводит немедленно
for i in range(5):
    sys.stdout.write(f'{i}...')
    sys.stdout.flush()  # Принудительный вывод
    time.sleep(1)
```

## Когда использовать stdin vs input()

### ✅ Используй sys.stdin, если:

1. **Не знаешь количество строк заранее**
   
   ```python
   # Пользователь вводит, пока не нажмёт EOF
   data = [line.strip() for line in sys.stdin]
   ```
2. **Нужно обработать весь ввод сразу**
   
   ```python
   text = sys.stdin.read()
   ```
3. **Данные через перенаправление**
   
   ```bash
   python script.py < input.txt
   ```
4. **Большой объём данных**
   
   ```python
   # Эффективная построчная обработка
   for line in sys.stdin:
       process(line)
   ```
5. **Онлайн-судьи (Codeforces, LeetCode)**
   
   ```python
   # Часто требуют stdin для тестирования
   ```

### ✅ Используй input(), если:

1. **Знаешь точное количество вызовов**
   
   ```python
   n = int(input())
   for _ in range(n):
       line = input()
   ```
2. **Интерактивный ввод с пользователем**
   
   ```python
   name = input("Введите имя: ")
   ```
3. **Работаешь с конкретными значениями**
   
   ```python
   age = int(input())
   ```

## Перенаправление потоков

### Перенаправление stdout в файл

```python
import sys

# Сохраняем исходный поток
original_stdout = sys.stdout

# Перенаправляем в файл
with open('output.txt', 'w') as f:
    sys.stdout = f
    print('Эта строка запишется в файл')
    print('И эта тоже')

# Восстанавливаем вывод на экран
sys.stdout = original_stdout
print('Теперь снова на экран')
```

### Перенаправление stdin из файла

```python
import sys

# Сохраняем исходный поток
original_stdin = sys.stdin

# Перенаправляем из файла
with open('input.txt', 'r') as f:
    sys.stdin = f
    data = [line.strip() for line in sys.stdin]

# Восстанавливаем ввод с клавиатуры
sys.stdin = original_stdin
print(data)
```

### Использование контекстного менеджера (безопаснее)

```python
import sys
from contextlib import redirect_stdout, redirect_stdin

# Перенаправление stdout
with open('output.txt', 'w') as f:
    with redirect_stdout(f):
        print('В файл')

# Перенаправление stdin
with open('input.txt', 'r') as f:
    with redirect_stdin(f):
        data = input()
```

## Практические примеры

### Пример 1: Суммирование чисел

```python
import sys

# Вариант 1: Построчное чтение
total = 0
for line in sys.stdin:
    total += int(line.strip())
print(total)

# Вариант 2: Списковое выражение
numbers = [int(line.strip()) for line in sys.stdin]
print(sum(numbers))

# Вариант 3: map()
numbers = list(map(int, sys.stdin))
print(sum(numbers))
```

**Ввод:**

```
10
20
30
```

**Вывод:**

```
60
```

### Пример 2: Фильтрация строк

```python
import sys

# Все строки длиннее 5 символов
data = sys.stdin.read()
lines = [line for line in data.splitlines() if len(line) > 5]

for line in lines:
    print(line)
```

**Ввод:**

```
hi
hello
world
a
python
```

**Вывод:**

```
hello
python
```

### Пример 3: Обработка CSV

```python
import sys

for line in sys.stdin:
    values = line.strip().split(',')
    name, age = values[0], values[1]
    print(f'Имя: {name}, Возраст: {age}')
```

**Ввод:**

```
Alice,25
Bob,30
Charlie,35
```

**Вывод:**

```
Имя: Alice, Возраст: 25
Имя: Bob, Возраст: 30
Имя: Charlie, Возраст: 35
```

### Пример 4: Подсчет слов

```python
import sys

text = sys.stdin.read()
words = text.split()

print(f'Всего слов: {len(words)}')
print(f'Уникальных слов: {len(set(words))}')
```

### Пример 5: Обработка пустых строк

```python
import sys

for line in sys.stdin:
    line = line.strip()
    
    if not line:  # Пропускаем пустые строки
        continue
    
    print(f'Обработано: {line}')
```

### Пример 6: Чтение до ключевого слова

```python
import sys

lines = []
for line in sys.stdin:
    line = line.strip()
    
    if line == 'STOP':  # Останавливаемся на STOP
        break
    
    lines.append(line)

print(f'Прочитано строк: {len(lines)}')
```

### Пример 7: Построчная статистика

```python
import sys

line_count = 0
char_count = 0
word_count = 0

for line in sys.stdin:
    line = line.strip()
    line_count += 1
    char_count += len(line)
    word_count += len(line.split())

print(f'Строк: {line_count}')
print(f'Символов: {char_count}')
print(f'Слов: {word_count}')
```

### Пример 8: Форматированный вывод с write()

```python
import sys

# С print()
for i in range(5):
    print(f'Число {i}', end=' ')

print()  # Перевод строки

# С write()
for i in range(5):
    sys.stdout.write(f'Число {i} ')

sys.stdout.write('\n')
```

## Производительность

### Сравнение input() vs sys.stdin

```python
import time
import sys

# С input() — медленнее
start = time.time()
n = int(input())
data1 = [input() for _ in range(n)]
time1 = time.time() - start

# С sys.stdin — быстрее
start = time.time()
lines = sys.stdin.readlines()
n = int(lines[0])
data2 = [line.strip() for line in lines[1:n+1]]
time2 = time.time() - start

print(f'input(): {time1:.4f}s')
print(f'sys.stdin: {time2:.4f}s')
# sys.stdin обычно в 2-3 раза быстрее
```

**Рекомендации:**

- Для больших объёмов данных (> 10000 строк) используйте `sys.stdin`
- Для интерактивного ввода используйте `input()`

## Работа с sys.stderr

**sys.stderr** — поток для вывода ошибок (не смешивается с основным выводом).

```python
import sys

# Обычный вывод
print('Обычное сообщение')

# Вывод ошибки
print('Ошибка!', file=sys.stderr)

# Или напрямую
sys.stderr.write('Критическая ошибка!\n')
```

**Перенаправление:**

```bash
# Только обычный вывод в файл
python script.py > output.txt

# Только ошибки в файл
python script.py 2> errors.txt

# И то, и другое
python script.py > output.txt 2> errors.txt
```

## Режимы работы с терминалом

### Интерактивный режим

```bash
# Программа ждёт ввода от пользователя
python script.py
```

### Перенаправление из файла

```bash
# Читает данные из input.txt
python script.py < input.txt
```

### Перенаправление в файл

```bash
# Записывает вывод в output.txt
python script.py > output.txt
```

### Pipe (конвейер)

```bash
# Передаёт вывод одной программы в другую
cat input.txt | python script.py
echo "Hello" | python script.py
```

## Распространенные ошибки

### ❌ Ошибка 1: Забыли strip()

```python
# ❌ Плохо — остаётся \n
for line in sys.stdin:
    numbers.append(int(line))  # ValueError!

# ✅ Хорошо
for line in sys.stdin:
    numbers.append(int(line.strip()))
```

### ❌ Ошибка 2: Смешивание input() и sys.stdin

```python
# ❌ Плохо — может работать некорректно
n = int(input())
data = [line.strip() for line in sys.stdin]

# ✅ Хорошо — используйте что-то одно
lines = sys.stdin.readlines()
n = int(lines[0])
data = [line.strip() for line in lines[1:]]
```

### ❌ Ошибка 3: write() без str()

```python
# ❌ Ошибка
sys.stdout.write(42)  # TypeError!

# ✅ Правильно
sys.stdout.write(str(42))
```

### ❌ Ошибка 4: Забыли \n в write()

```python
# ❌ Всё на одной строке
sys.stdout.write('Hello')
sys.stdout.write('World')
# HelloWorld

# ✅ Правильно
sys.stdout.write('Hello\n')
sys.stdout.write('World\n')
# Hello
# World
```

## Полезные ссылки

- [Документация: sys](https://docs.python.org/3/library/sys.html)
- [Документация: stdin/stdout](https://docs.python.org/3/library/sys.html#sys.stdin)
- [Перенаправление потоков](https://docs.python.org/3/library/contextlib.html#contextlib.redirect_stdout)