## Чтение stdin через open(0)

Нестандартный приём: `open(0)` читает стандартный ввод без импорта `sys`.

```python
from datetime import date

# open(0) — 0 это дескриптор файла stdin
dates = [date(*map(int, d.split('-'))) for d in open(0)]
print((max(dates) - min(dates)).days)
```

**Почему работает:** в Unix-системах stdin зарезервирован под дескриптор файла `0`. `open()` может принимать не только путь, но и дескриптор.

**Преимущества:**
- Не нужен `import sys`
- Лаконичный код для конкурсных задач

**Использование:**
```bash
python script.py < dates.txt
```

> 🔗 Документация: https://docs-python.ru/tutorial/vstroennye-funktsii-interpretatora-python/funktsija-open/
> 🔗 См. также: [[Работа с файлами]]