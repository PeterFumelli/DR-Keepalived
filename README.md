# Домашнее задание к занятию "`Disaster recovery и Keepalived`" - `Фумелли Петр`


### Задание 1

Cisco config ![alt text](https://github.com/PeterFumelli/DR-Keepalived/blob/main/img/cisco_conf.png)

Cisco pkt (https://github.com/PeterFumelli/DR-Keepalived/blob/main/pkt/hsrp_advanced_pf.pkt)


### Задание 2

bash-скрипт Master

```
vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 3

    authentication {
        auth_type PASS
        auth_pass 1234
    }

    virtual_ipaddress {
        192.168.1.100
    }

    track_script {
        check_web
    }
}

vrrp_script check_web {
    script "/etc/keepalived/check_web.sh"
    interval 3
    weight -10
}
```


bash-скрипт Backup

```
vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90
    advert_int 3

    authentication {
        auth_type PASS
        auth_pass 1234
    }

    virtual_ipaddress {
        192.168.1.100
    }

    track_script {
        check_web
    }
}

vrrp_script check_web {
    script "/etc/keepalived/check_web.sh"
    interval 3
    weight -10
}
```

конфигурационный файл keepalived

```
# !/bin/bash

PORT=80
URL="/var/www/html/index.html"

# Проверяем доступность порта

nc -z 127.0.0.1 $PORT
if [ $? -ne 0 ]; then
    exit 1
fi

# Проверяем наличие файла index.html

if [ ! -f "$URL" ]; then
    exit 1
fi

exit 0
```

stage before ![alt text](https://github.com/PeterFumelli/DR-Keepalived/blob/main/img/before1.png)

![alt text](https://github.com/PeterFumelli/DR-Keepalived/blob/main/img/before2.png)

stage after ![alt text](https://github.com/PeterFumelli/DR-Keepalived/blob/main/img/after.png)
