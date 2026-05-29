# SONAR MAIN 1.3

**Версия**: 1.3  
**Дата**: 29 мая 2026  
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
  main  — единственная рабочая ветка, ВСЕ изменения только сюда
          (trunk-based development, dev-ветка удалена 29.05.2026)

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
- Flask через Blueprint'ы
- Шаблоны Jinja2 в `templates/`
- Нет async, нет Celery — всё синхронно
- Все действия через `activity_log.log_action()`

### БД и миграции
- Изменения схемы БД только через миграции в `db.py`
- `database.db` не коммитится, хранится локально
- Классификаторы в таблице `classifiers` (category / value / sort_order)

### Коммиты
- Все коммиты идут **только в ветку `main`**
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
5. ИИ коммитит в main
6. ИИ закрывает Issue с комментарием что сделано
7. ИИ выдаёт SHA коммита (гиперссылка)
```

---

## ОТКРЫТЫЕ ISSUES (29 мая 2026)

### Баги
| # | Файл | Проблема |
|---|---|---|
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
- Замечай если issue закрыт или устарел — предлагай закрыть

### ✖ НЕ ДЕЛАЙ:
- Не меняй архитектуру без прямого запроса
- Не коммитить в другие ветки — **только `main`**
- Не создавать ветки `dev`, `feature/`, `hotfix/` без явного запроса
- Не коммитить `.env`, `database.db`, `uploads/`, `reports/`, `WPy/`
- Не добавлять async без обсуждения
- Не предлагать внешние ORM (проект использует чистый SQL)

---

## ЧЕКЛИСТ ПОСЛЕ ЗАДАЧИ

```
☐ SHA коммита отправлен гиперссылкой
☐ Issue закрыт с комментарием
☐ Спросить: что делаем дальше?
```

---

*SONAR MAIN 1.3 — специализированный промт для разработки WHO-AM-I-52/SONAR*  
*Обновлён: 29 мая 2026 — переход на trunk-based, ветка dev удалена*
