# My local project stack (PHP7-FPM - NGINX - MySQL)

This is the local environment I develop my open-source little tinker projects in. It has everything you need for developing simple web applications. This complete stack runs with [docker](https://docs.docker.com/get-started/) and [docker-compose](https://docs.docker.com/compose/) (1.7 or higher).

* [Installation](#installation)
* [Usage](#usage)
* [Customize](#custom)
* [How it works?](#explanation)
* [Useful commands](#useful-commands)

## Installation<a name="installation"></a>

1. Create a `config.env` from the `config.env.dist` file. Adapt it according to your needs.

    ```bash
    cp config.env.dist config.env
    ```

2. Build/run containers (with or without detached mode -d)

    ```bash
    $ docker-compose build
    $ docker-compose up -d
    ```

3. Update your system host file (add local.project)

    ```bash
    # UNIX only: get containers IP address and update host (replace IP according to your configuration)
    $ sudo echo $(docker network inspect bridge | grep Gateway | grep -o -E '[0-9\.]+') "local.project" >> /etc/hosts
    # on Windows, edit C:\Windows\System32\drivers\etc\hosts
    ```

5. Enjoy :-)

## Usage<a name="usage"></a>

Just run `docker-compose up -d`, then:

* app front page: visit [local.project](http://local.project)  
* phpmyadmin panel: visit [local.project:8080](http://local.project:8080)

## Customize<a name="custom"></a>

If you want to add optional containers like Redis, Elk(Kibana) etc. take a look on [docs/custom.md](docs/custom.md).

## How it works?<a name="explanation"></a>

Have a look at the `docker-compose.yml` file, here are the `docker-compose` built images:

* `local_mysql`: This is the MySQL database container,
* `local_php`: This is the PHP-FPM container in which the application volume is mounted,
* `local_nginx`: This is the Nginx webserver container in which application volume is mounted too,
* `local_phpmyadmin`: This is the phpmyadmin container, which is linked to the MySQL database container, and gives you easy access to the database right from your browser.

This results in the following running containers:

```bash
$ docker ps

CONTAINER ID    IMAGE                   COMMAND                         PORTS                            NAMES
d483043e19ee    phpmyadmin/phpmyadmin   "/run.sh supervisord…"          9000/tcp, 0.0.0.0:8080->80/tcp   local_phpmyadmin
82f49b22e220    php7.0_xdebug           "docker-php-entrypoi…"   ...    9000/tcp                         local_php
bdd30bc97851    percona:5.6             "docker-entrypoint.s…"                                           local_mysql
e10b4b22a4d9    nginx:latest            "nginx -g 'daemon of…"                                           local_nginx
```

## Useful commands<a name="useful-commands"></a>

```bash
# bash commands
$ docker-compose exec php bash

# Composer (e.g. composer update)
$ docker run --rm --interactive --tty --volume ${PWD}:/app --user ${(id -u)}:${(id -g)} composer update

# Retrieve an IP Address (here for the nginx container)
$ docker inspect --format '{{ .NetworkSettings.Networks.dockersymfony_default.IPAddress }}' $(docker ps -f name=local_nginx -q)
$ docker inspect $(docker ps -f name=local_nginx -q) | grep IPAddress

# MySQL commands
$ docker-compose exec db mysql -uroot -p"root"

# Check CPU consumption
$ docker stats $(docker inspect -f "{{ .Name }}" $(docker ps -q))

# Delete all containers
$ docker rm $(docker ps -aq)

# Delete all images
$ docker rmi $(docker images -q)
```