# Mirror Maker
Mirror Maker - это инструмент в экосистеме Apache Kafka, который используется для репликации данных между кластерами Kafka. Он позволяет создавать отказоустойчивые и масштабируемые системы, а также обеспечивает резервное копирование данных и возможность работы с удаленными кластерами. Это помогает улучшить надежность и производительность при использовании Kafka в различных сценариях.

Приступим:

1. Создаем необходимое окружение (зукипер, 2 брокера, mirror maker)
```bash
version: '3'
services:
  zookeeper-source:
    image: confluentinc/cp-zookeeper:4.0.0
    hostname: zookeeper-source
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka-source:
    image: confluentinc/cp-kafka:4.0.0
    hostname: kafka-source
    depends_on:
      - zookeeper-source
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper-source:2181'
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka-source:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  zookeeper-target:
    image: confluentinc/cp-zookeeper:4.0.0
    hostname: zookeeper-target
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka-target:
    image: confluentinc/cp-kafka:4.0.0
    hostname: kafka-target
    depends_on:
      - zookeeper-target
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: 'zookeeper-target:2181'
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka-target:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  topic-creation:
    image: confluentinc/cp-kafka:4.0.0
    command: bash -c "cub kafka-ready -z zookeeper-source:2181 1 30 && kafka-topics --zookeeper zookeeper-source:2181 --create --topic slurm-to-mirror --partitions 10 --replication-factor 1"
    depends_on:
      - zookeeper-source

  dummy-generation:
    image: confluentinc/cp-kafka:4.0.0
    command: bash -c "cub kafka-ready -z zookeeper-source:2181 1 30 && sleep 5 && seq 10000 | kafka-console-producer --broker-list kafka-source:9092 --topic slurm-to-mirror"
    depends_on:
      - zookeeper-source
      - kafka-source

  mirror-maker:
    image: confluentinc/cp-kafka:4.0.0
    volumes:
      - ./consumer.cfg:/etc/consumer.cfg
      - ./producer.cfg:/etc/producer.cfg
    command: bash -c "cub kafka-ready -z zookeeper-source:2181 1 30 && cub kafka-ready -z zookeeper-target:2181 1 30 && kafka-mirror-maker --consumer.config /etc/consumer.cfg --producer.config /etc/producer.cfg --whitelist slurm-to-mirror --num.streams 1"
    depends_on:
      - kafka-source
      - kafka-target
      - zookeeper-target
```

2. Необходимы 2 файла
producer.cfg - здесь вы описываете свойства продюсера:
```bash
bootstrap.servers=zookeeper-target:9092
acks=-1
batch.size=1
client.id=mirror_maker_producer
retries=2147483647
max.in.flight.requests.per.connection=1
```
consumer.cfg - здесь  вы описываете свойства консьюмера
```bash
bootstrap.servers=kafka-source:9092
exclude.internal.topics=true
group.id=mirror-maker
auto.offset.reset=earliest
partition.assignment.strategy=org.apache.kafka.clients.consumer.RoundRobinAssignor
```
3. Поднимите  окружение в докер-композе:
```bash
docker-compose up -d
```
4. Получаем список топиков в source зукипере:
```bash
docker-compose exec kafka-source kafka-topics --zookeeper zookeeper-source:2181 --list
```
В результате должны получить: slurm-to-mirror
5. Слушаем топик консольный консьюмером:
```bash
docker-compose exec kafka-source kafka-console-consumer --bootstrap-server localhost:9092 --topic slurm-to-mirror --from-beginning
```
В результате должны получить: 9997 9998 9999 10000
6. Повторите для другого брокера kafka:
```bash
docker-compose exec kafka-target kafka-topics --zookeeper zookeeper-target:2181 --list
```
7. Введите:
```bash
docker-compose exec kafka-target kafka-console-consumer --bootstrap-server localhost:9092 --topic slurm-to-mirror --from-beginning
```
Вы получили тот же результат, что и на source - kafka брокере. Далее, чтобы закрепить результат на source-брокере поднимите консольного продюсера, отправьте с помощью него сообщение, посмотрите - будет ли оно доставлено с помощью mirror-maker’a на другой экземпляр kafka.
8. Запустите продюсер
```bash
docker-compose exec kafka-source kafka-console-producer --broker-list localhost:9092 --topic slurm-to-mirror
>asd
```
После записи появилось и для топика в targer- kafka -> mirror-maker отработал на отлично
