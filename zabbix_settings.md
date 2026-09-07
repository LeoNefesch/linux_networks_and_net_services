# Руководство по развертыванию Zabbix на ALT Linux
## 1.Установите PHP-FPM и модули:
```bash
apt-get update
apt-get install zabbix-preinstall
apt-get install zabbix-phpfrontend-nginx-php8.3-fpm-fcgi php8.3-pgsql
```
Запустите PHP-FPM:
```bash
systemctl enable --now php8.3-fpm
systemctl start php8.3-fpm
```
Добавьте пользователя nginx в группу _webserver (для доступа к сокету):
```bash
usermod -a -G _webserver nginx
```
## 2. Настройка веб-сервера Nginx
Создайте конфигурационный файл /etc/nginx/sites-available.d/zabbix.conf:
```bash
server {
    listen 80;
    server_name <IP-адрес_сервера>;

    root /var/www/webapps/zabbix/ui;
    index index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php8.3-fpm/php8.3-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_buffering off;
    }

    gzip off;
}
```
Активируйте конфигурацию:
```bash
ln -s /etc/nginx/sites-available.d/zabbix.conf /etc/nginx/sites-enabled.d/zabbix.conf
```

Настройте PHP:
Отредактируйте /etc/php/8.3/fpm/php.ini:
```ini
max_execution_time = 300
max_input_time = 300
memory_limit = 256M
display_errors = Off
```

## 3. Установка и настройка PostgreSQL
```bash
apt-get install postgresql17 postgresql17-contrib
```
Инициализируйте базу данных:
```bash
/etc/init.d/postgresql initdb
```
Запустите PostgreSQL:
```bash
systemctl enable --now postgresql
systemctl start postgresql
```
Создайте пользователя и базу данных для Zabbix:
```bash
su - postgres -s /bin/sh -c 'createuser --no-superuser --no-createdb --no-createrole --encrypted --pwprompt zabbix'
# Введите пароль (рекомендуется "zabbix")

su - postgres -s /bin/sh -c 'createdb -O zabbix zabbix'
```
Импортируйте схему базы данных:
```bash
su - postgres -s /bin/sh -c 'psql -U zabbix -f /usr/share/doc/zabbix-common-database-pgsql-*/schema.sql zabbix'
su - postgres -s /bin/sh -c 'psql -U zabbix -f /usr/share/doc/zabbix-common-database-pgsql-*/images.sql zabbix'
su - postgres -s /bin/sh -c 'psql -U zabbix -f /usr/share/doc/zabbix-common-database-pgsql-*/data.sql zabbix'
```

## 4. Установка и настройка Zabbix-сервера
Установите пакеты:
```bash
apt-get install zabbix-server-pgsql
```

Настройте `/etc/zabbix/zabbix_server.conf`:
```ini
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=zabbix
```


**Запустите Zabbix-сервер:**
```bash
systemctl enable --now zabbix_pgsql
systemctl start zabbix_pgsql
```
Обратите внимание: служба называется `zabbix_pgsql`, а не `zabbix-server`!

## 5. Настройка веб-интерфейса
Скопируйте пример конфигурационного файла:
```bash
cd /var/www/webapps/zabbix/ui/conf
cp zabbix.conf.php.example zabbix.conf.php
```
Отредактируйте zabbix.conf.php, указав параметры подключения к БД.

Перезапустите службы:
```bash
nginx -t
systemctl restart nginx
systemctl restart php8.3-fpm
```
## 6. Завершение установки
Откройте в браузере http://<IP-адрес_alt-srv1>/setup.php и следуйте инструкциям мастера установки.
После завершения установки войдите в систему:

- Логин: Admin
- Пароль: zabbix

## 7. Установка и настройка Zabbix-агентов
На каждом хосте выполните:
```bash
apt-get install zabbix-agent
```

Настройте `/etc/zabbix/zabbix_agentd.conf`:
```ini
Server=<IP-адрес_сервера>
ServerActive=<IP-адрес_сервера> 
Hostname=<уникальное_имя_хоста>   # Например, alt-ws1, alt-srv2
HostMetadata=alt.autoreg           # Для авторегистрации
```
**Важно:** Если сервер и агенты находятся в разных подсетях, и трафик идёт через шлюз, добавьте IP-адрес шлюза в `Server` и `ServerActive` (через запятую), чтобы избежать ошибок `connection rejected`.

Запустите агента:
для ALT Linux:
```bash
systemctl enable --now zabbix_agentd.service
systemctl start zabbix_agentd.service
```

### 8. Добавление хостов в Zabbix

**Автоматическая регистрация:**
1. В веб-интерфейсе перейдите в **Data collection → Auto registration**.
2. Создайте правило:
   - **Condition**: `Host metadata` `contains` `alt.autoreg`
   - **Operation**: Add host to group "Linux Servers", link template "Linux by Zabbix agent".
3. Сохраните.

**Ручное добавление (если авторегистрация не работает):**
1. Перейдите в **Data collection → Hosts → Create host**.
2. Заполните:
   - **Host name**: уникальное имя (совпадает с `Hostname` в конфиге агента).
   - **Groups**: выберите `Linux Servers`.
   - **Agent interfaces**: укажите IP-адрес хоста и порт 10050.
   - На вкладке **Templates** прикрепите шаблон `Linux by Zabbix agent`.
3. Сохраните.

---

### 9. Создание карты сети (Monitoring Map)

**Создание карты:**
1. Перейдите в **Monitoring → Maps → Create map**.
2. Задайте название (например, "Стенд").
3. В режиме редактирования:
   - Добавьте элементы (хосты) через **Add**.
   - Перетащите их на поле.
   - Для создания связи: **правой кнопкой** по элементу → **Add link** → выберите второй элемент.
4. Настройте внешний вид:
   - Изменяйте цвета и толщину линий в свойствах связи.
   - Добавляйте фигуры и текст для обозначения подсетей.
5. Сохраните карту.

**Настройка отображения статуса на линиях:**
- В свойствах связи укажите **"Use trigger"** и выберите триггер доступности (например, `{Хост:icmpping.last(0)} = 0`).

---

### 10. Типичные проблемы и их решение

| Проблема | Решение |
|---|---|
| **502 Bad Gateway** | Проверьте, что PHP-FPM запущен: `systemctl status php8.3-fpm`. Убедитесь, что путь к сокету в `fastcgi_pass` корректен. |
| **Permission denied (fastcgi temp)** | Добавьте в конфиг Nginx `fastcgi_buffering off;` или исправьте права: `chown -R nginx:nginx /var/spool/nginx/tmp` |
| **jsLoader.php не загружается** | Отключите сжатие: `gzip off;` в конфиге Nginx. Добавьте `fastcgi_buffering off;`. |
| **Агент не подключается (connection rejected)** | В логах агента смотрите, с какого IP приходит запрос, и добавьте этот IP в `Server` и `ServerActive` через запятую. |
| **404 Not Found** | Проверьте, что `root` в конфиге Nginx указывает на `/var/www/webapps/zabbix/ui` и файлы там есть. |
| **Ошибка IMAGE_FORMAT_PNG** | В `zabbix.conf.php` замените `IMAGE_FORMAT_PNG` на `1` или закомментируйте строку. |

---

### 11. Проверка работоспособности

**Проверьте статус служб:**
```bash
systemctl status nginx php8.3-fpm postgresql zabbix_pgsql
systemctl status zabbix_agentd   # на каждом агенте
```

**Проверьте доступность портов:**
```bash
# С сервера до агента
nc -zv <IP_агента> 10050
# С агента до сервера
nc -zv <IP_сервера> 10051
```

**Проверьте логи:**
```bash
tail -f /var/log/zabbix/zabbix_server.log
tail -f /var/log/zabbix/zabbix_agentd.log
tail -f /var/log/nginx/error.log
tail -f /var/log/php8.3-fpm.log
```