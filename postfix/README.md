## Настройка почтового сервера (Postfix + Dovecot) для пересылки между двумя хостами

### Цель
- Настроить пересылку сообщений между altsrv1 и altsrv2.

### Файлы настроек
- `etc/dovecot/dovecot.conf`
- `/etc/postfix/main.cf`
- `/etc/postfix/master.cf`

### Генерация SSL-сертификатов на обеих ВМ
(/etc/ssl/certs/mail.crt и /etc/ssl/private/mail.key).
```bash
mkdir -p /etc/ssl/private /etc/ssl/certs
chmod 710 /etc/ssl/private
openssl genrsa -out /etc/ssl/private/mail.key 2048
chmod 600 /etc/ssl/private/mail.key
openssl req -new -x509 -days 365 -key /etc/ssl/private/mail.key -out /etc/ssl/certs/mail.crt
# При запросе Common Name (CN) укажите домен вашего сервера (например, mail.example.com)
openssl dhparam -out /etc/ssl/private/dhparams.pem 2048
chmod 600 /etc/ssl/private/dhparams.pem
```



### Проверка работоспособности
```bash
# На alt-srv1 отправляем письмо пользователю с alt-srv2
echo "Test" | mail -s "Test" leo_srv2@example.com
```
```bash
# На alt-srv2 проверяем появление письма
ls -la /home/leo_srv2/Maildir/new/
```
Аналогично - для alt-srv2.

### Логи:
```bash
journalctl -u postfix -f
```
Логи успешной отправки и доставки:
```text
postfix/smtp: to=<leo_srv2@example.com>, relay=10.0.0.12[10.0.0.12]:25, status=sent
postfix/local: to=<leo_srv2@example.com>, status=sent (delivered to maildir)
```

### Примечания
- Для тестовой среды использованы самоподписанные сертификаты.
- В production рекомендуется заменить их на доверенные.
- Брандмауэр должен пропускать порты 25 (SMTP), 993 (IMAPS), 995 (POP3S) при необходимости.


### Оглавление
- config:
  - [alt-srv1](configs/alt-srv1.md)
  - [alt-srv2](configs/alt-srv2.md)
- screenshots:
  - [messages_logs](screenshots/postfix_messages_logs.png)
  - [messages_screen](screenshots/postfix_messages_screen.png)
