ageres210784/kafka
=========

Role to run kafka or kafka cluster in docker or docker swarm

Requirements
------------

This role uses module community.docker.docker_compose, which is part of the community.docker collection (version 3.7.0).

Role Variables
--------------

You can see all vars in `defaults/main.yml` vars file.

For start kafka in swarm change `kafka_run_in_swarm: true`. Labels should be used to run kafka nodes on suitable hosts.

For create `KAFKA_KRAFT_CLUSTER_ID` use script `kafka-storage.sh`:
```
docker run --rm bitnami/kafka kafka-storage.sh random-uuid
```

For run cluster you should configure variable `kafka_docker_services`.
```yaml
kafka_docker_services:
  - name: kafka0
    environment:
      - KAFKA_KRAFT_CLUSTER_ID=c1LusqidTh2cSfwE_nmhmw
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka0:9092,EXTERNAL://node:9090
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka0:9093,1@kafka1:9093,2@kafka2:9093
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
    deploy:
      placement:
        constraints:
          - node.labels.kafka0 == true
    ports:
      - "9090:9094"
    volume:
      - kafka_volume0
  - name: kafka1
    environment:
      - KAFKA_KRAFT_CLUSTER_ID=c1LusqidTh2cSfwE_nmhmw
      - KAFKA_CFG_NODE_ID=1
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka1:9092,EXTERNAL://node:9091
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka0:9093,1@kafka1:9093,2@kafka2:9093
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
    deploy:
      placement:
        constraints:
          - node.labels.kafka1 == true
    ports:
      - "9091:9094"
    volume:
      - kafka_volume1
  - name: kafka2
    environment:
      - KAFKA_KRAFT_CLUSTER_ID=c1LusqidTh2cSfwE_nmhmw
      - KAFKA_CFG_NODE_ID=2
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,PLAINTEXT:PLAINTEXT,EXTERNAL:PLAINTEXT
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka2:9092,EXTERNAL://node:9092
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka0:9093,1@kafka1:9093,2@kafka2:9093
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
    deploy:
      placement:
        constraints:
          - node.labels.kafka2 == true
    ports:
      - "9092:9094"
    volume:
      - kafka_volume2
```

Dependencies
------------

Installed docker, docker-compose, [docker swarm] on the target host.

Created docker network on the target host, variable - `kafka_docker_network_name`

Example Playbook
----------------

```yaml
- name: Run kafka
  hosts: all
  roles:
      - { role: kafka, tags: ["kafka"] }
```

License
-------

Apache 2.0

Author Information
------------------

This role was created by [Sergey Evseev].
