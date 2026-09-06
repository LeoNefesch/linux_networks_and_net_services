## Настройка squid

### Цель
- На alt-srv2:
  - настроить Squid в непрозрачном режиме. Добиться попадания в кэш ОЗУ и кэш диска.

### Файл настроек
- `/etc/squid/squid.conf`

### Создание структуры каталогов для дискового кэша
```bash
squid -z
```

### Применение настроек
```bash
systemctl enable --now squid.service
systemctl start squid.service
```

## Тестирование squid
Создаём на alt-srv2 2 файла - небольшой (test.html) и большой (bigfile.bin, 5 Мб): 
```bash
cd /tmp
echo "test" > test.html

dd if=/dev/zero of=/tmp/bigfile.bin bs=1M count=5
````
и отдаём их через простой веб-сервер.
```bash
python3 -m http.server 8000 &
```
На клиенте (alt-ws1) запрашиваем файлы через прокси (повторить запросы несколько раз)
```bash
curl -x http://10.0.0.12:3128 http://10.0.0.12:8000/test.html -o /dev/null -s
curl -x http://10.0.0.12:3128 http://10.0.0.12:8000/bigfile.bin -o /dev/null -s
```

### Проверка логов:
```bash
tail -f /var/log/squid/access.log
```

### Оглавление
- config:
  - [alt-srv](configs/alt-srv2.md)
- screenshots:
  - [squid_conf_custom_params](screenshots/squid_conf_custom_params.png)
  - [squid_tcp_hit](screenshots/squid_tcp_hit.png)
  - [squid_tcp_mem_hit](screenshots/squid_tcp_mem_hit.png)
