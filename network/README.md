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
