# ООО «Обувь» — приложение для учета обуви

## Описание проекта

Проект представляет собой приложение для учета товаров обувного магазина ООО «Обувь».

## Используемые технологии

- Python 3.13
- Flet
- PostgreSQL
- psycopg2

## Структура проекта

- `main.py` — основной файл запуска приложения.
- `database.py` — работа с базой данных.
- `config.py` — настройки приложения и подключения к базе данных.
- `config.example.py` — пример файла конфигурации без настоящего пароля.
- `modules/` — дополнительные модули приложения.
- `assets/` — изображения и другие ресурсы приложения.
- `storage/` — папка для хранения данных.

## ER-диаграмма базы данных

Схема базы данных проекта представлена в виде ER-диаграммы. Графическое изображение находится в директории `docs/ER-diagram.png`.

Основные сущности базы данных:

- **`tovar`** — справочник товаров (обуви). Содержит артикул, наименование, единицу измерения, цену, информацию о поставщике и производителе, категорию, действующую скидку, количество на складе, описание и фото.
- **`orders`** — информация о заказах. Включает номер заказа, даты оформления и доставки, адрес пункта выдачи, ФИО авторизованного клиента, код для получения и текущий статус заказа.
- **`order_pick_up_points`** — справочник адресов пунктов выдачи заказов.
- **`orders_articles`** — связующая таблица (состав заказа), которая хранит информацию о том, какие именно товары (`tovar_id`) и в каком количестве входят в конкретный заказ (`Номер заказа`).
- **`users`** — данные пользователей системы. Содержит роль сотрудника, ФИО, логин и пароль для авторизации.

## Установка и запуск

1. Установить Python 3.13.
2. Установить PostgreSQL.
3. Установить необходимые зависимости:

   ```bash
   pip install -r requirements.txt
   ```

4. Создать файл `config.py` на основе `config.example.py` и указать параметры подключения к базе данных.
5. Запустить приложение:

   ```bash
   python main.py
   ```

## Запуск PostgreSQL через Docker Compose

### Предварительные требования
- Windows 10/11, Docker Desktop с Linux containers, Docker Compose v2.
- Python 3.10 и зависимости проекта (`pip install -r requirements.txt`).
- Файл резервной копии БД `db_shoe_store.sql` (custom-дамп `pg_dump`, формат 1.16, создан PostgreSQL 18).

### Создание .env
Скопируйте `.env.example` в `.env` и задайте локальный пароль:
POSTGRES_DB=shoe_store
POSTGRES_USER=shoe_user
POSTGRES_PASSWORD=<локальный пароль>
POSTGRES_HOST_PORT=5433
`.env` исключён из Git (`.gitignore`); не коммитьте его.

### Команды запуска
docker compose up --build -d
docker compose ps
Ожидаемое состояние: `db` — `Up (healthy)`, `db-check` — `Exited (0)`.

### Импорт БД
docker compose cp db_shoe_store.sql db:/tmp/db_shoe_store.dump
docker compose exec db pg_restore --no-owner --no-privileges --no-acl --dbname=shoe_store --username=shoe_user --verbose /tmp/db_shoe_store.dump
Ожидается 5 таблиц.

### Адрес подключения приложения
В `config.py` в словаре `DB_CONFIG`:
- `host`: `localhost`
- `port`: `5433`
- `database`: `shoe_store`
- `user`: `shoe_user`
- `password`: значение `POSTGRES_PASSWORD` из `.env`

Сервис `db-check` внутри Compose подключается к `db:5432` по имени сервиса. Приложение PyQt5 в Windows подключается к `localhost:5433` — опубликованному порту контейнера.

### Проверка
docker compose run --rm db-check
Ожидается: `OK: база=shoe_store; таблиц=5`.
docker compose exec db pg_isready -U shoe_user -d shoe_store
Ожидается: `accepting connections`.

### Остановка
docker compose stop
docker compose start

## Автор

alenamokhovaya-spec

Разработка проекта ведется с использованием системы контроля версий Git.