## Настройка сетевых интерфейсов с помощью etcnet

### Цель
- Настроить IP-адреса на всех ВМ согласно схеме, используя конфигурацию в `/etc/net/ifaces/`.
- Обеспечить межсетевое взаимодействие виртуальных машин. При этом виртуальная машина alt-srv1 выступает в роли маршрутизатора и шлюза по умолчанию. 

### Используемые файлы
- `/etc/net/ifaces/ens33/options`
- `/etc/net/ifaces/ens34/options`
- `/etc/net/ifaces/ens34/ipv4address`
- `/etc/net/ifaces/ens34/resolv.conf`
- `/etc/net/ifaces/ens35/options`
- `/etc/net/ifaces/ens35/ipv4address`
- `/etc/net/ifaces/ens35/resolv.conf`
- `/etc/net/ifaces/ens36/options`
- `/etc/net/ifaces/ens36/ipv4address`
- `/etc/net/ifaces/ens36/resolv.conf`

### Оглавление
- network:
  - [alt-srv1](configs/alt-srv1.md)
  - [alt-srv2](configs/alt-srv2.md)
  - [alt-srv3](configs/alt-srv3.md)
  - [alt-srv4](configs/alt-srv4.md)
  - [alt-ws1](configs/alt-ws1.md)
  - [alt-ws2](configs/alt-ws2.md)
- screenshots:
  - [alt-srv1_1](screenshots/alt-srv1_1.png)
  - [alt-srv1_2](screenshots/alt-srv1_2.png)
  - [alt-srv2](screenshots/alt-srv2.png)
  - [alt-srv3](screenshots/alt-srv3.png)
  - [alt-srv4](screenshots/alt-srv4.png)
  - [alt-ws1](screenshots/alt-ws1.png)
  - [alt-ws2](screenshots/alt-ws2.png)
  - [network_editor](screenshots/network_editor.png)