# Запуск кластера из одной ноды
Если вы работаете на нашем стенде, архив с кафкой мы уже скачали заранее :)

Копируем архив в домашний каталог:
```bash
cp /opt/kafka_2.13-2.7.0.tgz ~/
```
В случае, если что-то пойдет не так, вы сможете удалить каталог с кафкой полностью и взять чистый архив для того, чтобы начать практику с начала. Если вы его случайно удалили, можно скачать архив по ссылке:
```bash
wget https://archive.apache.org/dist/kafka/2.7.0/kafka_2.13-2.7.0.tgz
```
Распакуем архив и перейдем в каталог с приложением:
```bash
tar -xzf kafka_2.13-2.7.0.tgz
cd kafka_2.13-2.7.0
```
Первым делом мы запускаем Zookeeper. Как мы уже обсуждали, Кафка пользуется зукипером для хранения метаданных, а также для координации своей работы (выбора лидеров партиций и контроллера).
```bash
./bin/zookeeper-server-start.sh config/zookeeper.properties
```
Запускаем брокер Кафки
```bash
./bin/kafka-server-start.sh config/server.properties
```

# Запись и чтение сообщений
Создаем топик с регистрациями
```bash
./bin/kafka-topics.sh --create --topic registrations --bootstrap-server localhost:9092
```
Посмотрим на его конфигурацию
```bash
./bin/kafka-topics.sh --describe --topic registrations --bootstrap-server localhost:9092
```
Давайте запишем первое сообщение
```bash
./bin/kafka-console-producer.sh --topic registrations --bootstrap-server localhost:9092
>Hello world!
>Hello Slurm!
```
Попробуем его прочитать
```bash
./bin/kafka-console-consumer.sh --topic registrations --bootstrap-server localhost:9092
```
И… ничего не происходит!

В эту ситуацию попадают многие люди, впервые использующие кафку. Все дело в том, что консьюмер Kafka по умолчанию начинает читать данные с конца топика (см. настройку auto.offset.reset). Для того чтобы прочитать данные с начала, мы должны переопределить эту конфигу.

```bash
./bin/kafka-console-consumer.sh --topic registrations --bootstrap-server localhost:9092 --consumer-property auto.offset.reset=earliest
```
Или можно сказать “--from-beginning”. Ура, мы произвели запись и чтение! Запишем еще одно сообщение — увидим, что оно появляется в консьюмере. Обратим внимание на лог и увидим, что каждый запуск консольного консьюмера создает новую группу.

Давайте вместо этого зададим свою:
```bash
./bin/kafka-console-consumer.sh --topic registrations --group slurm --bootstrap-server localhost:9092 --consumer-property auto.offset.reset=earliest
```
Видим сообщения, все ок, а теперь давайте перезапустим... Снова ничего! Вспомним из прошлой лекции, что консьюмер группы в Кафке могут коммитить свои оффсеты для топик-партиции, чтобы при перезапуске продолжать чтение с последней запомненной позиции. Именно это поведение мы и видим. Давайте проверим.

```bash
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group slurm --describe
```
А теперь сбросим позицию обратно на начало
```bash
./bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group slurm --to-earliest --reset-offsets --execute --topic registrations
```
При новом запуске консьюмера снова давайте отключим автоматический коммит оффсетов
```bash
./bin/kafka-console-consumer.sh --topic registrations --bootstrap-server localhost:9092 --group slurm --consumer-property auto.offset.reset=earliest --consumer-property enable.auto.commit=false
```
Если запустим консьюмера снова, то увидим, что сообщения читаются. Этим мы можем подтвердить, что оффсеты не коммитятся (+ оставим запущенным консьюмера, чтобы увидеть идентификатор и адрес).
