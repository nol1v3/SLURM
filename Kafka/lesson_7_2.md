Каждая наша нода теперь экспортирует метрики в формате Prometheus на порту 7071. Теперь давайте установим сам Prometheus на node-1.

2. Устанавливаем Prometheus:
```bash
yum install prometheus2-2.25.0 -y
```
3. Создаем конфигурационный файл, в котором скажем Prometheus собирать метрики со всех наших нод:
```bash
vim ~/kafka/kafka-metrics/prometheus-kafka.yml
<<Заменяем {{USERNAME}} на свой логин>>
```
Копируем исправленный файл в каталог с настройками:
```bash
cp -f ~/kafka/kafka-metrics/prometheus-kafka.yml /etc/prometheus/prometheus.yml
```
4. Запускаем Prometheus
```bash
systemctl start prometheus
systemctl status -l prometheus
```
5. Проверим, что мы можем достучаться до Prometheus
```bash
curl localhost:9090
```
6. Устанавливаем и запускаем Grafana 
```bash
yum install -y https://dl.grafana.com/oss/release/grafana-7.4.3-1.x86_64.rpm
systemctl start grafana-server
systemctl status -l grafana-server
```

Наконец-то мы готовы к тому, чтобы визуализировать метрики Кафки!

1. Открываем в браузере http://grafana.<ваш номер студента>.edu.slurm.io

Обратите внимание: так как у нас нет TLS-сертификата, для открытия URL вам потребуется открыть браузер в режиме Инкогнито.
Для входа используйте те же доступы, как и для входа на sbox - ваш номер студента и пароль от него.

2. Переходим в Configuration -> Data Sources:

http://grafana.<ваш номер студента>.edu.slurm.io/datasources

Добавляем здесь новый DataSource типа Prometheus, указываем URL http://localhost:9090 и Scrape interval равный 10s

3. Нажимаем Save & Test для сохранения нашего DataSource.

4. Переходим в "+" -> Import: http://grafana.<ваш номер студента>.edu.slurm.io/dashboard/import

Импортируем дашборд, копируя контент из файла https://gitlab.slurm.io/edu/kafka/-/blob/master/kafka-metrics/dashboard.json

Ура, мы видим метрики!

Пока наши дашборды выглядят достаточно пусто, ведь у нас нет никакого трафика. Давайте воспользуемся транзакционным приложением из урока 4 для его создания!

1. Заходим на любую ноду и клонируем репо и соберем тестового клиента
```bash
git clone https://gitlab.slurm.io/edu/kafka.git
cd kafka
mvn package
```
2. Создаем топики
```bash
export USER=<ваш номер студента>

/opt/kafka_2.13-2.7.0/bin/kafka-topics.sh --bootstrap-server node-1.$USER:9092 --topic transactions-input --replication-factor 3 --partitions 3 --create

/opt/kafka_2.13-2.7.0/bin/kafka-topics.sh --bootstrap-server node-1.$USER:9092 --topic transactions-output --replication-factor 3 --partitions 1 --create
```
3. Запускаем продюсера
```bash
java -cp test-clients/target/test-clients-1.0-SNAPSHOT-jar-with-dependencies.jar io.slurm.kafka.TestProducer -b node-1.$USER:9092 -c 20000000 -t transactions-input -i -s 100
```
4. Запускаем exactly-once консюмера
```
java -cp test-clients/target/test-clients-1.0-SNAPSHOT-jar-with-dependencies.jar io.slurm.kafka.ReadProcessWriteExactlyOnceApp -b node-1.$USER:9092 --group my-processor --id 1 --in transactions-input --out transactions-output
```
6. Через некоторое время мы увидим "ожившие" метрики трафика на нашем дашборде

- Попробуйте переконфигурировать дашборд, добавить в него дополнительные метрики
- Попробуйте остановить ноду Kafka и Zookeeper и посмотреть, как это отразится на метриках
