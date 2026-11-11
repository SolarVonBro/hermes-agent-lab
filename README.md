# hermes-agent-lab

Лабораторная работа №2: развёртывание автономного агента (Hermes Agent) для исследовательских задач в изолированном контейнере Docker Desktop.

**Предупреждение о безопасности.** Агент выполняет команды в контейнере. Доступ к боту равнозначен доступу к терминалу контейнера. Используйте отдельный контур без личных данных и чужих ключей, список разрешённых пользователей из одного идентификатора, лимит расходов на ключе LLM. Токены и ключи не публикуйте; при утечке немедленно отзовите и перевыпустите.

## Состав

| Файл | Назначение |
|---|---|
| `compose.yaml` | запуск агента: образ, перезапуск, монтирование данных, лимиты, ротация логов |
| `.env.example` | какие переменные нужны (реальный `data/.env` вне репозитория) |
| `skills/site-healthcheck/` | собственный навык: проверка доступности сайта |
| `docs/` | отчёт, модель угроз T3 и доказательства (журналы) |

## Запуск

```bash
mkdir -p data && cp .env.example data/.env      # заполнить значения
docker run --rm -v "$PWD/data:/opt/data" nousresearch/hermes-agent:stable config set model.provider openrouter
docker run --rm -v "$PWD/data:/opt/data" nousresearch/hermes-agent:stable config set model.default "nvidia/nemotron-3.5-lightning:free"
docker compose up -d
docker logs hermes
```

Навык копируется в `data/skills/devops/site-healthcheck/`.

Лицензия: код и конфигурация — MIT; тексты — CC BY 4.0.
