1. Копируем архив с Kafka в домашний каталог:
```bash
cp /opt/kafka_2.13-2.7.0.tgz ~/
```
В случае, если что-то пойдет не так, вы сможете удалить каталог полностью и взять чистый архив для того, чтобы начать практику с начала.

Если вы его случайно удалили, можно скачать архив по ссылке:
```bash
wget <https://archive.apache.org/dist/kafka/2.7.0/kafka_2.13-2.7.0.tgz>
```
Распакуем архив и перейдем в каталог с приложением:
```bash
tar -xzf kafka_2.13-2.7.0.tgz
cd kafka_2.13-2.7.0
```
2. Первым делом мы запускаем Zookeeper.
```bash
./bin/zookeeper-server-start.sh config/zookeeper.properties
```
3. Запускаем брокер Kafka
```bash
./bin/kafka-server-start.sh config/server.properties
```
4. Останавливаем брокер Kafka штатно
```bash
./bin/kafka-server-stop.sh config/server.properties
```
5. Проверяем, что отключение произошло контролируемо
```bash
cat ./logs/server.log | grep "Starting controlled shutdown"
```
6. Запускаем брокер Кафки
```bash
./bin/kafka-server-start.sh config/server.properties
```
7.Останавливаем брокер Kafka нештатно
```bash
kill **-TERM** $(ps ax | grep -i 'kafka\\.Kafka' | grep java | grep -v grep | awk '{print $1}')
```
8. Запускаем брокер Кафки
```bash
./bin/kafka-server-start.sh config/server.properties
```
9. Проверяем, что отключение было грязным, и брокеру пришлось восстанавливаться
```bash
cat ./logs/server.log | grep "no clean shutdown file was found"
```
10. Останавливаем вначале брокер, и потом Zookeeper.
```bash
./bin/kafka-server-stop.sh config/server.properties
./bin/zookeeper-server-stop.sh config/zookeeper.properties
```
