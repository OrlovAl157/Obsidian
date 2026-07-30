---
tags: [git, инструменты, remote, stash, отмена]
difficulty: beginner
---

# ☁️ Git — Remote, отмена и stash — памятка

> Remote — удалённый репозиторий (GitHub, GitLab). Stash — временное хранилище незакоммиченных изменений. Отмена — способы откатить изменения на разных стадиях.

## Содержание

- [[#Справка|Справка]]
- [[#📊 Общая схема|Общая схема]]
- [[#☁️ Remote — работа с GitHub|Remote]]
- [[#↩️ Отмена изменений|Отмена изменений]]
- [[#📦 Stash — временное хранилище|Stash]]
- [[#🏷 Теги|Теги]]
- [[#💡 Практические замечания|Практические замечания]]
- [[#⚠️ Частые ошибки|Частые ошибки]]
- [[#✅ Главные правила|Главные правила]]

---

## Справка

| Команда | Что делает |
|---|---|
| `git remote -v` | Показать remote репозитории |
| `git remote add origin <url>` | Привязать remote |
| `git push origin <ветка>` | Отправить ветку на remote |
| `git pull` | Скачать и слить изменения |
| `git fetch` | Скачать без слияния |
| `git restore <файл>` | Отменить изменения в файле |
| `git restore --staged <файл>` | Убрать из staging |
| `git revert <hash>` | Отменить коммит (безопасно) |
| `git reset --soft HEAD~1` | Отменить коммит, сохранив изменения |
| `git reset --hard HEAD~1` | ⚠️ Удалить коммит и изменения |
| `git stash` | Спрятать изменения |
| `git stash pop` | Вернуть спрятанные изменения |

---

## 📊 Общая схема

```
Локальный репозиторий          Remote (GitHub/GitLab)
──────────────────────         ──────────────────────
  Working Directory
        │
      git add
        │
    Staging area
        │
    git commit
        │
   Local repo  ──── git push ────►  Remote repo
                ◄─── git fetch ───   (только скачивает)
                ◄─── git pull ────   (fetch + merge)

pull = fetch + merge
```

---

## ☁️ Remote — работа с GitHub

```bash
# Просмотр
git remote -v                          # показать все remote с URL
git remote show origin                 # детали remote

# Привязка
git remote add origin https://github.com/user/repo.git

# Push — отправить на remote
git push origin main                   # отправить ветку main
git push -u origin main                # push + запомнить ветку (потом просто git push)
git push                               # если ветка уже привязана через -u
git push origin --delete feature       # удалить remote ветку

# Pull / Fetch
git pull                               # скачать и слить (fetch + merge)
git pull origin main                   # явно указать откуда
git fetch                              # скачать БЕЗ слияния
git fetch origin                       # то же самое

# После fetch — посмотреть что изменилось и слить вручную
git log HEAD..origin/main --oneline    # что появилось на remote
git merge origin/main                  # слить вручную
```

**`pull` vs `fetch`:**

```
git fetch   → только скачивает изменения, не трогает твои файлы
git pull    → скачивает И сразу сливает с текущей веткой

Безопаснее: git fetch → посмотреть → git merge
Быстрее:    git pull
```

---

## ↩️ Отмена изменений

Три стадии — три способа отменить:

```
Изменения НЕ добавлены в staging:
git restore <файл>              # отменить изменения в файле ✅
git restore .                   # отменить всё ✅

Изменения добавлены в staging (git add):
git restore --staged <файл>     # убрать из staging (изменения сохранятся) ✅
git restore --staged .          # убрать всё из staging

Изменения уже закоммичены:
git revert <hash>               # создать новый коммит который отменяет ✅ безопасно
git reset --soft HEAD~1         # отменить коммит, изменения остаются в staging ✅
git reset --mixed HEAD~1        # отменить коммит, изменения остаются в файлах ✅
git reset --hard HEAD~1         # ⚠️ удалить коммит И изменения безвозвратно
```

**Разница reset:**

| Флаг | Коммит | Staging | Файлы |
|---|---|---|---|
| `--soft` | Отменён | Сохранён | Сохранены |
| `--mixed` (по умолчанию) | Отменён | Очищен | Сохранены |
| `--hard` | Отменён | Очищен | ⚠️ Удалены |

**revert vs reset:**

```bash
# revert — безопасно, создаёт новый коммит-отмену
git revert a3f2c1d              # ✅ можно на shared ветках, история сохраняется

# reset — опасно, переписывает историю
git reset --hard a3f2c1d        # ⚠️ только на локальных ветках которые не пушил
```

---

## 📦 Stash — временное хранилище

> Аналогия: поставить вещи на полку — убрал с рабочего стола, но не выбросил.

```bash
# Сохранить
git stash                       # спрятать все незакоммиченные изменения
git stash push -m "описание"    # с описанием — удобно если несколько stash

# Вернуть
git stash pop                   # вернуть последний stash и удалить его из списка
git stash apply                 # вернуть последний stash, но оставить в списке
git stash apply stash@{2}       # вернуть конкретный stash

# Просмотр
git stash list                  # список всех stash
git stash show                  # что в последнем stash
git stash show -p               # полный diff последнего stash

# Удаление
git stash drop                  # удалить последний stash
git stash drop stash@{1}        # удалить конкретный
git stash clear                 # удалить все stash ⚠️
```

**Типичный сценарий:**

```bash
# Работаешь над feature, нужно срочно переключиться на main
git stash                       # прячем незакоммиченные изменения
git switch main
# ... делаешь срочное дело ...
git switch feature/login
git stash pop                   # возвращаем изменения
```

---

## 🏷 Теги

Теги — метки на конкретных коммитах (обычно для версий релизов):

```bash
git tag                         # показать все теги
git tag v1.0                    # лёгкий тег на текущий коммит
git tag -a v1.0 -m "Версия 1.0" # аннотированный тег (с сообщением)
git tag -a v1.0 a3f2c1d         # тег на конкретный коммит

git push origin v1.0            # отправить один тег на remote
git push origin --tags          # отправить все теги

git tag -d v1.0                 # удалить локальный тег
```

---

## 💡 Практические замечания

- `git fetch` перед началом работы — узнать что изменилось на remote
- `git stash` удобен когда нужно срочно переключиться не теряя работу
- `git revert` — всегда предпочтительнее `git reset` на ветках которые уже запушил
- `git reset --hard` — данные удаляются навсегда (почти), будь осторожен
- `git push -u origin main` — после этого достаточно просто `git push`

---

## ⚠️ Частые ошибки

**❌ `git reset --hard` на запушенной ветке:**
```bash
git reset --hard HEAD~1         # ❌ создаст конфликт при следующем push
git revert HEAD                 # ✅ безопасная альтернатива
```

**❌ `git pull` когда есть незакоммиченные изменения:**
```bash
git pull                        # ❌ может создать конфликт
git stash                       # ✅ сначала спрятать
git pull
git stash pop
```

**❌ Забыл `-u` при первом push:**
```bash
git push origin main            # работает, но каждый раз нужно указывать ветку
git push -u origin main         # ✅ запомнит ветку, потом просто git push
```

**❌ `git stash clear` удалил нужное:**
```bash
git stash clear                 # ⚠️ удаляет ВСЕ stash без возможности восстановления
# Лучше удалять по одному:
git stash drop stash@{0}        # ✅
```

---

## ✅ Главные правила

✅ `git push -u origin main` при первом push — потом просто `git push`  
✅ `pull` = `fetch` + `merge` — если хочешь контроль, используй `fetch` отдельно  
✅ `git revert` — безопасная отмена коммита на shared ветках  
✅ `git reset --hard` — только на локальных незапушенных ветках  
✅ `git stash` — когда нужно срочно переключиться не теряя работу  
✅ `git stash pop` — вернуть и удалить из списка  
✅ Теги — для версий релизов, отправляй через `git push origin --tags`  

---

## 🔗 Связанные темы

- [[01 — 📋 Основы, коммиты, история]]
- [[02 — 🌿 Ветки и слияние]]

---

#git #инструменты #remote #stash #отмена
