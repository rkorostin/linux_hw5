# Операционные системы и виртуализация (Linux) 
## Настройка сети в Linux. Работа с IPtables
### 1. Настроить статическую конфигурацию (без DHCP) в Ubuntu через ip и netplan. Настроить IP, маршрут по умолчанию и DNS-сервера (1.1.1.1 и 8.8.8.8). Проверить работоспособность сети.
 - sudo vim /etc/netplan/00-installer-config.yaml
``` 
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.1.82/24]
      routes:
        - to: 0.0.0.0/0
          via: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
```

 - sudo netplan try
``` 
Do you want to keep these settings?
Press ENTER before the timeout to accept the new configuration
Changes will revert in 110 seconds
```
 - sudo netplan apply
 - ping ya.ru
```
PING ya.ru (5.255.255.242) 56(84) bytes of data.
64 bytes from ya.ru (5.255.255.242): icmp_seq=1 ttl=249 time=5.28 ms
64 bytes from ya.ru (5.255.255.242): icmp_seq=2 ttl=249 time=6.24 ms
```
### 2. Настроить правила ufw для доступности сервисов на TCP-портах 22, 80 и 443. Остальные подключения запретить.
 - sudo apt install ufw
 - sudo ufw allow 22/tcp
 - sudo ufw allow 80/tcp
 - sudo ufw allow 443/tcp
 - sudo ufw default deny incoming
 - sudo ufw enable
### 3. Разрешить подключение по SSH только из сети 192.168.0.0/24.
 - sudo ufw status verbose
```
Состояние: активен
Журналирование: on (low)
По умолчанию: deny (входящие), allow (исходящие), disabled (маршрутизированные)
Новые профили: skip

В                          Действие    Из
-                          --------    --
22/tcp                     ALLOW IN    Anywhere
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
80/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)
```
 - sudo ufw delete 1
 - sudo ufw delete 3
 - sudo ufw allow from 192.168.0.0/24 to any port 22
### 4. * Запретить любой входящий трафик с IP 3.4.5.6.
 - sudo ufw deny from 3.4.5.6 to any
 - sudo ufw status verbose
```
Состояние: активен
Журналирование: on (low)
По умолчанию: deny (входящие), allow (исходящие), disabled (маршрутизированные)
Новые профили: skip

В                          Действие    Из
-                          --------    --
80/tcp                     ALLOW IN    Anywhere
443/tcp                    ALLOW IN    Anywhere
22                         ALLOW IN    192.168.0.0/24
Anywhere                   DENY IN     3.4.5.6
80/tcp (v6)                ALLOW IN    Anywhere (v6)
443/tcp (v6)               ALLOW IN    Anywhere (v6)
```


