1. Проверьте, что вы находитесь в каталоге с Kafka, а если нет - перейдите туда:
```bash
cd kafka_2.13-2.7.0
```
2. Первым делом, мы запускаем Zookeeper. 
```bash
./bin/zookeeper-server-start.sh config/zookeeper.properties
```
3. Запускаем брокер Кафки
```bash
./bin/kafka-server-start.sh config/server.properties
```
4. Создаем топик 
```bash
./bin/kafka-topics.sh --create --topic registrations --partitions 1 --bootstrap-server localhost:9092
```
5. Проверяем, что топик создан
```bash
./bin/kafka-topics.sh --describe --topic registrations --bootstrap-server localhost:9092 | head -n1
```
6. Останавливаем Zookeeper
```bash
./bin/zookeeper-server-stop.sh config/zookeeper.properties
```
7. Записываем данные в топик и после прерываем команду Ctrl-C.
```bash
./bin/kafka-console-producer.sh --topic registrations --bootstrap-server localhost:9092

>Hello Slurm!
```
8. Читаем данные из топика. Там будет  0 messages, это мы увидим, прервав команду Ctrl-C.
```bash
./bin/kafka-console-consumer.sh --topic registrations --bootstrap-server localhost:9092 --from-beginning
```
9. Пытаемся создать новый топик и получаем ошибку ERROR org.apache.kafka.common.errors.TimeoutException
```bash
./bin/kafka-topics.sh --create --topic stats --partitions 1 --bootstrap-server localhost:9092
```
10. Запустим Zookeeper для удаления топика. После, останавливаем брокер, и потом Zookeeper. 
```bash
./bin/zookeeper-server-start.sh config/zookeeper.properties

./bin/kafka-topics.sh --delete --topic registrations --bootstrap-server localhost:9092

./bin/kafka-server-stop.sh config/server.properties

./bin/zookeeper-server-stop.sh config/zookeeper.properties
```
Поздравляем вас! До встречи в следующей теме!
