# Конвенция работы с проектом

## Структура проекта

```
.
├── book.toml              # Конфигурация mdBook
├── src/                   # Markdown-файлы
│   ├── SUMMARY.md         # Оглавление (структура навигации)
│   └── images/            # Изображения для страниц
├── theme/                 # Стили и шаблон HTML
│   ├── custom.css
│   └── head.hbs
├── .github/workflows/     # CI/CD
│   └── mdbook.yml         # Автосборка на GitHub Pages
└── doc/                   # Документация проекта
    └── convention.md      # Этот файл
```

## Язык и стиль

- Текст книги — на русском языке, коммиты и названия веток — на английском.
- Заголовки страниц совпадают с формулировками в `src/SUMMARY.md`.
- Факты, которые зависят от комплектации или рынка, помечаются явно («в зависимости от комплектации», «для дизельных версий»).
- Одна и та же характеристика должна совпадать во всех разделах: при изменении цифр проверяйте `overview/specs.md`, `maintenance/` и `engine/`.

## Git workflow

### Ветки

- `master` — основная ветка, автоматический деплой на GitHub Pages
- `feature/*` — ветки для новых разделов/изменений
- `fix/*` — исправления ошибок

### Коммиты

- Сообщения на английском языке
- Формат: `type: short description`
- Типы: `feat`, `fix`, `doc`, `style`, `refactor`
- Пример: `doc: Add engine specifications section`

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

# Локальный сервер с перезагрузкой (http://localhost:3000)
mdbook serve --open

# Сборка статического сайта в папку book/
mdbook build

# Проверка примеров кода из документации (НЕ проверка ссылок)
mdbook test
```

`mdbook test` прогоняет только Rust-примеры из блоков кода и не ищет битые ссылки. Ссылки проверяются отдельно:

```bash
# Проверка локальных ссылок и картинок без сторонних инструментов
python3 - <<'EOF'
import os, re, glob
root = "src"
for f in glob.glob(os.path.join(root, "**", "*.md"), recursive=True):
    for m in re.finditer(r"!?\[[^\]]*\]\(([^)\s]+)\)", open(f, encoding="utf-8").read()):
        t = m.group(1)
        if t.startswith(("http", "#", "mailto:")):
            continue
        p = os.path.normpath(os.path.join(os.path.dirname(f), t.split("#")[0]))
        if t.split("#")[0] and not os.path.exists(p):
            print(f, "->", t)
EOF
```

### Работа с контентом

```bash
# Добавить ссылку на новый раздел в SUMMARY.md
$EDITOR src/SUMMARY.md

# Собрать книгу (файлы, указанные в SUMMARY.md, будут созданы автоматически)
mdbook build

# Найти пустые страницы (заголовок без содержимого)
for f in $(find src -name '*.md'); do
  [ "$(grep -c . "$f")" -le 1 ] && echo "$f"
done
```

## CI/CD

- Push в `master` → автоматическая сборка → деплой на GitHub Pages
- Workflow: `.github/workflows/mdbook.yml`
- Ручной запуск: Actions → "Deploy mdBook site to Pages" → Run workflow

## Быстрый старт

1. `git clone <repo>`
2. `cd <project>`
3. `mdbook serve --open` (просмотр на <http://localhost:3000>)
4. Добавить ссылку на новый раздел в `src/SUMMARY.md`
5. `mdbook build` (создаст файлы по путям из SUMMARY.md)
6. Редактировать созданные файлы в `src/`
7. Проверить сборку и локальные ссылки (см. команды выше)
8. `git add . && git commit -m "doc: Add new section"`
9. `git push origin feature/new-section`
10. Создать PR

