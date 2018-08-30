# How to customise this stack

* [Add Redis](#redis)
* [Add Elk](#elk)

## Add Redis<a name="redis"></a>

1. Update docker-compose.yml file and add the following lines:

    ```yml
    service:
        # ...
        redis:
            image: redis:alpine
            ports:
                - 6379:6379
    ```

Access to redis-cli with:

```bash
# Redis commands
$ docker-compose exec redis redis-cli
```

## Add Elk<a name="elk"></a>

1. Update docker-compose.yml file and add the following lines:

    ```yml
    elk:
        image: willdurand/elk
        ports:
            - 81:80
        volumes:
            - ./docker/configs/elk/logstash:/etc/logstash
            - ./docker/configs/elk/logstash/patterns:/opt/logstash/patterns
        volumes_from:
            - php
            - nginx
    ```
    
2. Copy the optional-elk folder to the docker configs location:

    ```bash
    $ cp -r ./docs/optional-elk ./docker/configs/elk
    ```
    
3. Visit: [local.project:81](http://local.project:81)