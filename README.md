# kubernetes-migration

## Contents

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [Containerize the app](#containerize)
* [Local run](#local)
* [Kubernetes base](#kubernetes)

## Introduction  <a name="introduction"></a>
We move our services into cloud environment and use [Kubernetes](https://kubernetes.io/docs/tutorials/kubernetes-basics/).
This instruction will be helpful for developers who have never worked with Kubernetes.
This instruction describe how to:
- prepare [docker](https://www.docker.com/resources/what-container) image of application 
- prepare working infrastructure
- deploy docker image.

See also:
- [Docker get stated](https://www.docker.com/get-started)
- [Docker on wiki](https://en.wikipedia.org/wiki/Docker_(software))
- [Kubernetes in wiki](https://en.wikipedia.org/wiki/Kubernetes)

## Prerequisites <a name="prerequisites"></a>
Before start you should install
- [git](https://git-scm.com/)
- [docker](https://docs.docker.com/docker-for-windows/install/)

## Containerize the app <a name="containerize"></a>
 You should run docker with your app in Kubernetes Cluster.
 For example, take an application  `ExampleApp`, which accepts connection on port 8000.
  
1. Create directory and subdirectories:

```bash
mkdir quickstart_docker
mkdir quickstart_docker/application
mkdir quickstart_docker/docker
mkdir quickstart_docker/docker/application
```

2. You can move all into the root. But prefer structure for convince.

```text
quickstart_docker/ # directory the whole project
├──application/      # project code
└──docker/           # stuff for docker
   └──application/    # dockerfile for app
```

3. Prepare application for deploy.

4. For example take the next python-application (after you can change this)

5. In directory `quickstart_docker/application` put `application.py` file:

```python
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

httpd = socketserver.TCPServer(("", PORT), Handler)

print("serving at port", PORT)
httpd.serve_forever()
```
6. Add environment for app.

 **Requirements**:
 - python
 - OS for python with all dependences. Это все есть в докерхабе, возьмем оттуда. (нужна ссылка, незнаюгде взять)

7. In directory `quickstart_docker/docker/application` put file with `Dockerfile` name with information:

```text
FROM python:3.5  # Use base image from the registry
WORKDIR /app  # Set the working directory to /app
COPY ./application /app # Copy the 'application' directory contents into the container at /app
EXPOSE 8000 # Make port 8000 available to the world outside this container
CMD ["python", "/app/application.py"] # Execute 'python /app/application.py' when container launches
```

8. In the terminal:

```bash
docker build . -f-docker/application/Dockerfile -t exampleapp
```
Arguments:
 - work directory
 - build context.
 
```text
 -f docker/application/Dockerfile - docker-файл;
 -t exampleapp - тэгируем образ, чтоб потом можно было его найти.
```
See also about bulid image for [Docker](https://docs.docker.com/engine/reference/builder/).

9. Look at the list of docker images:

```bash
$ docker images
```
REPOSITORY  |           TAG    |          IMAGE ID      |     CREATED        |        SIZE|
------------|------------------|------------------------|--------------------|------------|
exampleapp  |            latest|          83wse0edc28a  |       2 seconds ago|       153MB|
python      |           3.6    |         05sob8636w3f   |     6 weeks ago    |       153MB|


10. Next we a going to put docker image into repository.

## Local run <a name="local"></a>



## Kubernetes base <a name="kubernetes"></a>
Learn more about Kubernetes technology:
- [Kubernetes concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes setup](https://kubernetes.io/docs/setup/)


