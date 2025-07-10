# Дополнение к модулю: Debezium
Изучение Debezium является важным для тех, кто изучает Apache Kafka, поскольку Debezium предоставляет интеграцию с базами данных. Debezium является надстройкой над Apache Kafka и предоставляет специализированные коннекторы для различных баз данных, таких как MySQL, PostgreSQL, Oracle и других. Эти коннекторы позволяют легко и надежно захватывать изменения данных из баз данных и передавать их в Apache Kafka.

Debezium обеспечивает строгую контрольную точку данных, что позволяет повторно создать состояние базы данных в любой момент времени. Это очень полезно при разработке микросервисных систем, где несколько сервисов могут использовать одну базу данных. С помощью Debezium каждый сервис может отслеживать изменения данных, которые ему интересны, и получать только необходимые обновления данных. Это значительно упрощает разработку и обеспечивает целостность данных между разными сервисами.

Мы предлагаем прочитать вам статью для более подробного изучения https://debezium.io/documentation/reference/1.9/connectors/postgresql.html

После изучения статьи, вы можете попробовать развернуть у себя в докере коннект к бд(PostgreSql), используя шаги и конфиг описанный ниже:

1. Установите и настройте Debezium для работы с PostgreSQL. Это можно сделать, следуя инструкциям на официальном сайте Debezium.

2. Создайте конфигурационный файл для Debezium, который будет указывать, какие таблицы и поля из PostgreSQL нужно отслеживать и отправлять в Kafka.

Пример такого файла:
```
name: postgres-connector

connector.class: io.debezium.connector.postgresql.PostgresConnector

tasks.max: 1

database.hostname: postgres

database.port: 5432

database.user: postgres

database.password: mysecretpassword

database.dbname: mydb

database.server.name: mydb

table.include.list: public.mytable
```
3. Запустите Debezium, указав путь к конфигурационному файлу:
```bash
./bin/connect-standalone.sh ./etc/kafka/connect-standalone.properties

./path/to/postgres-connector-config.json
```
4. После запуска Debezium начнет отслеживать изменения в таблице mytable базы данных mydb и отправлять их в Kafka топик slurm.

5. Можете настроить консьюмер Kafka для чтения сообщений из топика slurm и обработки их в соответствии с требованиями проекта:

Пример настройки консьюмера Debezium Kafka для чтения данных из топика "slurm" и добавления их в таблицу "mydb" в PostgreSQL базе данных:
```
{

  "name": "my-slurm-connector",

  "config": {

    "connector.class": "io.debezium.connector.postgresql.PostgresConnector",

    "database.hostname": "localhost",

    "database.port": "5432",

    "database.user": "myuser",

    "database.password": "mypassword",

    "database.dbname": "mydb",

    "database.server.name": "mydb",

    "schema.whitelist": "public",

    "table.whitelist": "public.slurm",

    "database.history.kafka.bootstrap.servers": "localhost:9092",

    "database.history.kafka.topic": "my-slurm-connector-history"

  }

}
```
Укажите, что коннектор должен использовать класс PostgresConnector для чтения данных из PostgreSQL базы данных. Укажите имя хоста, порт, имя пользователя и пароль для подключения к базе данных.

Свойство "database.dbname" указывает имя базы данных, которую мы хотим использовать.

Свойство "schema.whitelist" указывает, какие схемы мы хотим синхронизировать. В данном случае мы выбрали только схему "public".

Свойство "table.whitelist" указывает, какие таблицы мы хотим синхронизировать. В данном случае мы выбрали только таблицу "slurm".

Свойство "database.history.kafka.bootstrap.servers" указывает адрес Kafka-брокера, который будет использоваться для хранения истории базы данных.

И, наконец, свойство "database.history.kafka.topic" указывает имя топика Kafka, который будет использоваться для хранения истории базы данных.

 

Debezium позволяет улучшить взаимодействие между Apache Kafka и базами данных, а также обеспечивает более надежную и гибкую интеграцию в микросервисных архитектурах. Это способствует более эффективному обмену данными и обеспечивает целостность и консистентность данных между различными компонентами системы.
