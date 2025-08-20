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
