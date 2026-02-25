

## Что такое stdin?

**sys.stdin** — поток ввода, куда попадает весь текст от пользователя. Это **итератор**, который:

- Движется только вперед
- Прочитанные данные удаляются безвозвратно
- Выдает строки, введенные пользователем
- Заканчивается при завершении ввода (Ctrl+D в Linux/Mac, Ctrl+Z→Enter в Windows)

-----

## Чтение из stdin

### Импорт модуля

```python
import sys
```

### Построчное чтение через цикл

```python
import sys

for line in sys.stdin:
    print(line.strip())  # убираем \n в конце
```

### Чтение всех строк в список

**Через списочное выражение:**

```python
import sys

data = [line.strip() for line in sys.stdin]
```

**Через map():**

```python
import sys

data = list(map(str.strip, sys.stdin))
```

### Метод readlines() — список строк с \n

```python
import sys

data = sys.stdin.readlines()
# ['строка1\n', 'строка2\n', ...]
```

### Метод read() — весь текст целиком

```python
import sys

data = sys.stdin.read()
# 'строка1\nстрока2\n...'
```

-----

## Когда использовать stdin?

✅ **Используй stdin**, если:

- Не знаешь количество строк заранее
- Нужно обработать весь ввод сразу
- Данные поступают через перенаправление (`python script.py < input.txt`)

❌ **Используй input()**, если:

- Знаешь точное количество вызовов
- Нужен интерактивный ввод
- Работаешь с конкретными значениями

-----

## Потоковый вывод stdout

**sys.stdout** — поток вывода, куда по умолчанию пишет `print()`

### Запись в stdout

```python
import sys

print('Hello')              # автоматически добавляет \n
sys.stdout.write('World')   # НЕ добавляет \n
sys.stdout.write('\n')      # добавляем вручную
```

**Результат:**

```
Hello
World
```

### Важные отличия от print()

|`print()`                         |`sys.stdout.write()`          |
|----------------------------------|------------------------------|
|Автоматически добавляет `\n`      |Нужно добавлять `\n` вручную  |
|Преобразует в строку автоматически|Нужно вручную вызывать `str()`|
|Удобнее для простого вывода       |Более низкоуровневый контроль |

### Пример с числами

```python
import sys

# ❌ Ошибка:
sys.stdout.write(42)

# ✅ Правильно:
sys.stdout.write(str(42))
```

-----

## Перенаправление вывода в файл

```python
import sys

# Сохраняем исходный поток
original_stdout = sys.stdout

# Перенаправляем в файл
sys.stdout = open('output.txt', 'w')
print('Эта строка запишется в файл')
print('И эта тоже')

# Закрываем файл
sys.stdout.close()

# Восстанавливаем вывод на экран
sys.stdout = original_stdout
print('Теперь снова на экран')
```

-----

## Практические примеры

### Пример 1: Суммирование чисел из stdin

```python
import sys

numbers = [int(line.strip()) for line in sys.stdin]
print(sum(numbers))
```

### Пример 2: Фильтрация строк

```python
import sys

data = sys.stdin.read()
lines = [line for line in data.splitlines() if len(line) > 5]
for line in lines:
    print(line)
```

### Пример 3: Обработка CSV из stdin

```python
import sys

for line in sys.stdin:
    values = line.strip().split(',')
    print(f'Имя: {values[0]}, Возраст: {values[1]}')
```

-----

## Завершение ввода

|Платформа        |Комбинация клавиш     |
|-----------------|----------------------|
|Linux / macOS    |`Ctrl + D`            |
|Windows (консоль)|`Ctrl + Z` → `Enter`  |
|PyCharm          |`Ctrl + D`            |
|Wing IDE         |ПКМ → Send EOF → Enter|

-----

## Шпаргалка

```python
import sys

# Все строки в список (без \n)
data = [line.strip() for line in sys.stdin]

# Все строки в список (с \n)
data = sys.stdin.readlines()

# Весь текст целиком
text = sys.stdin.read()

# Построчная обработка
for line in sys.stdin:
    print(line.strip())

# Запись в stdout
sys.stdout.write('Текст\n')
```

-----

## Связь с input() и print()

```python
# input() ≈ sys.stdin.readline().strip()
# print(x) ≈ sys.stdout.write(str(x) + '\n')
```