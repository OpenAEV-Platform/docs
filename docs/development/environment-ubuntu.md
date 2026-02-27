# Prerequisites Ubuntu

## Backend
* git, OpenJDK >= 21 and Maven via `apt-get`:
```shell
apt-get update && apt-get install git openjdk-21-jdk maven
```
* A container runtime is required to run development dependency containers (PostgreSQL, MinIO, RabbitMQ, Elasticsearch). Install **one** of the following:

=== "Docker Engine"

    Follow the official instructions at [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/).

=== "Podman"

    [Podman](https://podman.io/) is a daemonless, rootless alternative to Docker. Most `docker` commands translate directly to `podman`.

    ```shell
    apt-get update && apt-get install podman podman-compose
    ```

    💡 Podman provides a Docker-compatible CLI. All `docker compose` commands used in the [build from source](build-from-source.md) guide can be replaced with `podman compose`.

## Frontend
* yarn via `apt-get`

```shell
apt-get update && apt-get install yarnpkg
```

* Node.js >= 20 via `nvm`

Install `nvm` via its install script: [Installing and updating nvm](https://github.com/nvm-sh/nvm/blob/master/README.md#installing-and-updating).

Then install and set as default Node.js 22 (you should be able to use any version from 20 onwards though):
```shell
nvm install 22
nvm use 22
```
