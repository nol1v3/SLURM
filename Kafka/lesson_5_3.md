1. Проверьте, что вы находитесь в каталоге с Kafka, а если нет - перейдите туда:
```bash
cd kafka_2.13-2.7.0
```
2. Запускаем первый Zookeeper.
```bash
cat << EOF > ./config/zookeeper1.properties
initLimit=5
syncLimit=2
dataDir=/tmp/zookeeper-1
clientPort=2181
server.1=localhost:2888:3888
server.2=localhost:2889:3889
EOF
```
```bash
mkdir /tmp/zookeeper-1 && echo 1 > /tmp/zookeeper-1/myid
```
```bash
./bin/zookeeper-server-start.sh ./config/zookeeper1.properties
```
```bash
./bin/zookeeper-server-start.sh ./config/zookeeper1.properties
```
3. Запускаем второй Zookeeper.
```bash
cat << EOF > ./config/zookeeper2.properties
initLimit=5
syncLimit=2
dataDir=/tmp/zookeeper-2
clientPort=2182
server.1=localhost:2888:3888
server.2=localhost:2889:3889
EOF
```
```bash
mkdir /tmp/zookeeper-2 && echo 2 > /tmp/zookeeper-2/myid
```
```bash
./bin/zookeeper-server-start.sh ./config/zookeeper2.properties
```
4. Запускаем брокер Kafka
```bash
rm -rf /tmp/kafka-logs
```
```bash
cp ./config/server.properties ./config/server1.properties
```
```bash
sed -i 's/zookeeper.connect=localhost:2181/zookeeper.connect=localhost:2181,localhost:2182/g' ./config/server1.properties
```
```bash
./bin/kafka-server-start.sh config/server1.properties
```
5. Создаем топик
```bash
./bin/kafka-topics.sh --create --topic registrations --partitions 1 --bootstrap-server localhost:9092
```
6. Проверяем, что топик создан
```bash
./bin/kafka-topics.sh --describe --topic registrations --bootstrap-server localhost:9092 | head -n1
```
7. Останавливаем один Zookeeper
```bash
kill -s ${SIGNAL:-TERM} $(ps -af | grep java | grep -i QuorumPeerMain | grep -v grep | grep zookeeper2.properties | awk '{print $2}' | head -n1)
```
8. Пытаемся создать новый топик и получаем ошибку ERROR org.apache.kafka.common.errors.TimeoutException (аналогичную ошибку мы наблюдали в Практике 2 с единственным Zookeeper)
```bash
./bin/kafka-topics.sh --create --topic stats --partitions 1 --bootstrap-server localhost:9092
```
9. Запустим Zookeeper (который был у вас остановлен) для удаления топика. После останавливаем брокер, и потом Zookeeper.
```bash
./bin/zookeeper-server-start.sh ./config/zookeeper2.properties

./bin/kafka-topics.sh --delete --topic registrations --bootstrap-server localhost:9092

./bin/kafka-server-stop.sh config/server1.properties

./bin/zookeeper-server-stop.sh ./config/zookeeper1.properties

./bin/zookeeper-server-stop.sh ./config/zookeeper2.properties
```
