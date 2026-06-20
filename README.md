# Cantina Core collection

`cantina.core` — базовая ansible-коллекция домашнего пет-проекта. Содержит
фундаментальные роли, на которые опираются прикладные сервисы из
[`cantina.apps`](https://github.com/v-kamerdinerov/cantina.apps).

> ### Почему cantina?
> Потому что инфраструктура — это бар, куда стекаются все сервисы. А `core` —
> это барная стойка, без которой остальное просто негде поставить.

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
  - name: https://github.com/v-kamerdinerov/cantina.core/releases/download/v0.1.0/cantina-core-0.1.0.tar.gz
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
git tag v0.1.0
git push origin v0.1.0
```

GitHub Actions сам соберёт коллекцию и создаст релиз с артефактом
`cantina-core-0.1.0.tar.gz`.

## Лицензия

[MIT](LICENSE)
