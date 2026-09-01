### Конфигурация на alt-ws1
```bash
# /etc/net/ifaces/ens33/options
TYPE=eth
CONFIG_WIRELESS=no
BOOTPROTO=static
SYSTEM_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
ONBOOT=yes
DHCP_TIMEOUT=7

# /etc/net/ifaces/ens33/ipv4address
192.168.100.101/24

# /etc/net/ifaces/ens33/ipv4route
default via 192.168.100.1

# /etc/net/ifaces/ens33/resolv.conf
nameserver 77.88.8.8
nameserver 8.8.8.8
