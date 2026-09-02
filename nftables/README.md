## Настройка iptables

### Цель
- На alt-srv3:
  - настроить nftables для работы веб-сервера на портах 80 и 443;
  - разрешить доступ к веб-серверу только с IP-адресов из доверенной подсети 192.168.100.0/24;
  - все остальные попытки подключения к портам 80 и 443 должны блокироваться;
  - политика по умолчанию - запрет.

### Файл настроек
- `/etc/nftables/nftables.nft`

### Применение правил
```bash
nft -f /etc/nftables.conf
```

### Просмотр правил:
```bash
nftables list ruleset
```

### Оглавление
- iptables:
  - [alt-srv3](configs/alt-srv3.md)
- screenshots:
  - [alt-srv3](screenshots/nftables_22_80_443_ports_srv3.png)
