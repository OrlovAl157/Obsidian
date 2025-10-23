😊 Прочитай в документации про функцию open(): https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/funktsija-open/ Она может принимать в качестве аргумента не только абсолютный/относительный путь к файлу, но и дескриптор файла: https://timeweb.com/ru/community/articles/chto-takoe-faylovyy-deskriptor-prostymi-slovami Под stdin (стандартный вход) в системе зарезервирован дескриптор 0. Таким образом, я читаю входной поток с помощью функции open() напрямую, используя дескриптор. При этом импорт stdin из sys не нужен.


```python
from datetime import date

# 📖 Используем open(0) для чтения из stdin (дескриптор файла 0)
# 🔗 https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/funktsija-open/
# 🔗 https://timeweb.com/ru/community/articles/chto-takoe-faylovyy-deskriptor-prostymi-slovami
dates = [date(*map(int, d.split('-'))) for d in open(0)]

# 📅 Вычисляем разницу между самой поздней и самой ранней датой
print((max(dates) - min(dates)).days)
```

**✨ Преимущества подхода:**
- 🚀 Прямой доступ к stdin через дескриптор файла
- 📦 Не требует импорта `sys`
- 💡 Чистый и лаконичный код
- 🎯 Автоматическое определение минимальной и максимальной дат

**🖥️ Пример использования:**
```bash
# Ввод данных с клавиатуры
python script.py
2023-01-01
2023-12-31
2023-06-15
Ctrl+D

# Или через перенаправление файла
python script.py < dates.txt
```

**📊 Результат:**
```
364
```
