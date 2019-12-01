# Migration to Kubernetes

## Contents

* [Introduction](#introduction)
* [Docker](#docker)
* [Prerequisites](#prerequisites)
* [Build](#build)
* [Run](#run)
* [Kubernetes basics](#kubernetes)

## Introduction  <a name="introduction"></a>

We are moving our services into cloud infrastructure and starting to use Kubernetes.

[Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) is a container-orchestration system for automating application deployment, scaling, and management.

[Docker](https://www.docker.com/resources/what-container) is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers.

This instruction will be helpful for developers who has never worked either with Kubernetes or with Docker.

This instruction describes how to:
- prepare docker image with application 
- build and publish docker image
- run your application inside docker container.

In the same repository with this article all mentioned files are located.

See also:
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)

## Docker basics <a name="docker"></a>

- image
- [container]((https://www.docker.com/resources/what-container))
- registry

## Prerequisites <a name="prerequisites"></a>

Docker is available for different platforms but this user guide is primary for Windows platform.
General system requirements can be found in [Docker Installation Guide](https://docs.docker.com/docker-for-windows/install/#system-requirements) article.
In addition to this you should install:
- [Docker](https://docs.docker.com/docker-for-windows/install/)
- [Git](https://git-scm.com/download/win) (optional, since you can download this repository as .zip).

## Build <a name="build"></a>

Following step-by-step guide describes how to wrap your application inside Docker image. 
As as example we will take Python application called `ExampleApp`, which accepts HTTP-requests on port 8000.

1. Create folder structure as follows:

```text
quickstart_docker/  # root directory of project
├──application/     # application source code located here
└──docker/          # Docker-related files
   └──application/  # contains Dockerfile
```

To create such structure you can use command:

```bash
mkdir quickstart_docker \
mkdir quickstart_docker/application \
mkdir quickstart_docker/docker \
mkdir quickstart_docker/docker/application
```

2. Put all your source code into `quickstart_docker/application`;

In this example we create `application.py` file with the following content:

```python
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

httpd = socketserver.TCPServer(("", PORT), Handler)

print("serving at port", PORT)
httpd.serve_forever()
```

3. Configure container for your application.

Image content defined in configuration file called [Dockerfile](https://docs.docker.com/engine/reference/builder/).

In our scenario we will use following file:

```text
FROM python:3.5
WORKDIR /app
COPY ./application /app
EXPOSE 8000
CMD ["python", "/app/application.py"]
```

- `FROM python:3.5` - inherit from public image from Docker Hub registry with python and all dependencies installed.
- `WORKDIR /app` - create working directory with application.
- `COPY ./application /app` - copy local `application` directory to the container's `app`.
- `EXPOSE 8000` - make port 8000 of container available outside this container.
- `CMD ["python", "/app/application.py"]` - execute `python /app/application.py` during every container launch.

4. Build an image using created Dockerfile.

For this execute [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) command:

```bash
$ docker build . -f docker/application/Dockerfile -t exampleapp
```

Arguments:
- `.` - working directory;
- `-f docker/application/Dockerfile` - path to your Dockerfile;
- `-t exampleapp` - name of created Docker image.

5. Check that new image has appeared.

To list all Docker images existing in your local registry use [`docker images`](https://docs.docker.com/engine/reference/commandline/images/) command

```bash
$ docker images
```

REPOSITORY  |           TAG    |          IMAGE ID      |     CREATED        |        SIZE|
------------|------------------|------------------------|--------------------|------------|
exampleapp  |            latest|          83wse0edc28a  |       2 seconds ago|       153MB|
python      |           3.6    |         05sob8636w3f   |     6 weeks ago    |       153MB|

6. Push your image to remote registry

Firstly you should authorize in remote Docker registry via [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) command

```bash
$ docker login
```

To push some image from your local registry to remote one use [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) command

```bash
$ docker push exampleapp
```

## Run <a name="run"></a>

Now you or anyone else can run your application with a single command.

To run (and pull if needed) Docker image you can use [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) command

```bash
$ docker run -p 8000:8000 exampleapp
```

Arguments:
- `-p 8000:8000` - port mapping
- `exampleapp` - name of Docker image

Now your application is available on `http://localhost:8000/`

## Kubernetes basics <a name="kubernetes"></a>

Learn more about Kubernetes technology:
- [Kubernetes concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes setup](https://kubernetes.io/docs/setup/)
