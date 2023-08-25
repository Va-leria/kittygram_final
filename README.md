# Kittygram
![workflow](https://github.com/Va-leria/kittygram_final/actions/workflows/main.yml/badge.svg)

### Возможности проекта
* Добавлять котиков и их достижения
* Смотреть и комментировать чужих котиков

___

### Стек технологий

* Python
* Django
* React
* JavaScript
* Ubuntu
* Gunicorn
* Nginx

---

## Установка 

1. Клонируйте репозиторий и перейдите в корневую директорию:

    ```bash
    git clone git@github.com:Va-leria/kittygram_final.git
    ```
    ```bash
    cd kittygram
    ```
2. Укадите необходимые переменные в файле .env. Перечень данных переменных указан в корневой директории проекта в файле .env.example.

___
## Билд Docker-образов

P.S. Далее везде замените username на ваш логин на DockerHub:

1.  Сделайте билд образов для frontend, backend и gateway

    ```bash
    cd frontend
    docker build -t <username>/kittygram_frontend .
    cd ../backend
    docker build -t <username>/kittygram_backend .
    cd ../nginx
    docker build -t <username>/kittygram_gateway . 
    ```

2. Загрузите образы на DockerHub:

    ```bash
    docker push <username>/kittygram_frontend
    docker push <username>/kittygram_backend
    docker push <username>/kittygram_gateway
    ```

___

## Локальная сборка

1. Установите на свой компьютер docker
   ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```
2. Поднимите docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.yml up -d
    ```
    P.S. При поднятии docker-compose произойдет билд трех образов из Dockerfile, которые лежат в директориях frontend, backend и gateway

## Деплой на сервере

1. Подключитесь к удаленному серверу

    ```bash
    ssh -i path_to_SSH/SSH_name username@server_ip
    * path_to_SSH — путь к файлу с SSH-ключом;
    * SSH_name — имя файла с SSH-ключом (без расширения);
    * username — ваше имя пользователя на сервере;
    * server_ip — IP вашего сервера.
    ```

2. Установите на сервер docker:

    ```bash
    sudo apt update
    sudo apt install curl
    curl -fSL https://get.docker.com -o get-docker.sh
    sudo sh ./get-docker.sh
    sudo apt-get install docker-compose-plugin
    ```
3. Создайте на сервере директорию kittygram через терминал

    ```bash
    mkdir kittygram
    ```
4. В директорию kittygram/ скопируйте файлы docker-compose.production.yml и .env:

    ```bash
    scp -i path_to_SSH/SSH_name docker-compose.production.yml username@server_ip:/home/username/kittygram/docker-compose.production.yml
    scp -i path_to_SSH/SSH_name .env username@server_ip:/home/username/kittygram/.env
    ```

5. Поднимите docker compose в режиме демона:

    ```bash
    sudo docker compose -f docker-compose.production.yml up -d
    ```

6. Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /static/static/:

    ```bash
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py migrate
    sudo docker compose -f docker-compose.production.yml exec backend python manage.py collectstatic
    sudo docker compose -f docker-compose.production.yml exec backend cp -r /app/collected_static/. /static/static/
    ```

7. На сервере откройте конфиг Nginx:

    ```bash
    sudo vi /etc/nginx/sites-enabled/default
    ```

8. Измените настройки location в секции server:

    ```bash
    location / {
        proxy_set_header Host $http_host;
        proxy_pass http://127.0.0.1:9000;
    }
    ```

9. Проверьте работоспособность конфига Nginx:

    ```bash
    sudo nginx -t
    ```

10. Перезапустите Nginx
    ```bash
    sudo service nginx reload
    ```

___
## Настройка CI/CD

1. Создайте в своем репозитории на Git Hub дирректорию .github/workflows/ и файл main.yml

2. Содержимое файла main.yml можете взять из этой директории: kittygram/.github/workflows/main.yml

3. Добавьте в GitHub секреты:

    ```bash
    DOCKER_USERNAME                # имя пользователя в DockerHub
    DOCKER_PASSWORD                # пароль пользователя в DockerHub
    HOST                           # ip_address сервера
    USER                           # имя пользователя
    SSH_KEY                        # приватный ssh-ключ (cat ~/.ssh/id_rsa)
    SSH_PASSPHRASE                 # кодовая фраза (пароль) для ssh-ключа
    ```

