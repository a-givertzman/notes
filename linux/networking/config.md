# Linux Networking

## Config

- configuration file path:  
`/etc/network/interfaces`

- view all network interfaces:  
`ip a`  
`i -4 a` - only ip v4  
`i -6 a` - only ip v6

- view interface eth0:

  ```bash
  ip a show eth0
  ip a list eth0
  ip a show dev eth0
  ```

- view UP - state only interfaces:  
`ip link ls up`

- add IP address to the interface eth0:

  ```bash
  ip a add {ip_addr/mask} dev {interface}  
  ip a add 192.168.1.200/24 dev eth0
  ip a add 192.168.1.200/255.255.255.0 dev eth0
  ```

- delete IP address from interface:

  ```bash
  ip a del {ipv6_addr_OR_ipv4_addr} dev {interface}
  ip a del 192.168.1.200/24 dev eth0
  ```

- set interface UP / DOWN:

  ```bash
  ip link set dev {DEVICE} {up|down}
  ip link set dev eth1 up
  ip link set dev eth1 down
  ```

- Как изменить txqueuelen устройства:  
Вы можете установить длину очереди передачи устройства с помощью ifconfig или команды ip следующим образом:  
`ip link set txqueuelen {NUMBER} dev {DEVICE}`  
изменим txqueuelen со значения по умолчанию, равного 1000, на 10000 для интерфейса 10000:  
`ip link set txqueuelen 10000 dev eth0`
ip a list eth0

- Как изменить MTU устройства:  
Для гигабиных сетей вы можете установить максимальный размер блоков передачи (MTU) для лучшей производительности сети. Синтаксис следующий:  
`ip link set mtu {NUMBER} dev {DEVICE}`  
Например, установим MTU устройства eth0 на 9000:  
`ip link set mtu 9000 dev eth0`  
`ip a list eth0`

- Показать соседей (arp кэш):  
Синтаксис:  
`ip n show`  
`ip neigh show`  
Пример вывода (часть информации скрыта):  

  ```bash
  74.xx.yy.zz dev eth1 lladdr 00:30:48:yy:zz:ww REACHABLE  
  10.10.29.66 dev eth0 lladdr 00:30:48:c6:0a:d8 REACHABLE  
  74.ww.yyy.xxx dev eth1 lladdr 00:1a:30:yy:zz:ww REACHABLE  
  10.10.29.68 dev eth0 lladdr 00:30:48:33:bc:32 REACHABLE  
  74.fff.uu.cc dev eth1 lladdr 00:30:48:yy:zz:ww STALE  
  74.rr.ww.fff dev eth1 lladdr 00:30:48:yy:zz:ww DELAY  
  10.10.29.65 dev eth0 lladdr 00:1a:30:38:a8:00 REACHABLE  
  10.10.29.74 dev eth0 lladdr 00:30:48:8e:31:ac REACHABLE  
  ```

  Последнее поле показывает состояние системы "определения недоступности соседа":  
    STALE - сосед существует, но, скорее всего недоступен, поэтому ядро попробует проверить это при первой ближайшей передаче.  
    DELAY - пакет был отправлен соседу в состоянии STALE и ядро ждет подтверждения.  
    REACHABLE - сосед существует и определенно доступен.

- Добавить новую ARP запись:  
Синтаксис:  
`ip neigh add {IP-HERE} lladdr {MAC/LLADDRESS} dev {DEVICE} nud {STATE}`  
Например, добавить постоянную ARP запись для соседа 192.168.1.5 на устройстве eth0:  
`ip neigh add 192.168.1.5 lladdr 00:1a:30:38:a8:00 dev eth0 nud perm`  
Где:

  | Состояние соседа  |                   Значение                        |
  |----------------|--------------------------------------------------|
  | permanent      | Состояние соседа занято навсегда и может быть удалено только администратором.|
  |noarp | Состояние соседа валидно. Попыток проверить состояние не будет, но может быть удалено, когда закончится время жизни.
  stale| Состояние соседа валидно, но подозрительно. Эта опция для ip neigh не меняет состояние соседа, если оно было валидно и адрес не был изменен этой командой.
  reachable| Состояние соседа валидно до окончания таймаута доступности.

- Удалить ARP запись:  
Синтаксис:  
`ip neigh del {IPAddress} dev {DEVICE}`  
Удалить запись для соседа 192.168.1.5 на интерфейсе eth1:  
`ip neigh del 192.168.1.5 dev eth1`  
Изменить состояние на reachable для соседа 192.168.1.100 на устройстве eth1:  
`ip neigh chg 192.168.1.100 dev eth1 nud reachable`  

- Очистить ARP запись
Команда flush или f очищает таблицы соседей или arp таблицы. Синтаксис:  
`ip -s -s n f {IPAddress}`  
Например:  
`ip -s -s n f 192.168.1.5`  
или  
`ip -s -s n flush 192.168.1.5`

- ip route: команды управления таблицей маршрутизации
Используйте следующие команды для управления таблицей маршрутизации ядра.

- Показать таблицу маршрутизации:  

  ```bash
  ip r
  ip r list
  ip route
  ```

  Пример вывода:
  
  ```bash
  default via 192.168.1.254 dev eth1
  192.168.1.0/24 dev eth1 proto kernel scope link src 192.168.1.10
  ```

- Показать роутинг для 192.168.1.0/24:  
`ip r list 192.168.1.0/24`  
Пример вывода:  
`192.168.1.0/24 dev eth1  proto kernel  scope link  src 192.168.1.10`

- Добавить новый маршрут:
Синтаксис:  

  ```bash
  ip route add {NETWORK/MASK} via {GATEWAYIP}
  ip route add {NETWORK/MASK} dev {DEVICE}
  ip route add default {NETWORK/MASK} dev {DEVICE}
  ip route add default {NETWORK/MASK} via {GATEWAYIP}
  ```

  Добавить статический маршрут в сеть 192.168.1.0/24 через шлюз 192.168.1.254:  
  `ip route add 192.168.1.0/24 via 192.168.1.254`  
  Чтобы маршрутизировать весь трафик через шлюз 192.168.1.254 подключенный через сетевой интерфейс eth0:  
  `ip route add 192.168.1.0/24 dev eth0`

- Удалить маршрут:  
Команда чтобы удалить маршрут по умолчанию:  
`ip route del default`  
В этом примере удалим маршрут созданный в предыдущем блоке:  
`ip route del 192.168.1.0/24 dev eth0`
