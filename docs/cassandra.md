## Cassandra

Currently, the docker image is very basic and based off the default Cassandra Docker image. We're using `docker-compose` to build up a simple dev environment, as well as a potential test environment when deployed.

### Clustering

For simple clustering, we can use `docker-compose` to create multiple nodes and link them together. For example:

```yml
version: '2'
services:
  cassandra_01:
    build: ./cassandra
    ports:
      - "7000:7001"
      - "7070:7070"
      - "7199:7199"
      - "9042:9042"
      - "9160:9160"
  cassandra_02:
    build: ./cassandra
    ports:
      - "7071:7070"
    links:
      - "cassandra_01:seed"
    depends_on:
      - cassandra_01
    environment:
      SEEDS: seed
  prometheus:
    build: ./prometheus
    ports:
      - "9090:9090"
    depends_on:
      - cassandra_01
      - cassandra_02
```

Here, we have Prometheus depending on both nodes before starting, and we specify the link nodes in Prometheus' config file by doing:

```yml
global:
 scrape_interval: 10s
 evaluation_interval: 10s
scrape_configs:
 - job_name: 'cassandra_01'
   static_configs:
    - targets:
      - cassandra_01:7070
 - job_name: 'cassandra_02'
   static_configs:
    - targets:
      - cassandra_02:7070
```

This allows us to monitor `N` nodes (albeit in a very manual process).
