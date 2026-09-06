### Конфигурация на alt-srv4
```bash
# /etc/fail2ban/jail.d/10local.conf
[DEFAULT]
backend = systemd
banaction = iptables-multiport
bantime = 1h
findtime = 10m
maxretry = 4

# /etc/fail2ban/jail.d/20sshd.conf
[sshd]
enabled = true
port = ssh
filter = sshd
bantime = 4h
findtime = 10m
maxretry = 3
action = iptables-multiport[name=SSH, port="ssh", protocol=tcp]
