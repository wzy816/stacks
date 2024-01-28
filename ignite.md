# ignite

## run via docker-compose

```bash
# prepare docker-compose.yml
services:
  ignite_0:
    image: apacheignite/ignite:latest
    ports:
      - 10800:10800
    restart: always

  ignite_1:
    image: apacheignite/ignite:latest
    ports:
      - 10801:10800
    restart: always

  ignite_2:
    image: apacheignite/ignite:latest
    ports:
      - 10802:10800
    restart: always

# start 3 nodes service
docker-compose up -d
```

## run test scripts

[examples](https://github.com/apache/ignite-python-thin-client/tree/master/examples)
