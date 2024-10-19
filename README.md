# Taski Docker Project
[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Django](https://img.shields.io/badge/Django-4.x-brightgreen.svg)](https://www.djangoproject.com/)
[![Docker](https://img.shields.io/badge/Docker-ready-blue.svg)](https://www.docker.com/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-blue.svg)](https://www.postgresql.org/)
[![CI/CD](https://img.shields.io/badge/CI/CD-GitHub%20Actions-yellow.svg)](https://github.com/features/actions)

## Описание проекта
**Taski** - это уникальный сервис для любителей котиков. Проект позволяет пользователям добавлять, просматривать, редактировать и удалять котиков, а также присваивать им достижения и просматривать котов других пользователей.

## Что умеет проект:
- **Добавление котиков**: Создание профилей для котиков.
- **Просмотр котиков**: Просмотр всех котиков и их достижений.
- **Редактирование котиков**: Изменение информации о котиках.
- **Удаление котиков**: Удаление котиков из базы данных.
- **Достижения**: Добавление новых и присваивание существующих достижений.

## Установка
1. **Клонируйте репозиторий на свой компьютер**:
    ```bash
    git clone https://github.com/ak1-0taski-docker.git
    cd taski_final
    ```

2. **Создайте файл .env и заполните его своими данными**:
    Перечень данных указан в корневой директории проекта в файле `.env.example`.

## Создание Docker-образов
1. **Замените username на ваш логин на DockerHub**:
    ```bash
    cd backend
    docker build -t username/taski_backend:latest .
    cd ../frontend
    docker build -t username/taski_frontend:latest .
    cd ../gateway
    docker build -t username/taski_gateway:latest .
    ```

2. **Загрузите образы на DockerHub**:
    ```bash
    docker push username/taski_backend:latest
    docker push username/taski_frontend:latest
    docker push username/taski_gateway:latest
    ```

## Деплой на сервере
1. **Подключитесь к удаленному серверу**:
    ```bash
    ssh -i /path_to_ssh_key username@server_ip
    ```

2. **Создайте на сервере директорию taski**:
    ```bash
    mkdir taski
    ```

3. **Установите Docker и Docker Compose на сервер**:
    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```

4. **Скопируйте файлы `docker-compose.production.yml` и `.env` в директорию `taski/` на сервере**:
    ```bash
    scp -i /path_to_ssh_key docker-compose.production.yml username@server_ip:/home/username/taski/docker-compose.production.yml
    scp -i /path_to_ssh_key .env username@server_ip:/home/username/taski/.env
    ```

5. **Добавьте в файл .env переменные и их значения**:
    ```env
    POSTGRES_USER=django_user
    POSTGRES_PASSWORD=mysecretpassword
    POSTGRES_DB=django
    ```

6. **Запустите контейнеры, выполните миграции, соберите статические файлы**:
    ```bash
    sudo docker compose -f docker-compose.production.yml pull
    sudo docker compose -f docker-compose.production.yml down
    sudo docker compose -f docker-compose.production.yml up -d
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic --noinput
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collect_static/. /backend_static/static/
    ```

## Настройка Nginx
1. **Откройте конфиг Nginx на сервере**:
    ```bash
    sudo nano /etc/nginx/sites-enabled/default
    ```

2. **Измените настройки location в секции server**:
    ```nginx
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:8000;
    }
    ```

3. **Проверьте конфигурацию и перезапустите Nginx**:
    ```bash
    sudo nginx -t
    sudo service nginx reload
    ```

## Настройка CI/CD
1. **Файл workflow уже написан и находится в директории `taski/.github/workflows/main.yml`**:
2. **Добавьте секреты в GitHub Actions**:
    - `DOCKER_USERNAME`                # имя пользователя в DockerHub
    - `DOCKER_PASSWORD`                # пароль пользователя в DockerHub
    - `HOST`                           # ip_address сервера
    - `USER`                           # имя пользователя
    - `SSH_KEY`                        # приватный ssh-ключ (`cat ~/.ssh/id_rsa`)
    - `SSH_PASSPHRASE`                 # кодовая фраза (пароль) для ssh-ключа
    - `TELEGRAM_TO`                    # id телеграм-аккаунта (можно узнать у `@userinfobot`, команда `/start`)
    - `TELEGRAM_TOKEN`                 # токен бота (получить токен можно у `@BotFather`, `/token`, имя бота)

## Вклад
Не стесняйтесь открывать вопросы и отправлять pull-запросы. Будь то исправление ошибки, новая функция или предложение по улучшению, все приветствуются!

## Лицензия
Этот проект лицензирован под лицензией MIT.
