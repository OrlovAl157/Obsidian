---
tags:
  - python
  - тема/файлы
  - статус/завершён
уровень: средний
стандартная_библиотека: true
---

# 📦 Работа с ZIP архивами (ZipFile)

## 📌 Коротко

> ZipFile — встроенный модуль для создания, чтения и модификации ZIP архивов. Позволяет упаковывать файлы, получать информацию об архиве и распаковывать содержимое с поддержкой различных методов сжатия.

---

## 💡 Полезные советы

- **Всегда используйте `with`** — автоматически закрывает архив
- **Режимы**: `'r'` (чтение), `'w'` (создание, перезапись!), `'a'` (добавление), `'x'` (новый, ошибка если существует)
- **Используйте `namelist()`** — для быстрого просмотра содержимого
- **Проверяйте перед открытием** — используйте `is_zipfile()`
- **Путь в архиве** — указывайте через `/` (работает на всех ОС)
- **Сжатие** — по умолчанию включено, но можно отключить (`ZIP_STORED`)
- **Распаковка** — сохраняет структуру папок

---

## 🚀 Быстрая справка

| Задача | Метод | Пример |
|---|---|---|
| Открыть архив | `ZipFile(file, mode)` | `ZipFile('archive.zip', 'r')` |
| Список файлов | `.namelist()` | `files = zf.namelist()` |
| Информация о файлах | `.infolist()` | `infos = zf.infolist()` |
| Чтение файла | `.read(name)` | `content = zf.read('file.txt')` |
| Добавить файл | `.write(filename)` | `zf.write('local.txt')` |
| Распаковать всё | `.extractall(path)` | `zf.extractall('output')` |
| Распаковать файл | `.extract(name, path)` | `zf.extract('file.txt', 'output')` |
| Информация о файле | `.getinfo(name)` | `info = zf.getinfo('file.txt')` |
| Проверить формат | `is_zipfile(file)` | `is_zipfile('archive.zip')` |

---

## 📖 Теория

### Что такое ZIP архив?

**ZIP** — универсальный формат сжатия файлов:
- Содержит один или несколько файлов
- Может быть сжат (меньше размер) или без сжатия
- Поддерживается всеми операционными системами
- Сохраняет структуру директорий

**✅ Преимущества ZIP:**
- ✅ Универсальный формат (Windows, Mac, Linux)
- ✅ Встроенная поддержка (нужен только Python, не нужна установка)
- ✅ Сжимает файлы (экономит место)
- ✅ Сохраняет структуру папок
- ✅ Быстрая работа

**Структура ZIP архива:**
```
archive.zip
├── file1.txt
├── file2.pdf
└── folder/
    ├── file3.py
    └── subfolder/
        └── file4.json
```

### Методы сжатия

| Метод | Константа | Описание |
|---|---|---|
| Deflate | `ZIP_DEFLATED` | Сжатие (по умолчанию, хорошее сжатие) |
| Stored | `ZIP_STORED` | Без сжатия (быстрее, но больше размер) |
| Bzip2 | `ZIP_BZIP2` | Сильное сжатие (медленнее) |
| LZMA | `ZIP_LZMA` | Очень сильное сжатие (самое медленное) |

---

## 💻 Чтение архивов

### Открытие архива

```python
from zipfile import ZipFile

# Открыть для чтения (режим 'r' — по умолчанию)
with ZipFile('archive.zip', 'r') as zf:
    # Работаем с архивом
    pass

# При выходе из блока архив закроется автоматически
```

### Просмотр содержимого

```python
from zipfile import ZipFile

with ZipFile('archive.zip') as zf:
    # Таблица содержимого (красиво)
    zf.printdir()
    # File Name                         Modified    Size
    # document.txt                 2024-01-15    1024
    # folder/image.png             2024-01-14   50000
    
    # Список названий файлов и папок
    files = zf.namelist()
    # ['document.txt', 'folder/', 'folder/image.png', ...]
    
    # Список только файлов (без папок)
    files_only = [f for f in zf.namelist() if not f.endswith('/')]
```

### Получение информации о файлах

```python
from zipfile import ZipFile

with ZipFile('archive.zip') as zf:
    # Полная информация о всех файлах
    infos = zf.infolist()
    
    for info in infos:
        print(f"📄 {info.filename}")
        print(f"  Исходный размер: {info.file_size} байт")
        print(f"  Сжатый размер: {info.compress_size} байт")
        print(f"  Это папка? {info.is_dir()}")
        print(f"  Дата: {info.date_time}")
    
    # Информация об отдельном файле
    info = zf.getinfo('document.txt')
    compression_ratio = (1 - info.compress_size / info.file_size) * 100
    print(f"Сжатие: {compression_ratio:.1f}%")
```

### Чтение файла из архива

```python
from zipfile import ZipFile

with ZipFile('archive.zip') as zf:
    # Чтение в бинарном режиме
    content = zf.read('document.txt')
    print(type(content))  # <class 'bytes'>
    
    # Декодирование в текст
    text = zf.read('document.txt').decode('utf-8')
    print(text)
    
    # Или открыть как файловый объект
    with zf.open('document.txt') as f:
        text = f.read().decode('utf-8')
        # Можно строка за строкой:
        for line in f:
            print(line.decode('utf-8').strip())
```

---

## ✍️ Создание и добавление файлов

### Создание нового архива

```python
from zipfile import ZipFile

# Создать новый архив (перезапишет существующий!)
with ZipFile('archive.zip', 'w') as zf:
    # Добавить файл с его текущим именем
    zf.write('document.txt')  # Сохранится как 'document.txt'
    
    # Добавить с другим именем в архиве
    zf.write('local_file.txt', 'archive_name.txt')
    
    # Добавить с путём в архиве (структура папок)
    zf.write('image.png', 'images/photo.png')
```

### Добавление в существующий архив

```python
from zipfile import ZipFile

# Добавить в конец (режим 'a' — append)
with ZipFile('archive.zip', 'a') as zf:
    zf.write('new_file.txt')
    
    # Или добавить из строки/переменной
    zf.writestr('readme.txt', 'This is a readme file')
    zf.writestr('config.json', json.dumps(config))
```

### Выбор метода сжатия

```python
from zipfile import ZipFile, ZIP_DEFLATED, ZIP_STORED

# Сжатие (по умолчанию, хороший баланс)
with ZipFile('compressed.zip', 'w', ZIP_DEFLATED) as zf:
    zf.write('large_file.bin')  # Будет сжат

# Без сжатия (быстрее, но больше файл)
with ZipFile('uncompressed.zip', 'w', ZIP_STORED) as zf:
    zf.write('small_file.txt')  # Без сжатия
```

---

## 📂 Распаковка файлов

### Распаковать всё содержимое

```python
from zipfile import ZipFile

with ZipFile('archive.zip') as zf:
    # Распаковать в текущую директорию
    zf.extractall()
    
    # Распаковать в конкретную папку
    zf.extractall('output')
    zf.extractall('/path/to/destination')
```

### Распаковать отдельный файл

```python
from zipfile import ZipFile

with ZipFile('archive.zip') as zf:
    # Распаковать один файл
    zf.extract('document.txt')
    
    # Распаковать в конкретную папку
    zf.extract('document.txt', 'output')
    
    # Распаковать файл из подпапки архива
    zf.extract('folder/subfolder/file.txt', 'output')
```

---

## 💻 Практические примеры

### Создание архива из папки

```python
from zipfile import ZipFile
import os

def create_archive(folder_path, archive_name):
    """Создать архив из всей папки с подпапками"""
    with ZipFile(archive_name, 'w') as zf:
        for root, dirs, files in os.walk(folder_path):
            for file in files:
                file_path = os.path.join(root, file)
                # Сохранить с относительным путём (без абсолютного пути)
                arcname = os.path.relpath(file_path, folder_path)
                zf.write(file_path, arcname)

create_archive('my_project', 'backup.zip')
```

### Просмотр содержимого архива

```python
from zipfile import ZipFile

def list_archive(archive_path):
    """Красивый просмотр архива"""
    with ZipFile(archive_path) as zf:
        print(f"📦 {archive_path}")
        print("=" * 60)
        
        total_uncompressed = 0
        total_compressed = 0
        
        for info in zf.infolist():
            if info.is_dir():
                print(f"📁 {info.filename}/")
            else:
                ratio = (1 - info.compress_size / max(info.file_size, 1)) * 100
                print(f"📄 {info.filename}")
                print(f"   {info.file_size} → {info.compress_size} байт ({ratio:.0f}% сжатия)")
            
            total_uncompressed += info.file_size
            total_compressed += info.compress_size
        
        print("=" * 60)
        total_ratio = (1 - total_compressed / max(total_uncompressed, 1)) * 100
        print(f"Всего: {total_uncompressed} → {total_compressed} байт ({total_ratio:.0f}% сжатия)")

list_archive('archive.zip')
```

### Копирование архива с фильтром

```python
from zipfile import ZipFile

def filter_archive(source, destination, extensions):
    """Скопировать архив, оставляя только файлы с нужными расширениями"""
    with ZipFile(source) as src, ZipFile(destination, 'w') as dst:
        for item in src.infolist():
            # Фильтр по расширению
            if any(item.filename.endswith(ext) for ext in extensions):
                content = src.read(item.filename)
                dst.writestr(item, content)

# Копировать только текстовые файлы
filter_archive('archive.zip', 'text_only.zip', ['.txt', '.md', '.py'])
```

### Объединение архивов

```python
from zipfile import ZipFile

def merge_archives(source_files, output_archive):
    """Объединить несколько архивов в один"""
    with ZipFile(output_archive, 'w') as out:
        for archive_path in source_files:
            with ZipFile(archive_path) as src:
                for item in src.infolist():
                    content = src.read(item.filename)
                    out.writestr(item, content)

merge_archives(['archive1.zip', 'archive2.zip', 'archive3.zip'], 
               'merged.zip')
```

### Добавление файлов из памяти

```python
from zipfile import ZipFile
import json

# Создать архив с файлами, созданными в коде
data = {'name': 'Alice', 'age': 25}
text = 'This is a text file'

with ZipFile('data.zip', 'w') as zf:
    # Добавить JSON из переменной
    zf.writestr('data.json', json.dumps(data, indent=2))
    
    # Добавить простой текст
    zf.writestr('readme.txt', text)
    
    # Добавить список строк
    zf.writestr('items.txt', '\n'.join(['item1', 'item2', 'item3']))
```

---

## ⚠️ Частые ошибки

```python
from zipfile import ZipFile

# ❌ Ошибка 1: Забыли with (архив не закроется при ошибке)
zf = ZipFile('archive.zip')
files = zf.namelist()
zf.close()  # Может не выполниться при ошибке!

# ✅ Используйте with
with ZipFile('archive.zip') as zf:
    files = zf.namelist()

# ❌ Ошибка 2: Перезапись вместо добавления
with ZipFile('archive.zip', 'w') as zf:  # ❌ Перезапишет!
    zf.write('file.txt')

# ✅ Используйте 'a' для добавления
with ZipFile('archive.zip', 'a') as zf:
    zf.write('new_file.txt')

# ❌ Ошибка 3: Не проверили существование файла в архиве
with ZipFile('archive.zip') as zf:
    content = zf.read('missing.txt')  # KeyError!

# ✅ Проверьте перед чтением
with ZipFile('archive.zip') as zf:
    if 'file.txt' in zf.namelist():
        content = zf.read('file.txt')
    else:
        print("Файл не найден в архиве")

# ❌ Ошибка 4: Забыли декодировать bytes
with ZipFile('archive.zip') as zf:
    content = zf.read('text.txt')
    print(content)  # Выведет: b'...' (bytes, не строка!)

# ✅ Декодируйте
with ZipFile('archive.zip') as zf:
    text = zf.read('text.txt').decode('utf-8')
    print(text)

# ❌ Ошибка 5: Неправильный путь при добавлении
zf.write('/full/path/file.txt')  # ❌ Добавится со всем путём!

# ✅ Используйте относительный путь или arcname
zf.write('local_file.txt')  # OK
zf.write('local_file.txt', 'archive_name.txt')  # OK с переименованием
```

---

## 📝 Шпаргалка

```python
from zipfile import ZipFile, is_zipfile, ZIP_DEFLATED, ZIP_STORED

# ЧТЕНИЕ
with ZipFile('archive.zip') as zf:
    # Список файлов
    files = zf.namelist()
    
    # Чтение файла
    content = zf.read('file.txt')  # bytes
    text = zf.read('file.txt').decode('utf-8')  # строка
    
    # Распаковка
    zf.extractall('output')
    zf.extract('file.txt', 'output')

# СОЗДАНИЕ
with ZipFile('new.zip', 'w') as zf:
    zf.write('file.txt')  # Добавить файл
    zf.writestr('readme.txt', 'content')  # Добавить текст

# ДОБАВЛЕНИЕ
with ZipFile('archive.zip', 'a') as zf:
    zf.write('new_file.txt')

# ПРОВЕРКА
if is_zipfile('file.zip'):
    print("ZIP архив")
```

---

## 🔗 Связанные темы

- [[00 — Обзор работы с файлами]]

## ❓ Вопросы / Непонятное
