# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Task Dashboard — одностраничное HTML-приложение (single-file `index.html`) для отслеживания задач. Интерфейс на русском языке.

## Architecture

Весь проект — один файл `index.html`, содержащий встроенные CSS и JavaScript. Нет сборки, зависимостей или npm.

### Data Flow

1. Данные загружаются из Google Sheets через JSONP (`docs.google.com/spreadsheets/.../gviz/tq`)
2. Глобальный callback `handleSheetData()` получает ответ от Google Visualization API
3. `gvizToRows()` преобразует ответ gviz в массив строк
4. `processData()` парсит строки, извлекает task ID (формат `TD-XXX`), группирует подзадачи с одинаковым ID в карточки
5. Результат кэшируется в `localStorage` (ключ `task-dashboard-cache`) как fallback

### Task Status Pipeline

Функция `classifyStatus()` маппит сырые текстовые статусы из таблицы в группы:
- **wip** — в работе (дефолт)
- **review** — на ревью, PR создан, ревью пройдено
- **merged** — завершено (содержит "merge" или "push")
- **cancelled** — отменена

### Key Data Columns (Google Sheet row mapping)

`[0]` описание (с префиксом TD-XXX), `[1]` статус, `[2]` дата начала, `[3]` дата окончания, `[4]` Jira URL, `[5]` PR internal URL, `[6]` PR external URL, `[7]` репозиторий.

### Grouping Logic

Строки с одинаковым `TD-XXX` в описании группируются в одну карточку с подзадачами (`subtasks`). Статус группы определяется по наивысшему статусу подзадач.

## Development

Открыть `index.html` в браузере. Никакого dev-сервера или сборки не требуется. Для работы нужен доступ к интернету (загрузка данных из Google Sheets).

## UI Features

- Тёмная/светлая тема — `toggleTheme()`, сохраняется в `localStorage`
- Фильтры по статусу (chip-кнопки), множественный выбор
- Адаптивная верстка (breakpoints: 768px, 400px)
- Даты в формате `DD.MM.YYYY`, отображаются как `D мес YYYY`
