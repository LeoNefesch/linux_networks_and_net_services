### Конфигурация на alt-srv4
```bash
# /etc/sysconfig/iptables
*filter
:INPUT DROP [0:0]
:FORWARD DROP [0:0]
:OUTPUT ACCEPT [375:30156]
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -s 192.168.100.0/24 -p tcp -m tcp --dport 80 -j ACCEPT
-A INPUT -s 192.168.100.0/24 -p tcp -m tcp --dport 443 -j ACCEPT
COMMIT
