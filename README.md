# Migration to Kubernetes

## Contents

* [Introduction](#introduction)
* [Prerequisites](#prerequisites)
* [Containerize the app](#containerize)
* [Local run](#local)
* [Kubernetes base](#kubernetes)

## Introduction  <a name="introduction"></a>

We are moving our services into cloud infrastructure and starting to use Kubernetes.
[Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) is container-orchestration system for automating application deployment, scaling, and management.
[Docker](https://www.docker.com/resources/what-container) is a set of platform as a service (PaaS) products that use OS-level virtualization to deliver software in packages called containers.

This instruction will be helpful for developers who has never worked either with Kubernetes or with Docker.

This instruction describes how to:
- prepare [docker](https://www.docker.com/resources/what-container) image of application 
- build and publish docker image
- how to run your app in docker container
- prepare working infrastructure.

An example application and image described in this article located in the repository too.

- image
- container

See also:
- [Docker Documentation](https://docs.docker.com/)
- [Kubernetes Documentation](https://kubernetes.io/docs/home/)

## Prerequisites <a name="prerequisites"></a>

Docker is available for different platforms but this user guide is primary for Windows.
General system requirements can be taken from [Docker Installation Guide](https://docs.docker.com/docker-for-windows/install/#system-requirements).
In addition to this you should install:
- [Docker](https://docs.docker.com/docker-for-windows/install/)
- [Git](https://git-scm.com/download/win) (optional, since you can download this repository).

## Containerize the app <a name="containerize"></a>

Let's take an example of dockerization of `ExampleApp` Python application, which accepts connection on port 8000.

1. It is better to stick following repository structure:

```text
quickstart_docker/  # root directory of project
├──application/     # application source code located here
└──docker/          # Docker-related files
   └──application/  # contains Dockerfile
```

To create such structure you can use following command:

```bash
mkdir quickstart_docker \
mkdir quickstart_docker/application \
mkdir quickstart_docker/docker \
mkdir quickstart_docker/docker/application
```

2. Prepare your application for work inside a container;

3. Put all your source code into `quickstart_docker/application`;

In this example we create `application.py` file there with the following content:

```python
import http.server
import socketserver

PORT = 8000

Handler = http.server.SimpleHTTPRequestHandler

httpd = socketserver.TCPServer(("", PORT), Handler)

print("serving at port", PORT)
httpd.serve_forever()
```

4. Configure container for your application.

Image defines in text configuration file called [Dockerfile](https://docs.docker.com/engine/reference/builder/).

In our scenario we will use following file:

```text
FROM python:3.5
WORKDIR /app
COPY ./application /app
EXPOSE 8000
CMD ["python", "/app/application.py"]
```

- `FROM python:3.5` - Use base image from the registry.
- `WORKDIR /app` - Set the working directory to /app.
- `COPY ./application /app` - Copy the 'application' directory contents into the container at /app.
- `EXPOSE 8000` - Make port 8000 available to the world outside this container.
- `CMD ["python", "/app/application.py"]` - Execute 'python /app/application.py' when container launches.

5. Build an image using created Dockerfile.

For this execute [`docker build`](https://docs.docker.com/engine/reference/commandline/build/) command:

```bash
$ docker build . -f docker/application/Dockerfile -t exampleapp
```

Arguments:
- `.` - working directory;
- `-f docker/application/Dockerfile` - path to your Dockerfile;
- `-t exampleapp` - name of created Docker image.

6. Check that new image has appeared.

To list all Docker images existing in your local registry use [`docker images`](https://docs.docker.com/engine/reference/commandline/images/) command

```bash
$ docker images
```

REPOSITORY  |           TAG    |          IMAGE ID      |     CREATED        |        SIZE|
------------|------------------|------------------------|--------------------|------------|
exampleapp  |            latest|          83wse0edc28a  |       2 seconds ago|       153MB|
python      |           3.6    |         05sob8636w3f   |     6 weeks ago    |       153MB|

7. Push your image to remote registry

Describe registries concept

Firstly you should login to authorize in remote Docker registry via [`docker login`](https://docs.docker.com/engine/reference/commandline/login/) command

```bash
$ docker login
```

To push some image from your local registry to remote one use [`docker push`](https://docs.docker.com/engine/reference/commandline/push/) command

```bash
$ docker push exampleapp
```

## Local run <a name="local"></a>

Now you or anyone else can run you application from scratch with a single command.

To run (and pull if needed) Docker image you can use [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) command

```bash
$ docker run -p 8000:8000 exampleapp
```

Arguments:
- `-p 8000:8000` - port mapping
- `exampleapp` - name of Docker image

## Kubernetes base <a name="kubernetes"></a>

Learn more about Kubernetes technology:
- [Kubernetes concepts](https://kubernetes.io/docs/concepts/)
- [Kubernetes setup](https://kubernetes.io/docs/setup/)
