# Зачем необходим мониторинг Kafka:

Отслеживание поведения системы в реальном времени;
Трекинг изменений в поведении с течением времени;
Заблаговременное планирование ресурсов.

# Что необходимо мониторить:

Собирать метрики Apache Kafka и Apache Zookeeper;
Собирать метрики JVM;
Собирать метрики хоста системы (загрузки памяти, ЦПУ сетевого стека, диска).

# Мониторинг Kafka - основные тезисы:

Кафка репортит несколько сотен метрик из коробки;
По умолчанию, метрики репортятся через JMX;
Можно подключать кастомные репортеры, изменяя настройку metric.reporters.

# Варианты архитектуры (несколько способов сбора и отображения метрик из Kafka):

JmxTrans + Graphite/StatsD/TSDB: https://github.com/jmxtrans/jmxtrans
Prometheus & JMX Exporter: https://github.com/prometheus/jmx_exporter
Custom Reporter

# Статьи о проблемах с EDR:

https://medium.com/expedia-group-tech/your-latency-metrics-could-be-misleading-you-how-hdrhistogram-can-help-9d545b598374
https://engineering.salesforce.com/be-careful-with-reservoirs-708884018daf

Ключевые метрики Kafka (на основе 4 Golden Signals: https://sre.google/sre-book/monitoring-distributed-systems/#xref_monitoring_golden-signals):
