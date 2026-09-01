### Конфигурация на alt-srv1
```bash
# /etc/net/ifaces/ens33/options
TYPE=eth
CONFIG_WIRELESS=no
BOOTPROTO=dhcp
SYSTEM_BOOTPROTO=dhcp4
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
ONBOOT=yes

# /etc/net/ifaces/ens34/options
TYPE=eth
CONFIG_WIRELESS=no
BOOTPROTO=static
SYSTEM_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
ONBOOT=yes

# /etc/net/ifaces/ens34/ipv4address
192.168.100.1/24

# /etc/net/ifaces/ens34/resolv.conf
nameserver 77.88.8.8
nameserver 8.8.8.8

# /etc/net/ifaces/ens35/options
TYPE=eth
CONFIG_WIRELESS=no
BOOTPROTO=static
SYSTEM_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
ONBOOT=yes

# /etc/net/ifaces/ens35/ipv4address
192.168.200.1/24

# /etc/net/ifaces/ens35/resolv.conf
nameserver 77.88.8.8
nameserver 8.8.8.8

# /etc/net/ifaces/ens36/options
TYPE=eth
CONFIG_WIRELESS=no
BOOTPROTO=static
SYSTEM_BOOTPROTO=static
CONFIG_IPV4=yes
DISABLED=no
NM_CONTROLLED=no
SYSTEMD_CONTROLLED=no
ONBOOT=yes

# /etc/net/ifaces/ens36/ipv4address
10.0.0.1/24

# /etc/net/ifaces/ens36/resolv.conf
nameserver 77.88.8.8
nameserver 8.8.8.8