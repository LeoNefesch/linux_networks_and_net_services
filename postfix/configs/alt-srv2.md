### Конфигурация на alt-srv2

### Основные параметры
- **IP-адрес:** `10.0.0.12`
- **Имя хоста (myhostname):** `mail2.example.com`
- **Домен:** `example.com`
- **Локальный пользователь:** `leo_srv2`

Файл /etc/hosts
```bash
127.0.0.1 localhost.localdomain localhost
::1       localhost6.localdomain localhost6
192.168.254.128 mail1.example alt-srv1
10.0.0.12       mail2.example alt-srv2
```

Файл /etc/postfix/main.cf
```bash
# Global Postfix configuration file.
myhostname = mail2.example.com
mydomain = example.com
myorigin = $mydomain
inet_interfaces = all
inet_protocols = ipv4
mydestination = $myhostname, localhost.$mydomain, localhost
mynetworks = 127.0.0.1/32 10.0.0.0/24
home_mailbox = Maildir/
smtpd_banner = $myhostname ESMTP $mail_name
biff = no
append_dot_mydomain = no
readme_directory = no

# SASL через Dovecot
smtpd_sasl_auth_enable = yes
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_local_domain = $mydomain
smtpd_sasl_security_options = noanonymous
smtpd_sasl_tls_security_options = noanonymous

# TLS
smtpd_use_tls = yes
smtpd_tls_security_level = may
smtpd_tls_auth_only = yes
smtpd_tls_cert_file = /etc/ssl/certs/mail.crt
smtpd_tls_key_file = /etc/ssl/private/mail.key
smtpd_tls_dh1024_param_file = /etc/ssl/private/dhparams.pem
smtpd_tls_mandatory_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtpd_tls_mandatory_ciphers = high

# Ограничения
smtpd_relay_restrictions = permit_mynetworks, permit_sasl_authenticated, reject_unauth_destination
smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, reject_unauth_destination

# Доставка через Dovecot (транспорт определён в master.cf)
virtual_transport = dovecot
relay_domains = $mydomain
transport_maps = hash:/etc/postfix/transport
```

Файл /etc/postfix/master.cf
(только добавленная строка для транспорта Dovecot)
```bash
dovecot   unix  -       n       n       -       -       pipe
  flags=DRhu user=mail:mail argv=/usr/libexec/dovecot/deliver -d ${recipient}
```

Файл /etc/postfix/transport
```bash
leo_srv2@example.com  local:
leo_srv1@example.com  smtp:[192.168.254.128]
```

Файл /etc/dovecot/dovecot.conf
```bash
dovecot_config_version = 2.4.0
dovecot_storage_version = 2.4.0

protocols = imap pop3

auth_mechanisms = plain
passdb pam {
}
userdb passwd {
  fields {
    mail = maildir:~/Maildir
  }
}

ssl = required
ssl_server_cert_file = /etc/ssl/certs/mail.crt
ssl_server_key_file = /etc/ssl/private/mail.key

service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0660
    user = postfix
    group = postfix
  }
}

service imap-login {
  inet_listener imap { 
    port = 0 
  }
  inet_listener imaps { 
    port = 993 
    ssl = yes 
  }
}

service pop3-login {
  inet_listener pop3 { 
    port = 110 
  }
  inet_listener pop3s { 
    port = 995 
    ssl = yes 
  }
}
```

Файл /etc/aliases
```bash
root: leo_srv2
```

Создание пользователя и Maildir
```bash
useradd -m -s /bin/false leo_srv2
passwd leo_srv2
usermod -a -G mail leo_srv2
mkdir -p /home/leo_srv2/Maildir/{cur,new,tmp}
chown -R leo_srv2:mail /home/leo_srv2/Maildir
chmod -R 750 /home/leo_srv2/Maildir
```

Перезапуск служб
```bash
postmap /etc/postfix/transport
newaliases
systemctl restart postfix dovecot
```