
## 📋 Описание
`maketrans()` создает **таблицу перевода** для замены множества символов за один проход. Таблица используется с методом `translate()`.

## 🎯 Основные способы использования

### 1. Две строки-аргумента
```python
intab = "aeiou"      # Что заменять
outtab = "12345"     # На что заменять
trantab = str.maketrans(intab, outtab)

text = "this is string example"
result = text.translate(trantab)
print(result)  # th3s 3s str3ng 2x1mpl2
```

### 2. Словарь замен
```python
# Более гибкий способ
replace_dict = {'a': '1', 'b': '2', 'c': '3'}
trantab = str.maketrans(replace_dict)

text = "abc hello"
result = text.translate(trantab)
print(result)  # 123 hello
```

### 3. Три аргумента (удаление символов)
```python
intab = "abc"
outtab = "123"
del_chars = "xyz"    # Что удалить

trantab = str.maketrans(intab, outtab, del_chars)
text = "abcxyz hello"
result = text.translate(trantab)
print(result)  # 123 hello
```

## ⚠️ Важные правила
- `intab` и `outtab` должны быть **одинаковой длины**
- Символы, не указанные в таблице, **остаются без изменений**
- Удобно для массовых замен символов

## 💡 Практическое применение
```python
# Шифр Цезаря (сдвиг на 1)
import string
intab = string.ascii_lowercase
outtab = intab[1:] + intab[0]  # bcdef...a
trantab = str.maketrans(intab, outtab)

text = "hello world"
print(text.translate(trantab))  # ifmmp xpsme
```

**Итог:** `maketrans()` + `translate()` = мощный инструмент для пакетной замены символов!