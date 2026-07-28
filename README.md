# Cantina Core collection

`cantina.core` — базовая ansible-коллекция ролей для личных проектов. Содержит
фундаментальные роли, на которые опираются прикладные сервисы из
[`cantina.apps`](https://github.com/v-kamerdinerov/cantina.apps).

> ### Почему cantina?
> Потому что инфраструктура — это бар, куда стекаются все сервисы. А `core` —
> это барная стойка, без которой остальное просто негде поставить.

![cantina](https://news.fit.edu/wp-content/uploads/2015/12/cantina_denizens_1.jpg)

## Включённые роли

| Роль | Описание |
|------|----------|
| `common` | Базовая настройка хоста (пакеты, пользователи, sysctl и т.п.) |
| `docker` | Установка и настройка Docker / docker compose |
| `caddy` | Установка и конфигурирование reverse-proxy Caddy |
| `postgres` | Установка и настройка PostgreSQL |

## Установка

Из артефакта GitHub Release:

```yaml
# requirements.yml
collections:
  - name: https://github.com/v-kamerdinerov/cantina.core/releases/download/v0.0.3/cantina-core-0.0.3.tar.gz
```

```bash
ansible-galaxy collection install -r requirements.yml
```

## Использование

```yaml
- hosts: all
  roles:
    - cantina.core.common
    - cantina.core.docker
    - cantina.core.caddy
    - cantina.core.postgres
```

## Готовые примеры плейбуков

Коллекция содержит директорию `playbooks/` с готовыми примерами - после
установки коллекции их можно запускать по FQCN, без копирования файлов
в свой проект:

| Плейбук | Описание |
|---------|----------|
| `cantina.core.common` | Базовая настройка хоста (роль `common`) |
| `cantina.core.docker` | Установка и настройка Docker (роль `docker`) |
| `cantina.core.caddy` | Развертывание Caddy reverse-proxy (роль `caddy`) |
| `cantina.core.postgres` | Локальный standalone PostgreSQL в docker-контейнере (роль `postgres`) |
| `cantina.core.postgres_managed_backup` | Полный бекап managed PostgreSQL инстанса (`postgres` / `managed_backup`) |
| `cantina.core.postgres_managed_databases` | Создание БД/пользователей на managed-инстансе (`postgres` / `managed_add_db`) |

```bash
ansible-playbook cantina.core.postgres -i inventories/hosts.yml --limit my_host
```

## Разработка

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
ansible-galaxy collection install -r requirements.yml

# линтеры
yamllint .
ansible-lint -c tools/ansible-lint/.ansible-lint.yml roles/

# локальная сборка артефакта
ansible-galaxy collection build --output-path ./dist --force
```

## CI/CD

| Workflow | Триггер | Что делает |
|----------|---------|------------|
| `lint` | push в `main`/`master`, любой PR | `yamllint` + `ansible-lint` |
| `release` | git-тег `vX.Y.Z` | подставляет версию в `galaxy.yml`, собирает `.tar.gz`, прикладывает его к GitHub Release |

### Как выпустить версию

```bash
git tag v0.0.3
git push origin v0.0.3
```

GitHub Actions сам соберёт коллекцию и создаст релиз с артефактом
`cantina-core-0.0.3.tar.gz`.

## Лицензия

[MIT](LICENSE)
