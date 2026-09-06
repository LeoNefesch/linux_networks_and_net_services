## Настройка fail2ban

### Цель
- На alt-srv4:
  - создать защиту от брутфорс-атаки для SSH (автоматическая блокировка на 4 часа).

### Файлы настроек
- `/etc/fail2ban/jail.d/10local.conf`
- `/etc/fail2ban/jail.d/20sshd.conf`

### Применение настроек
```bash
systemctl enable --now fail2ban.service
systemctl start fail2ban.service
```

### Просмотр правил (если используется iptables):
```bash
iptables -L f2b-SSH -vn
```

### Оглавление
- config:
  - [alt-srv4](configs/alt-srv4.md)
- screenshots:
  - [alt-srv4](screenshots/f2b-SSH.png)
