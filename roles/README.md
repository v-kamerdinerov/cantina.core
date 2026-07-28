
### `common`
Базовая роль для настройки сервера:
- Настройка SSH (порт, безопасность)
- Установка базовых пакетов
- Настройка системных лимитов (ulimits)
- Конфигурация sysctl параметров
- Настройка UFW фаервола

### `docker`
Роль для установки и настройки Docker:
- Удаление старых версий Docker
- Установка Docker Engine и Docker Compose
- Добавление Docker репозитория
- Настройка Docker daemon
- Добавление пользователей в группу docker
- Обновление Docker образов сервисов

### `caddy`
Роль для установки и настройки Caddy в качестве reverse proxy:
- Создание директорий для Caddy
- Генерация docker-compose.yml и Caddyfile из шаблонов
- Управление HTTPS и маршрутизацией для n8n, app и других сервисов
- **Поддержка SSO (GitHub OAuth)**: защита веб-интерфейсов через GitHub аутентификацию
- Динамическая конфигурация маршрутов на основе переменных `caddy_services` и `projects`
- Использование shared Docker network для взаимодействия с сервисами
- Запуск Caddy через Docker Compose
- Создание резервных копий при обновлении конфигураций (backup: true)

### `postgres`
Роль для развертывания отдельного PostgreSQL сервера:
- Развертывание PostgreSQL в Docker контейнере
- Настройка производительности БД (max_connections, shared_buffers и т.д.)
- Создание пользователей и баз данных
- Загрузка данных из резервных копий
- Резервное копирование баз данных
- Слушает на порту 5433 (отлично от стандартного 5432)

Дополнительно роль содержит таски для работы с **managed** PostgreSQL
инстансом (RDS/Cloud SQL/Yandex MDB и т.п.), а не соседним docker-контейнером.
Обе таски не входят в основной сценарий роли (tasks/main.yml) и вызываются
только явно, отдельными плейбуками:

- `managed_backup` (tasks/managed_backup.yml) - полный бекап инстанса:
  дампятся глобальные объекты и все пользовательские базы целиком.
  Пример: `playbooks/postgres_managed_backup.yml`.
- `managed_add_db` (tasks/managed_add_db.yml) - создание БД/пользователей
  на managed-инстансе по переменной `postgres_managed_databases` (тот же
  формат, что и `postgres_databases` для локального контейнера).
  Пример: `playbooks/postgres_managed_databases.yml`.

```yaml
- import_role:
    name: cantina.core.postgres
    tasks_from: managed_backup   # или managed_add_db
```

