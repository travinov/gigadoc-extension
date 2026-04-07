# Gigadoc Extension (Gigacode/Gemini + Qwen)

Расширение для документирования Python-файлов и директорий проекта в официальном русскоязычном стиле.

## Публичные ссылки

1. Extension repo: [https://github.com/travinov/gigadoc-extension](https://github.com/travinov/gigadoc-extension)
2. MCP repo: [https://github.com/travinov/gigadoc-mcp](https://github.com/travinov/gigadoc-mcp)
3. MCP npm: [https://www.npmjs.com/package/gigadoc-mcp](https://www.npmjs.com/package/gigadoc-mcp)

## Актуальные версии

1. `gigadoc-extension`: `0.4.2`
2. `gigadoc-mcp`: `0.4.3`

## Что делает extension

1. Добавляет команды `/doc:sber` и `/sber`.
2. Подключает стиль `sber-doc-style` для официальной русскоязычной документации.
3. Использует MCP tools сервера `gigadoc-mcp`:
   - `analyze_python_target`
   - `build_sber_doc_outline`
   - `build_sber_project_outline`
   - `validate_sber_doc`

## Форматы манифеста

1. `gigacode-extension.json` — основной для Gigacode fork.
2. `gemini-extension.json` — Gemini-совместимый формат.
3. `qwen-extension.json` — формат Qwen.

## Установка extension

Через UI:

```text
/extensions install https://github.com/travinov/gigadoc-extension.git
```

Локальная разработка:

```bash
git clone https://github.com/travinov/gigadoc-extension.git
gigacode extensions link /absolute/path/to/gigadoc-extension
```

## Подключение MCP в Gigacode: ветвление по окружению

### Вариант A: стандартная сеть (рекомендуется)

```json
{
  "mcpServers": {
    "gigadoc-mcp": {
      "command": "npx",
      "args": ["--yes", "gigadoc-mcp@0.4.3"],
      "timeout": 60000
    }
  }
}
```

### Вариант B: корпоративная сеть с SSL inspection

Если появляется ошибка вроде `SELF_SIGNED_CERT_IN_CHAIN`, используйте:

```json
{
  "mcpServers": {
    "gigadoc-mcp": {
      "command": "npx",
      "args": ["--yes", "gigadoc-mcp@0.4.3"],
      "env": {
        "NPM_CONFIG_STRICT_SSL": "false"
      },
      "timeout": 60000
    }
  }
}
```

Примечание: `NPM_CONFIG_STRICT_SSL=false` — временный workaround. Для production рекомендуется настроить корпоративный CA (`npm config set cafile ...`).

### Вариант C: альтернативное окружение (CLI не видит PATH)

Используйте абсолютные пути к Node/npm:

```json
{
  "mcpServers": {
    "gigadoc-mcp": {
      "command": "/absolute/path/to/node",
      "args": [
        "/absolute/path/to/npm/bin/npx-cli.js",
        "--yes",
        "gigadoc-mcp@0.4.3"
      ],
      "env": {
        "PATH": "/absolute/path/to/node/bin:/usr/bin:/bin:/usr/sbin:/sbin"
      },
      "timeout": 60000
    }
  }
}
```

### Вариант D: локальный запуск MCP без `npx`

```json
{
  "mcpServers": {
    "gigadoc-mcp": {
      "command": "node",
      "args": ["/absolute/path/to/gigadoc-mcp/dist/src/index.js"],
      "timeout": 60000
    }
  }
}
```

## Как проверить подключение

1. Перезапустить CLI после изменения `settings.json`.
2. Открыть `/mcp` и убедиться, что `gigadoc-mcp` в статусе `connected`.
3. Выполнить:

```text
/doc:sber /repo/src/module.py
```

Если MCP недоступен, команда вернет подсказку о подключении сервера.

## Управление контекстом

Для больших директорий запускайте документацию в два шага:

1. Обзор проекта (context-safe): используйте `analyze_python_target` с `max_modules=40` и `include_modules=false`.
2. Детализация: документируйте приоритетные модули отдельными вызовами по конкретным файлам.

Команды `/doc:sber` и `/sber` уже ориентированы на этот режим для директорий.

## Настройка под себя

### Уровень 1: без форка

Добавляйте стиль в запрос перед командой:

```text
Пиши проще, короткими абзацами, для junior-аудитории. Добавляй мини-итоги после разделов.
/doc:sber /path/to/module.py
```

### Уровень 2: правила на уровне проекта

Создайте или дополните `QWEN.md`/`GEMINI.md` в проекте:

1. фиксируйте тон (строже, проще, короче);
2. фиксируйте обязательные разделы;
3. фиксируйте ограничения по длине и формату таблиц.

### Уровень 3: полный контроль через форк

1. Форкните `gigadoc-extension`.
2. Измените:
   - `skills/sber-doc-style/SKILL.md`
   - `commands/doc/sber.md`
   - `commands/sber.md`
3. Подключите свой форк:

```text
/extensions install https://github.com/<your-user>/<your-repo>.git
```

## Использование

Один файл:

```text
/doc:sber /repo/src/module.py
# или
/sber /repo/src/module.py
```

Вся директория:

```text
/doc:sber /repo/src
```

Результат: структурированный документ (модульный или проектный) с проверкой качества через `validate_sber_doc`.
