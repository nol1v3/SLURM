# Информация о кластере Kafka в ZooKeeper

Под конец урока давайте заглянем в ZooKeeper, и посмотрим, какую информацию он хранит.

Откроем консоль zookeeper:
```bash
./bin/zookeeper-shell.sh localhost:2181
```
И уже там можем посмотреть, что у нас есть:
```bash
ls /

get /controller

get /brokers/topics/registrations/partitions/0/state

stat /brokers/ids/0
```
