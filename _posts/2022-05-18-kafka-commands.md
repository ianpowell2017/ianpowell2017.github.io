---
title: Kafka notes
author: ianpowell
date: 2022-05-18 12:34:00 +0100
categories: [kafka]
tags: [c#]
---

## Kafka

### Scripts directory

``` bash
/opt/bitnami/kafka/bin
```

### Logging into the Kafka Container

``` bash
docker exec -it kafka-broker /bin/bash
```

### Navigate to the Kafka Scripts directory

``` bash
cd /opt/bitnami/kafka/bin
```

### Creating new Topics

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

### Listing Topics

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --list
```

### Getting details about a Topic

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --describe
```

### Publishing Messages to Topics

``` bash
./kafka-console-producer.sh \
    --bootstrap-server localhost:9092 \
    --topic kafka.learning.tweets
```

### Consuming Messages from Topics

``` bash
./kafka-console-consumer.sh \
    --bootstrap-server localhost:29092 \
    --topic kafka.learning.tweets \
    --from-beginning
```

### Deleting Topics

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:29092 \
    --delete \
    --topic kafka.learning.alerts
```


### Create a Topic with multiple partitions

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --create \
    --topic kafka.learning.orders \
    --partitions 3 \
    --replication-factor 1
```

### Check topic partitioning

``` bash
./kafka-topics.sh \
    --bootstrap-server localhost:9092 \
    --topic kafka.learning.orders \
    --describe
```

### Publishing Messages to Topics with keys

``` bash
./kafka-console-producer.sh \
    --bootstrap-server localhost:29092 \
    --property "parse.key=true" \
    --property "key.separator=:" \
    --topic kafka.learning.orders
```

### Consume messages using a consumer group

``` bash
./kafka-console-consumer.sh \
    --bootstrap-server localhost:29092 \
    --topic kafka.learning.orders \
    --group test-consumer-group \
    --property print.key=true \
    --property key.separator=" = " \
    --from-beginning
```

### Check current status of offsets

``` bash
./kafka-consumer-groups.sh \
    --bootstrap-server localhost:29092 \
    --describe \
    --all-groups
```
## Dockerfile

``` dockerfile
version: '2'
services:

#Zookeeper Service.
  zookeeper:
    image: 'bitnami/zookeeper:latest'
    restart: "no"
    ports:
      - '2181:2181'
    volumes:
      - "zookeeper_data:/bitnami"
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes
    container_name: zookeeper

#Kafka Service
  kafka:
    image: 'bitnami/kafka:latest'
    restart: "no"
    ports:
      - '9092:9092'
      - '29092:29092'
    volumes:
      - "kafka_data:/bitnami"
    environment:
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=INTERNAL:PLAINTEXT,EXTERNAL:PLAINTEXT
      - KAFKA_CFG_LISTENERS=INTERNAL://:29092,EXTERNAL://:9092
      - KAFKA_CFG_ADVERTISED_LISTENERS=INTERNAL://kafka:29092,EXTERNAL://docker-pve.localdomain:9092
      - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_INTER_BROKER_LISTENER_NAME=INTERNAL
      - ALLOW_PLAINTEXT_LISTENER=yes
      
    container_name: kafka-broker
    
    depends_on:
      - "zookeeper"
    
volumes:
  zookeeper_data:
    driver: local
  kafka_data:
    driver: local
```

## Errors

### Connection problems

- `thrd:localhost:9092/1001]: localhost:9092/1001: Connect to ipv4#127.0.0.1:9092 failed: Unknown error (after 2047ms in state CONNECT)`

- The advertised broker address is set to localhost, needs docker host DNS address
- Update this line to contain docker address instead, example, `KAFKA_CFG_ADVERTISED_LISTENERS=INTERNAL://kafka:29092,EXTERNAL://docker-pve.localdomain:9092`

## C# Sample code

``` powershell 
Install-Package 'Confluent.Kafka'
```

``` c#
public static async Task PublishAsync()
{
    var config = new ProducerConfig
    {
        BootstrapServers = "docker-pve.localdomain:9092",
        ClientId = Dns.GetHostName()
    };


    using (var producer = new ProducerBuilder<Null, string>(config).Build())
    {
        for (var i = 0; i < 2000; i++)
        {
            await producer.ProduceAsync("weblogs", new Message<Null, string> { Value = $"hello world - {i}" }).ContinueWith(task =>
            {
                if (task.IsFaulted)
                {

                }
                else
                {

                    Console.WriteLine($"Wrote to offset: {task.Result.Offset}");
                }
            });
        }
    }
}

public static void ConsumeAsync()
{
    var config = new ConsumerConfig
    {
        BootstrapServers = "docker-pve.localdomain:9092",
        GroupId = "foo",
        AutoOffsetReset = AutoOffsetReset.Earliest
    };

    bool cancelled = false;
    using (var consumer = new ConsumerBuilder<Ignore, string>(config).Build())
    {
        consumer.Subscribe("weblogs");

        while (!cancelled)
        {
            var result = consumer.Consume(100);

            if (result != null)
            {
                var msg = result.Message.Value;
                Console.WriteLine(msg);
            }

        }

        consumer.Close();
    }
}
```
