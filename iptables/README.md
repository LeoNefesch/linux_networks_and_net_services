## Настройка iptables

### Цель
- На alt-srv1:
  - настроить iptables для использования его как маршрутизатора и шлюза.
- На alt-srv4:
  - настроить iptables для работы веб-сервера на портах 80 и 443;
  - разрешить доступ к веб-серверу только с IP-адресов из доверенной подсети 192.168.100.0/24;
  - все остальные попытки подключения к портам 80 и 443 должны блокироваться;
  - трафик на порт SSH разрешён.

### Файл настроек(на обеих машинах)
- `/etc/sysconfig/iptables`

### Просмотр правил:
```bash
iptables -L
iptables -L -t nat
```

### Оглавление
- iptables:
  - [alt-srv1](configs/alt-srv1.md)
  - [alt-srv4](configs/alt-srv4.md)
- screenshots:
  - [alt-srv4](screenshots/iptables_22_80_443_ports_srv4.png)
