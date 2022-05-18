---
title: Kafka command
author: ianpowell
date: 2022-05-18 12:34:00 +0100
categories: [kafka]
tags: [misc]
---

## Logging into the Kafka Container

``` bash
docker exec -it kafka-broker /bin/bash
```

## Navigate to the Kafka Scripts directory

``` bash
cd /opt/bitnami/kafka/bin
```

## Creating new Topics

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --create \
    --topic kafka.learning.tweets \
    --partitions 1 \
    --replication-factor 1

./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --create \
    --topic kafka.learning.alerts \
    --partitions 1 \
    --replication-factor 1
```

## Listing Topics

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --list
```

## Getting details about a Topic

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --describe
```

## Publishing Messages to Topics

``` bash
./kafka-console-producer.sh \
    --bootstrap-server localhost:29092 \
    --topic kafka.learning.tweets
```

## Consuming Messages from Topics

``` bash
./kafka-console-consumer.sh \
    --bootstrap-server localhost:29092 \
    --topic kafka.learning.tweets \
    --from-beginning
```

## Deleting Topics

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:29092 \
    --delete \
    --topic kafka.learning.alerts
```


## Create a Topic with multiple partitions

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --create \
    --topic kafka.learning.orders \
    --partitions 3 \
    --replication-factor 1
```

## Check topic partitioning

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --topic kafka.learning.orders \
    --describe
```

## Publishing Messages to Topics with keys

``` bash
./kafka-console-producer.sh \
    --bootstrap-server localhost:29092 \
    --property "parse.key=true" \
    --property "key.separator=:" \
    --topic kafka.learning.orders
```

## Consume messages using a consumer group

``` bash
./kafka-console-consumer.sh \
    --bootstrap-server localhost:29092 \
    --topic kafka.learning.orders \
    --group test-consumer-group \
    --property print.key=true \
    --property key.separator=" = " \
    --from-beginning
```

## Check current status of offsets

``` bash
./kafka-consumer-groups.sh \
    --bootstrap-server localhost:29092 \
    --describe \
    --all-groups
```
