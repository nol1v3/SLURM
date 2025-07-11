# Требования к стендам
Мы вплотную подобрались к практической части, которая позволит вам поработать с Kafka собственноручно и на практике закрепить понимание вами основных концепций и принципов работы этого ПО.

Мы можем предоставить вам стенд для выполнения практических заданий этой темы. Но требования к стенду совсем несложные, поэтому, если вам интересно пройти практику этой темы на вашем собственном компьютере, мы предлагаем системные требования и небольшую инструкцию.

# Требования к системным ресурсам: 

Как минимум 1 ядро CPU, 1 Gb RAM, 1 Gb disk. 

Однако современные ОС, как правило, требуют больше ресурсов просто для своего запуска и нормальной работы, поэтому скорее всего ресурсов у вас уже достаточно.

# Требования к ПО: 

64-битная ОС, Java Runtime Environment (JRE) 8 или 11.

1. Если у вас ОС Linux или MacOS (современных версий - не старше 5 лет), вы можете установить в систему пакет jre-headless (либо openjdk-11-jre-headless, названия пакетов у разных дистрибутивов могут отличаться) и делать практику в системном терминале. Других пакетов не понадобится, будут использоваться простые команды типа wget, tar, dd, встроенные в bash и т.д.

Проверить, что JRE установлена, и нужной версии, можно командой:
```bash
java –version
```
Получим примерно такой ответ:
```
openjdk 11.0.10 2021-01-19 LTS

OpenJDK Runtime Environment Zulu11.45+27-CA (build 11.0.10+9-LTS)

OpenJDK 64-Bit Server VM Zulu11.45+27-CA (build 11.0.10+9-LTS, mixed mode)
```
2. Если у вас относительно новый ПК, и установлен Windows 10 – вы можете установить себе WSL (Windows Subsystem for Linux), таким образом, вы получите терминал с почти полнофункциональной ОС Linux.

Вот здесь можно почитать, как это делается: https://docs.microsoft.com/ru-ru/windows/wsl/install-win10

3. В Windows 8/10 (и во многих других ОС, в том числе и Linux или MacOS), вы можете установить простой гипервизор Virtualbox (https://www.virtualbox.org/) и запустить образ с Linux.

Образы можно найти например вот тут: https://www.linuxvmimages.com/

Спикер использовал для записи CentOS 7, вот ссылка сразу на подходящий образ: https://sourceforge.net/projects/linuxvmimages/files/VirtualBox/C/7/CentOS_7.8.2003_VBM.zip/download 

Далее – смотрите пункт 1, потребуется доустановить пакет jre-headless и можно приступать к заданию.

4. Если у вас есть опыт работы с облачными провайдерами или другими гипервизорами, вы уже знаете что делать – создайте виртуальную машину с Linux и установите JRE 8 или 11.
