# 🗜️ Памятка по работе с ZIP файлами в Python

## 📋 Быстрая справка: Основные методы

| Метод | Назначение | Режим | Пример использования |
|-------|-----------|-------|---------------------|
| `ZipFile(file, 'w')` | Создать новый архив | Запись | `with ZipFile('arc.zip', 'w') as z:` |
| `ZipFile(file, 'r')` | Открыть для чтения | Чтение | `with ZipFile('arc.zip', 'r') as z:` |
| `ZipFile(file, 'a')` | Добавить в архив | Добавление | `with ZipFile('arc.zip', 'a') as z:` |
| `.write(file)` | Добавить файл в архив | Запись | `zipf.write('file.txt')` |
| `.extractall(path)` | Распаковать весь архив | Чтение | `zipf.extractall('folder')` |
| `.extract(file)` | Распаковать один файл | Чтение | `zipf.extract('file.txt')` |
| `.read(file)` | Прочитать файл (bytes) | Чтение | `data = zipf.read('file.txt')` |
| `.namelist()` | Список файлов в архиве | Чтение | `files = zipf.namelist()` |

## 🔑 Ключевые параметры ZipFile

| Параметр | По умолчанию | Описание | Пример |
|----------|--------------|----------|--------|
| `mode` | 'r' | Режим открытия ('r', 'w', 'a', 'x') | `mode='w'` |
| `compression` | ZIP_STORED | Метод сжатия | `compression=zipfile.ZIP_DEFLATED` |
| `compresslevel` | None | Уровень сжатия (0-9) | `compresslevel=9` |
| `allowZip64` | True | Поддержка больших файлов (>2GB) | `allowZip64=True` |

## 📚 Теория

### Что такое ZIP?

**ZIP** — формат сжатия и архивирования данных. Позволяет объединить несколько файлов в один архив с возможностью сжатия.

**Преимущества ZIP:**

✅ Широкая поддержка на всех платформах  
✅ Возможность сжатия данных  
✅ Чтение файлов без полной распаковки  
✅ Поддержка защиты паролем  
✅ Сохранение структуры папок  
✅ Встроенный модуль `zipfile` в Python

### 🔤 Режимы открытия архива

| Режим | Название | Описание | Что происходит с существующим файлом |
|-------|----------|----------|-------------------------------------|
| `'r'` | Read | Чтение | Открывается для чтения |
| `'w'` | Write | Запись | Создается новый (старый удаляется) |
| `'a'` | Append | Добавление | Добавляется к существующему |
| `'x'` | Exclusive | Эксклюзивное создание | Ошибка, если уже существует |

### 🗜️ Методы сжатия

| Константа | Значение | Описание | Скорость | Степень сжатия |
|-----------|----------|----------|----------|----------------|
| `ZIP_STORED` | 0 | Без сжатия | ⚡⚡⚡ Быстро | ❌ Нет |
| `ZIP_DEFLATED` | 8 | Стандартное (DEFLATE) | ⚡⚡ Средне | ✅ Хорошо |
| `ZIP_BZIP2` | 12 | BZIP2 | ⚡ Медленно | ✅✅ Отлично |
| `ZIP_LZMA` | 14 | LZMA | 🐌 Очень медленно | ✅✅✅ Максимум |

⚠️ **Важно:** `ZIP_DEFLATED` — наиболее используемый метод (баланс скорости и сжатия)

## 💻 Практические примеры

### 1. Создание архива (один файл)

```python
import zipfile

# Создать архив и добавить файл
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.write('document.txt')
```

### 2. Создание архива (несколько файлов)

```python
import zipfile

files = ['file1.txt', 'file2.txt', 'data.csv']

with zipfile.ZipFile('archive.zip', 'w', compression=zipfile.ZIP_DEFLATED) as zipf:
    for file in files:
        zipf.write(file)
```

### 3. Создание архива с переименованием

```python
import zipfile

# Файл будет сохранен под другим именем в архиве
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.write('path/to/file.txt', arcname='renamed.txt')
```

### 4. Архивирование целой папки (рекурсивно)

```python
import zipfile
import os

def zip_folder(folder_path, output_zip):
    """Архивирует папку со всеми подпапками"""
    with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as zipf:
        for root, dirs, files in os.walk(folder_path):
            for file in files:
                file_path = os.path.join(root, file)
                # Относительный путь в архиве
                arcname = os.path.relpath(file_path, folder_path)
                zipf.write(file_path, arcname)

# Использование
zip_folder('my_project', 'project.zip')
```

### 5. Распаковка всего архива

```python
import zipfile

# Распаковать все файлы
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extractall('extracted_folder')
```

### 6. Распаковка конкретного файла

```python
import zipfile

# Распаковать только один файл
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extract('document.txt', path='output')
```

### 7. Чтение файла без распаковки

```python
import zipfile

# Прочитать содержимое в память
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    content = zipf.read('document.txt')  # bytes
    text = content.decode('utf-8')       # str
    print(text)
```

### 8. Построчное чтение файла из архива

```python
import zipfile

# Открыть файл как file-like объект
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    with zipf.open('document.txt') as f:
        for line in f:
            print(line.decode('utf-8').strip())
```

### 9. Просмотр содержимого архива

```python
import zipfile

# Получить список всех файлов
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    files = zipf.namelist()
    print(files)
    # ['file1.txt', 'folder/file2.txt', 'data.csv']
```

### 10. Детальная информация о файлах

```python
import zipfile

with zipfile.ZipFile('archive.zip', 'r') as zipf:
    for info in zipf.infolist():
        print(f"Файл: {info.filename}")
        print(f"Размер: {info.file_size} байт")
        print(f"Сжат до: {info.compress_size} байт")
        print(f"Сжатие: {100 - (info.compress_size / info.file_size * 100):.1f}%")
        print(f"Дата: {info.date_time}")
        print("-" * 50)
```

### 11. Добавление файлов в существующий архив

```python
import zipfile

# Режим 'a' (append) - добавление
with zipfile.ZipFile('archive.zip', 'a') as zipf:
    zipf.write('new_file.txt')
```

### 12. Запись данных напрямую в архив

```python
import zipfile

# Создать файл в архиве без создания на диске
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.writestr('generated.txt', 'Это сгенерированный текст')
    
    # С указанием метаданных
    import time
    info = zipfile.ZipInfo('my_file.txt')
    info.date_time = time.localtime()[:6]
    info.compress_type = zipfile.ZIP_DEFLATED
    zipf.writestr(info, 'Содержимое файла')
```

### 13. Работа с паролями

```python
import zipfile

# Чтение защищенного архива
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.setpassword(b'password123')
    content = zipf.read('secret.txt')

# Распаковка с паролем
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.extractall(pwd=b'password123')
```

⚠️ **Важно:** `zipfile` может **читать** защищенные архивы, но **не может создавать** их. Для создания используйте библиотеку `pyminizip`.

### 14. Проверка целостности архива

```python
import zipfile

# Проверить архив на ошибки
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    result = zipf.testzip()
    if result is None:
        print("✅ Архив в порядке")
    else:
        print(f"❌ Поврежден файл: {result}")

# Проверить, является ли файл ZIP-архивом
is_valid = zipfile.is_zipfile('file.zip')
print(f"Это ZIP? {is_valid}")
```

### 15. Настройка уровня сжатия

```python
import zipfile

# Уровень сжатия от 0 (без сжатия) до 9 (максимум)
with zipfile.ZipFile('archive.zip', 'w', zipfile.ZIP_DEFLATED, compresslevel=9) as zipf:
    zipf.write('large_file.txt')
```

### 16. Фильтрация файлов при распаковке

```python
import zipfile

# Распаковать только .txt файлы
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    for filename in zipf.namelist():
        if filename.endswith('.txt'):
            zipf.extract(filename, 'output')
```

### 17. Использование Path из pathlib

```python
import zipfile
from pathlib import Path

# Архивирование всех .py файлов в проекте
with zipfile.ZipFile('code.zip', 'w') as zipf:
    base_path = Path('my_project')
    for file_path in base_path.rglob('*.py'):
        zipf.write(file_path, arcname=file_path.relative_to(base_path.parent))
```

### 18. Обработка ошибок

```python
import zipfile

try:
    with zipfile.ZipFile('archive.zip', 'r') as zipf:
        zipf.extractall()
except zipfile.BadZipFile:
    print("❌ Файл поврежден или не является ZIP")
except FileNotFoundError:
    print("❌ Архив не найден")
except PermissionError:
    print("❌ Нет прав доступа")
except Exception as e:
    print(f"❌ Ошибка: {e}")
```

## ⚠️ Важные ограничения и особенности

### 1. Размер архива

- **ZIP64**: По умолчанию включен, позволяет работать с файлами >4GB
- Без ZIP64 максимальный размер архива — 2GB

### 2. Защита паролем

❌ **Нельзя создавать** защищенные паролем архивы через `zipfile`  
✅ **Можно читать** защищенные архивы  
✅ Для создания используйте `pyminizip` или `pyzipper`

### 3. Кодировка имен файлов

```python
# При работе с кириллицей
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.write('файл.txt')  # Работает корректно в Python 3
```

### 4. Относительные и абсолютные пути

```python
# Плохо: сохраняет полный путь
zipf.write('/home/user/docs/file.txt')  
# В архиве: /home/user/docs/file.txt

# Хорошо: используйте arcname
zipf.write('/home/user/docs/file.txt', arcname='file.txt')
# В архиве: file.txt
```

## 🎯 Лучшие практики

### ✅ Рекомендуется

**1. Всегда используйте контекстный менеджер `with`:**
```python
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.write('file.txt')
# Автоматически закроется
```

**2. Указывайте метод сжатия:**
```python
with zipfile.ZipFile('archive.zip', 'w', compression=zipfile.ZIP_DEFLATED) as zipf:
    zipf.write('file.txt')
```

**3. Используйте `arcname` для контроля структуры:**
```python
zipf.write('long/path/to/file.txt', arcname='file.txt')
```

**4. Обрабатывайте исключения:**
```python
try:
    with zipfile.ZipFile('archive.zip', 'r') as zipf:
        zipf.extractall()
except zipfile.BadZipFile:
    print("Поврежденный архив")
```

**5. Проверяйте архив перед распаковкой:**
```python
if zipfile.is_zipfile('file.zip'):
    with zipfile.ZipFile('file.zip', 'r') as zipf:
        if zipf.testzip() is None:
            zipf.extractall()
```

### ❌ Не рекомендуется

❌ Открывать архив без `with`  
❌ Игнорировать исключения  
❌ Распаковывать архивы без проверки (zip bomb)  
❌ Забывать про `arcname` при архивировании  
❌ Использовать `ZIP_STORED` для больших файлов

## 🔍 Полезные функции-хелперы

### Получить общий размер архива

```python
def get_zip_total_size(zip_path):
    """Возвращает суммарный размер всех файлов в архиве"""
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        total = sum(info.file_size for info in zipf.infolist())
        return total

size = get_zip_total_size('archive.zip')
print(f"Общий размер: {size / 1024 / 1024:.2f} MB")
```

### Извлечь только определенные файлы

```python
def extract_by_extension(zip_path, extension, output_dir):
    """Распаковывает файлы с определенным расширением"""
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        for filename in zipf.namelist():
            if filename.endswith(extension):
                zipf.extract(filename, output_dir)

extract_by_extension('archive.zip', '.txt', 'output')
```

### Объединить несколько архивов

```python
def merge_zip_files(zip_files, output_zip):
    """Объединяет несколько ZIP архивов в один"""
    with zipfile.ZipFile(output_zip, 'w', zipfile.ZIP_DEFLATED) as output:
        for zip_file in zip_files:
            with zipfile.ZipFile(zip_file, 'r') as input_zip:
                for filename in input_zip.namelist():
                    data = input_zip.read(filename)
                    output.writestr(filename, data)

merge_zip_files(['archive1.zip', 'archive2.zip'], 'combined.zip')
```

### Показать дерево файлов

```python
def print_zip_tree(zip_path):
    """Выводит структуру архива в виде дерева"""
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        for info in zipf.infolist():
            level = info.filename.count('/')
            indent = "  " * level
            name = info.filename.split('/')[-1]
            size = f"({info.file_size} bytes)" if not info.is_dir() else ""
            print(f"{indent}{name} {size}")

print_zip_tree('archive.zip')
```

### Получить информацию о степени сжатия

```python
def get_compression_info(zip_path):
    """Показывает статистику сжатия"""
    with zipfile.ZipFile(zip_path, 'r') as zipf:
        total_size = 0
        compressed_size = 0
        
        for info in zipf.infolist():
            if not info.is_dir():
                total_size += info.file_size
                compressed_size += info.compress_size
        
        ratio = 100 - (compressed_size / total_size * 100) if total_size > 0 else 0
        
        print(f"Оригинальный размер: {total_size / 1024:.2f} KB")
        print(f"Сжатый размер: {compressed_size / 1024:.2f} KB")
        print(f"Степень сжатия: {ratio:.1f}%")

get_compression_info('archive.zip')
```

## 📝 Сводная шпаргалка

```python
import zipfile
import os

# ═══ СОЗДАНИЕ АРХИВА ═══
# Простое создание
with zipfile.ZipFile('archive.zip', 'w', zipfile.ZIP_DEFLATED) as zipf:
    zipf.write('file.txt')
    zipf.write('data.csv', arcname='renamed.csv')

# Архивировать папку
def zip_folder(folder, output):
    with zipfile.ZipFile(output, 'w', zipfile.ZIP_DEFLATED) as zipf:
        for root, dirs, files in os.walk(folder):
            for file in files:
                path = os.path.join(root, file)
                zipf.write(path, os.path.relpath(path, folder))

# ═══ РАСПАКОВКА ═══
# Распаковать всё
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extractall('output_folder')

# Распаковать один файл
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    zipf.extract('file.txt', 'output')

# ═══ ЧТЕНИЕ БЕЗ РАСПАКОВКИ ═══
# Прочитать в память
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    content = zipf.read('file.txt')
    text = content.decode('utf-8')

# Построчное чтение
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    with zipf.open('file.txt') as f:
        for line in f:
            print(line.decode('utf-8'))

# ═══ ИНФОРМАЦИЯ ═══
# Список файлов
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    files = zipf.namelist()
    
# Детали о файлах
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    for info in zipf.infolist():
        print(info.filename, info.file_size, info.compress_size)

# ═══ ПРОВЕРКА ═══
# Проверить целостность
with zipfile.ZipFile('archive.zip', 'r') as zipf:
    result = zipf.testzip()  # None = OK

# Проверить, является ли ZIP
is_zip = zipfile.is_zipfile('file.zip')

# ═══ ДОБАВЛЕНИЕ ═══
# Добавить в существующий
with zipfile.ZipFile('archive.zip', 'a') as zipf:
    zipf.write('new_file.txt')

# ═══ ЗАПИСЬ НАПРЯМУЮ ═══
with zipfile.ZipFile('archive.zip', 'w') as zipf:
    zipf.writestr('generated.txt', 'Content')

# ═══ С ПАРОЛЕМ ═══
with zipfile.ZipFile('protected.zip', 'r') as zipf:
    zipf.setpassword(b'password')
    content = zipf.read('secret.txt')
```

## 🔄 Таблица методов класса ZipFile

| Метод | Описание | Возвращает |
|-------|----------|-----------|
| `.write(filename, arcname)` | Добавить файл в архив | None |
| `.writestr(filename, data)` | Записать данные напрямую | None |
| `.read(name)` | Прочитать файл | bytes |
| `.open(name, mode)` | Открыть файл | file-like object |
| `.extract(member, path)` | Извлечь один файл | str (путь) |
| `.extractall(path, members)` | Извлечь все файлы | None |
| `.namelist()` | Список имен файлов | list[str] |
| `.infolist()` | Детальная информация | list[ZipInfo] |
| `.getinfo(name)` | Информация о файле | ZipInfo |
| `.testzip()` | Проверить целостность | str или None |
| `.setpassword(pwd)` | Установить пароль | None |
| `.printdir()` | Вывести содержимое | None |

## 💡 Запомнить

1. **ZIP** — формат архивирования и сжатия данных
2. Модуль **zipfile** встроен в Python
3. Всегда используйте **`with`** для автоматического закрытия
4. **`ZIP_DEFLATED`** — стандартный метод сжатия
5. **`arcname`** контролирует имя файла в архиве
6. Можно читать файлы **без распаковки** всего архива
7. **`zipfile`** читает защищенные архивы, но не создает их
8. Всегда обрабатывайте **`BadZipFile`** исключение
9. **`compresslevel=9`** — максимальное сжатие
10. Используйте **`os.walk()`** для рекурсивной архивации папок

## 🌐 Полезные ссылки

- [Официальная документация Python](https://docs.python.org/3/library/zipfile.html)
- [Спецификация ZIP формата](https://pkware.cachefly.net/webdocs/casestudies/APPNOTE.TXT)
- [PyPI: pyminizip](https://pypi.org/project/pyminizip/) — для создания защищенных архивов
- [PyPI: pyzipper](https://pypi.org/project/pyzipper/) — альтернатива с поддержкой паролей