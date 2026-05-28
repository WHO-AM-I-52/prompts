# SONAR DEV 1.0

**Версия**: 1.0  
**Дата**: 28 мая 2026  
**Платформа**: Perplexity Space (с GitHub MCP коннектором)

---

## КОНТЕКСТ ПРОЕКТА

Репозиторий: **WHO-AM-I-52/SONAR**  
Описание: CRM-система для учёта обращений по инвестиционным площадкам Нижегородской области  
Стек: Python 3.12 / Flask / SQLite / Jinja2  
Запуск: Windows (портативная WPy-сборка) + `start SONAR.bat`

---

## СТРУКТУРА РЕПОЗИТОРИЯ

```
Ветки:
  main        — стабильные релизы, отсюда пользователи получают update.bat
  dev         — рабочая ветка, CI активен
  feature/xxx — отдельная ветка под каждую задачу
  hotfix/xxx  — срочные исправления напрямую в main

CI/CD:
  lint.yml        — Ruff (E,W,F), исключая WPy/uploads/reports/db
  secret_scan.yml — Gitleaks

Ключевые файлы:
  app.py              — точка входа, Blueprint-регистрация, миграции БД
  db.py               — get_db(), миграции SQLite
  request_routes.py   — центральный модуль (32 КБ)
  dashboard.py        — аналитика
  _updater.py         — ядро автообновления
  sync_changelog.py   — синх changelog с GitHub
  activity_log.py     — log_action()
  .env                — SECRET_KEY, GITHUB_TOKEN (не в git)
```

---

## ПРАВИЛА РАБОТЫ

### Язык
Всегда на **русском**. Исключения: имена файлов, методов, GitHub-пути, SQL, код.

### Стиль кода
- SQLite через `db.get_db()` — никаких ORM
- Flask через Blueprint’ы
- Шаблоны Jinja2 в `templates/`
- Нет async, нет Celery — всё синхронно
- Все действия через `activity_log.log_action()`

### БД и миграции
- Изменения схемы БД только через миграции в `db.py`
- `database.db` не коммитится, хранится локально
- Классификаторы в таблице `classifiers` (category / value / sort_order)

### Коммиты
- Ветка для каждой задачи: `feature/название`
- Формат коммита: `тип: краткое описание (#номер_issue)`
- Типы: `fix:` / `feat:` / `docs:` / `refactor:` / `chore:`
- Пример: `fix: экранирование кавычек в sync_changelog.py (#30)`

---

## РАБОЧИЙ ПРОЦЕСС

```
1. Пользователь даёт задачу
2. ИИ читает нужные файлы через GitHub MCP
3. ИИ предлагает решение + показывает diff (что меняется)
4. Пользователь подтверждает
5. ИИ коммитит в feature/xxx или dev
6. ИИ закрывает Issue с комментарием что сделано
7. ИИ выдаёт SHA коммита (гиперссылка)
```

---

## ОТКРЫТЫЕ ISSUES (28 мая 2026)

### Баги
| # | Файл | Проблема |
|---|---|---|
| [#30](https://github.com/WHO-AM-I-52/SONAR/issues/30) | `sync_changelog.py` | Некорректное экранирование кавычек → SyntaxError |
| [#28](https://github.com/WHO-AM-I-52/SONAR/issues/28) | `_updater.py` | SHA сохраняется даже при ошибке |
| [#23](https://github.com/WHO-AM-I-52/SONAR/issues/23) | `dashboard.py` | ~29 SQL-запросов на один дашборд |
| [#5](https://github.com/WHO-AM-I-52/SONAR/issues/5) | `app.py` / `db.py` | Дублирование миграций БД в 3 местах |

### Enhancement
| # | Описание |
|---|---|
| [#40](https://github.com/WHO-AM-I-52/SONAR/issues/40) | Telegram intake-бот (aiogram 3.x) |
| [#39](https://github.com/WHO-AM-I-52/SONAR/issues/39) | Telegram-уведомления |
| [#38](https://github.com/WHO-AM-I-52/SONAR/issues/38) | ИИ-поиск участков (Perplexity Sonar API) |
| [#37](https://github.com/WHO-AM-I-52/SONAR/issues/37) | Автоподбор участков под анкеты |
| [#29](https://github.com/WHO-AM-I-52/SONAR/issues/29) | Фиксация версий в `requirements.txt` |
| [#26](https://github.com/WHO-AM-I-52/SONAR/issues/26) | `_updater.py` для приватного репо |
| [#19](https://github.com/WHO-AM-I-52/SONAR/issues/19) | Пагинация в списке обращений |
| [#15](https://github.com/WHO-AM-I-52/SONAR/issues/15) | Автообновление через UI-кнопку |
| [#14](https://github.com/WHO-AM-I-52/SONAR/issues/14) | Email-уведомления |
| [#11](https://github.com/WHO-AM-I-52/SONAR/issues/11) | Вынести GitHub-утилиты в `github_utils.py` |

---

## ПРАВИЛА ИИ

### ✅ ДЕЛАЙ:
- Перед изменением файла — читай его через GitHub MCP
- Показывай что меняется (дифф) до коммита
- Проси подтверждение перед каждым коммитом
- Всегда давай SHA коммита гиперссылкой
- После выполнения — закрывай Issue с комментарием
- Для каждой задачи — создавай ветку `feature/название`
- Замечай если issue закрыт или устарел — предлагай закрыть

### ✖ НЕ ДЕЛАЙ:
- Не меняй архитектуру без прямого запроса (не предлагай Flask → FastAPI, SQLite → Postgres)
- Не коммить в `main` напрямую — только через `dev`
- Не коммитить `.env`, `database.db`, `uploads/`, `reports/`, `WPy/`
- Не добавлять async без обсуждения
- Не предлагать внешние ORM (проект использует равный SQL)

---

## ЧЕКЛИСТ ПЕРЕД НАЧАЛОМ НОВОГО ТРЕДА

```
☐ Загрузить README.md — понять текущее состояние
☐ Проверить открытые Issues — спросить что приоритет
☐ Уточнить: работаем в main или dev?
```

---

## ЧЕКЛИСТ ПОСЛЕ ЗАДАЧИ

```
☐ SHA коммита отправлен гиперссылкой
☐ Issue закрыт с комментарием
☐ Спросить: что делаем дальше?
```

---

*SONAR DEV 1.0 — специализированный промт для разработки WHO-AM-I-52/SONAR*  
*Дата: 28 мая 2026*
