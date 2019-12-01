# kubernetes-migration

Ставь себе докер и го вновь сюда. 
Смотри, нам нужно запустить контейнер с приложением в кластере Кубернетес. Для примера, возьмем наш ExampleApp, пусть принимает запросы на порту 8800. 
Создаем каталог и подкаталоги:
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application

quickstart_docker/ # Каталог всего проекта
├──application/      # Тут будет лежать код приложения
└──docker/           # Тут будет всякий стафф для докера
   └──application/    # ...здесь положим Dockerfile для приложения
(Мне кажтеся, можно все и в корень пихнуть, но говорят, что надо структуру и все дела, )
Едем дальше. Чтобы продеплоить приложение, нам понадобится приложение. Для примера возьмем пока такое. Потом нужно заменить.
В каталог quickstart_docker/application положим файл application.py:
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

httpd = socketserver.TCPServer(("", PORT), Handler)

print("serving at port", PORT)
httpd.serve_forever()
Приложению нужно окружение. Как минимум понадобится python, а ему, в свою очередь, ОС со всеми зависимостями. Это все есть в докерхабе, возьмем оттуда.
Итак, в каталог quickstart_docker/docker/application кладём файл с именем Dockerfile и следующим содержимым:
# Use base image from the registry
FROM python:3.5

# Set the working directory to /app
WORKDIR /app

# Copy the 'application' directory contents into the container at /app
COPY ./application /app

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Execute 'python /app/application.py' when container launches
CMD ["python", "/app/application.py"]
Следующий шаг. В терминале набираем:
docker build . -f-docker/application/Dockerfile -t exampleapp
Аргументы: . - рабочий каталог, контекст сборки;  -f docker/application/Dockerfile - docker-файл; -t exampleapp - тэгируем образ, чтоб потом можно было его найти.
Подробнее про сборку образов для Docker здесь https://docs.docker.com/engine/reference/builder/.
Ну и смотрим список образов:
$ docker images
REPOSITORY             TAG             IMAGE ID            CREATED             SIZE
exampleapp             latest          83wse0edc28a        2 seconds ago       153MB
python                 3.6             05sob8636w3f        6 weeks ago         153MB

Дальше будем закидывать образ в репозиторий.
