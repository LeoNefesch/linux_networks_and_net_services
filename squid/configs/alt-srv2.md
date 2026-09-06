### Конфигурация на alt-srv2 (кастомные настройки)
```bash
# /etc/fail2ban/jail.d/10local.conf
http_access allow localhost
http_access allow localnet
http_access deny all
cache allow all
http_port 3128

cache_mem 256 MB
cache_dir ufs /var/spool/squid 10000 16256
maximum_object_size_in_memory 128 KB
memory_replacement_policy heap GDSF
minimum_object_size 0 KB
maximum_object_size 100 MB
cache_replacement_policy heap LFUDA 

coredump_dir /var/spool/squid

refresh_pattern . 0 20% 43200 override-expire override-lastmod reload-into-ims ignore-reload ignore-no-cache ignore-private ignore-auth
refresh_pattern . 0 20% 4320
