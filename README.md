Аварийное восстановление и Keepalived Брюхов А SYS-26

Решение 1

Решение 2

1. Установка и настройка Keepalived

    Установите Keepalived на обе виртуальные машины:

sudo apt-get update
sudo apt-get install keepalived

2.Создал конфигурационный файл для Keepalived на обеих машинах. Пример:

На первой машине (primary): /etc/keepalived/keepalived.conf

vrrp_instance VI_1 {
        state MASTER
        interface ens33
        virtual_router_id 15
        priority 255
        advert_int 1

        virtual_ipaddress {
              192.168.6.15/24
        }

}


На второй машине (backup): /etc/keepalived/keepalived.conf

vrrp_instance VI_1 {
        state BACKUP
        interface ens33
        virtual_router_id 15
        priority 200
        advert_int 1

        virtual_ipaddress {
              192.168.6.15/24
        }

}

Запуск сервиса Keepalived на обеих машинах:

    sudo systemctl start keepalived

3. Установка веб-сервера

sudo apt-get install nginx

4.Создал Bash-скрипт для проверки доступности порта и наличия файла index.html. Например, /path/to/your/script.sh:

#!/bin/bash

nc -z -w3 localhost 80
PORT_STATUS=$?

if [ $PORT_STATUS -eq 0 ]; then
    curl -s localhost | grep -q "index.html"
    FILE_STATUS=$?

    if [ $FILE_STATUS -eq 0 ]; then
        exit 0
    else
        exit 1
    fi
else
    exit 1
fi

Сделал скрипт исполняемым:

    chmod +x /path/to/your/script.sh

