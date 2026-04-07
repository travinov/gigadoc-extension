# Qwen Sber Doc Extension

Qwen-расширение для стандартизированной документации Python-модулей и директорий проекта в стиле Сбера.

## Публичные ссылки

1. Репозиторий extension: [https://github.com/travinov/qwen-sber-doc-extension](https://github.com/travinov/qwen-sber-doc-extension)
2. Репозиторий MCP server: [https://github.com/travinov/qwen-sber-doc-mcp](https://github.com/travinov/qwen-sber-doc-mcp)
3. Форк с результатом по челленджу: [https://github.com/travinov/claw-code](https://github.com/travinov/claw-code)
4. Релиз extension `v0.2.0`: [https://github.com/travinov/qwen-sber-doc-extension/releases/tag/v0.2.0](https://github.com/travinov/qwen-sber-doc-extension/releases/tag/v0.2.0)
5. npm-пакет MCP: [https://www.npmjs.com/package/qwen-sber-doc-mcp](https://www.npmjs.com/package/qwen-sber-doc-mcp)

## Зачем устанавливать это решение

Расширение закрывает практическую проблему командной документации:

1. Единый официальный стиль на русском языке без ручного «выравнивания» текста.
2. Повторяемая структура для всех модулей: назначение, сущности, параметры, примеры.
3. Поддержка обзора всего проекта при передаче пути к директории.
4. Связка с MCP-проверками, чтобы текст был не только красивым, но и полным.

Это полезно, когда вы:

1. Ведете внутреннюю техдокументацию SDK/CLI.
2. Готовите open-source README/API docs и хотите стабильный формат.
3. Онбордите новых инженеров и хотите одинаковый уровень качества описаний.

## Что внутри

1. Skill: `skills/sber-doc-style/SKILL.md`
Назначение: закрепляет тон, терминологию и структуру документа.
2. Slash command: `commands/doc/sber.md`
Назначение: дает единый вход `/doc:sber <path>`.
3. Extension context: `QWEN.md`
Назначение: описывает правила и порядок использования инструментов.
4. Manifest: `qwen-extension.json`
Назначение: подключает skill, command и MCP server.
Дополнительно:
- `gigacode-extension.json` для форков, где CLI ожидает такой файл конфигурации.
- `gemini-extension.json` для Gemini-совместимых форков.
Контекстные файлы:
- `QWEN.md` для Qwen-ветки;
- `GEMINI.md` для Gigacode/Gemini-ветки.
5. Launcher: `scripts/run-mcp.js`
Назначение: находит и запускает companion MCP server.

## Как это работает в CLI

Пользователь запускает:

```text
/doc:sber /absolute/path/to/module.py
# или
/doc:sber /absolute/path/to/project_dir
```

Далее агент:

1. вызывает `analyze_python_target`;
2. если это файл, строит каркас через `build_sber_doc_outline`;
3. если это директория, строит каркас через `build_sber_project_outline`;
4. генерирует финальный документ в RU-формате;
5. проверяет результат через `validate_sber_doc`.

## Установка (подробно)

Требования:

1. `gigacode` CLI установлен и доступен в `PATH`.
   Если используется оригинальный клиент, можно применять `qwen`.
2. Node.js 22+.
3. Доступ к MCP server одним из способов:
   - локальная сборка [`qwen-sber-doc-mcp`](https://github.com/travinov/qwen-sber-doc-mcp),
   - npm-пакет `qwen-sber-doc-mcp` (через fallback `npx`).

Рекомендуемая структура каталогов:

```text
workspace/
  qwen-sber-doc-extension/
  qwen-sber-doc-mcp/
```

Шаги:

```bash
git clone https://github.com/travinov/qwen-sber-doc-extension.git qwen-sber-doc-extension
git clone https://github.com/travinov/qwen-sber-doc-mcp.git qwen-sber-doc-mcp

cd qwen-sber-doc-mcp
npm install
npm run build

cd ../qwen-sber-doc-extension
gigacode extensions link .
```

Быстрая установка extension без клонирования:

```bash
gigacode extensions install travinov/qwen-sber-doc-extension
```

Практический one-command путь для конечного пользователя:
установить extension, а MCP будет подтянут автоматически через `npx qwen-sber-doc-mcp`.

Совместимый fallback для оригинального клиента:

```bash
qwen extensions install travinov/qwen-sber-doc-extension
```

Launcher `scripts/run-mcp.js` запускает MCP в таком порядке:

1. `QWEN_SBER_DOC_MCP_ENTRY`
2. `../qwen-sber-doc-mcp/dist/src/index.js`
3. `node_modules/qwen-sber-doc-mcp/dist/src/index.js`
4. `npx --yes qwen-sber-doc-mcp`

Если extension и MCP лежат не рядом, укажите явный путь:

```bash
export QWEN_SBER_DOC_MCP_ENTRY="/absolute/path/to/qwen-sber-doc-mcp/dist/src/index.js"
```

После этого перезапустите `gigacode` (или `qwen`, если используете оригинальный клиент).

## Примеры использования

1. Документация конкретного файла:

```text
/doc:sber /repo/src/query_engine.py
# или, если форк регистрирует только плоские команды:
/sber /repo/src/query_engine.py
```

2. Обзор проекта по директории:

```text
/doc:sber /repo/src
```

3. Пакетная работа по модулям (ручной запуск по очереди):

```text
/doc:sber /repo/src/session_store.py
/doc:sber /repo/src/runtime.py
/doc:sber /repo/src/tool_pool.py
```

4. Обновление документации после изменения API:

```text
/doc:sber /repo/src/new_module.py
```

## Типовые задачи конечного пользователя

1. Подготовить docs для релиза библиотечного модуля.
2. Привести в единый формат документацию разных команд/репозиториев.
3. Контролировать минимальный уровень качества (параметры + примеры + структура).
4. Сократить время ревью документации на pull request.

## Ограничения v1

1. Фокус на Python-модулях.
2. Без публикации в marketplace.
3. Качество итогового текста зависит от качества исходного кода и docstring.
