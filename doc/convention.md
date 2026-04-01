# Конвенция работы с проектом

## Структура проекта

```
.
├── book.toml              # Конфигурация mdBook
├── src/                   # Markdown-файлы
│   └── SUMMARY.md        # Оглавление (структура навигации)
├── .github/workflows/    # CI/CD
│   └── mdbook.yml        # Автосборка на GitHub Pages
└── doc/                  # Документация проекта
```

## Git workflow

### Ветки
- `master` — основная ветка, автоматический деплой на GitHub Pages
- `feature/*` — ветки для новых разделов/изменений
- `fix/*` — исправления ошибок

### Коммиты
- Сообщения на английском языке
- Формат: `[type] short description`
- Типы: `feat`, `fix`, `docs`, `style`, `refactor`
- Пример: `[docs] Add engine specifications section`

### Pull Requests
1. Создать PR из feature-ветки в `master`
2. Описать изменения
3. Проверить сборку: `mdbook build`
4. После мержа — автоматический деплой

## Основные команды

### Установка и запуск
```bash
# Установка mdBook
cargo install mdbook

# Локальный сервер (http://localhost:3000)
mdbook serve

# Сборка
mdbook build

# Проверка ссылок
mdbook test
```

### Работа с контентом
```bash
# Добавить ссылку на новый раздел в SUMMARY.md
edit src/SUMMARY.md

# Запустить сборку для создания файлов
mdbook build

# Файлы создадутся автоматически по путям из SUMMARY.md

# Поиск незаполненных файлов
grep -r "^# $" src/
```

## CI/CD
- Push в `master` → автоматическая сборка → деплой на GitHub Pages
- Workflow: `.github/workflows/mdbook.yml`
- Ручной запуск: Actions → "Deploy mdBook site to Pages" → Run workflow

## Быстрый старт
1. `git clone <repo>`
2. `cd <project>`
3. `mdbook serve` (просмотр на http://localhost:3000)
4. Добавить ссылку на новый раздел в `src/SUMMARY.md`
5. `mdbook build` (создаст файлы по путям из SUMMARY.md)
6. Редактировать созданные файлы в `src/`
7. `git add . && git commit -m "[docs] Add new section"`
8. `git push origin feature/new-section`
9. Создать PR