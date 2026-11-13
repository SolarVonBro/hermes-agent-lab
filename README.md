# hermes-agent-lab

Задание 2: развёртывание автономного агента (Hermes Agent) для исследовательских задач в изолированном контейнере Docker Desktop.

**Предупреждение о безопасности.** Агент выполняет команды в контейнере. Доступ к боту равнозначен доступу к терминалу контейнера. Используйте отдельный контур без личных данных и чужих ключей, список разрешённых пользователей из одного идентификатора, лимит расходов на ключе LLM. Токены и ключи не публикуйте; при утечке немедленно отзовите и перевыпустите.

## Состав

| Файл | Назначение |
|---|---|
| `compose.yaml` | запуск агента: образ, перезапуск, монтирование данных, лимиты, ротация логов |
| `.env.example` | какие переменные нужны (реальный `data/.env` вне репозитория) |
| `skills/site-healthcheck/` | собственный навык: проверка доступности сайта |
| `docs/` | отчёт ([`report.md`](docs/report.md), версия для печати [`otchet-zadanie-2.pdf`](docs/otchet-zadanie-2.pdf)), модель угроз T3 и доказательства (журналы) |

## Запуск

```bash
mkdir -p data && cp .env.example data/.env      # заполнить значения
docker run --rm -v "$PWD/data:/opt/data" nousresearch/hermes-agent:stable config set model.provider openrouter
docker run --rm -v "$PWD/data:/opt/data" nousresearch/hermes-agent:stable config set model.default "nvidia/nemotron-3.5-lightning:free"
docker compose up -d
docker logs hermes
```

Навык копируется в `data/skills/devops/site-healthcheck/`.

### Резервная цепочка моделей

Чтобы при ошибке лимита (429) или недоступности основной модели агент сам переходил на другую, в конец `data/config.yaml` добавляется следующий фрагмент. Включать можно только бесплатные модели: переключение срабатывает и на ошибку оплаты (402).

```yaml
fallback_providers:
  - provider: openrouter
    model: nvidia/nemotron-3-ultra-550b-a55b:free
  - provider: openrouter
    model: google/gemma-4-31b-it:free
  - provider: openrouter
    model: openrouter/free

auxiliary:
  free_only: true
```

Проверка: `hermes fallback list`, затем `docker restart hermes`. Подробнее — в отчёте, подразделы 2.9 и 3.8.

Лицензия: код и конфигурация — MIT; тексты — CC BY 4.0.
