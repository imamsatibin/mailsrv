## Securing Postfix Instalation
Ubah file `main.cf`
```
sudo nano /etc/postfix/main.cf
```
Sesuaikan dengan certificate pada langkah 3 -> [Certbot (SSL)](03-certbot.md)
```
#TLS parameters
smtpd_tls_cert_file = /etc/letsencrypt/live/oxygen.my.id/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/oxygen.my.id/privkey.pem
```

Paksa supaya menggunakan `TLSv1.2` (atau di atasnya)
```
#Enforce TLSv1.2 or TLSv1.3
smtpd_tls_mandatory_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtp_tls_mandatory_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtp_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
```

Ubah `submission`
```
sudo nano /etc/postfix/master.cf
```
Isi dengan (ini untuk SMPT port `587`)
```
submission inet n       -       y       -       -       smtpd
  -o syslog_name=postfix/submission
  -o smtpd_tls_security_level=encrypt
  -o smtpd_sasl_auth_enable=yes
  #add this value to following following line
  -o smtpd_recipient_restrictions=permit_mynetworks,permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
  -o smtpd_sasl_type=dovecot
  -o smtpd_sasl_path=private/auth
```
Lalu dengan (ini untuk SMPTS port `465`) 
```
smtps     inet  n       -       y       -       -       smtpd
  -o syslog_name=postfix/smtps
  -o smtpd_tls_wrappermode=yes
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_recipient_restrictions=permit_mynetworks,permit_sasl_authenticated,reject
  -o smtpd_relay_restrictions=permit_sasl_authenticated,reject
  -o smtpd_sasl_type=dovecot
  -o smtpd_sasl_path=private/auth
```

Restart postfix
```
sudo service postfix restart
```

## Instalasi Dovecot

Kita akan langsung menghubungkan dengan PostgreSQL.
Run command.
```
sudo apt install dovecot-core dovecot-imapd dovecot-pop3d dovecot-lmtpd dovecot-pgsql -y
```

Ubah `mail_location`
```
sudo nano /etc/dovecot/conf.d/10-mail.conf
```

Add user `dovecot` to the mail's group
```
sudo adduser dovecot mail
```

Kembali ke postfix dan ubah file
```
sudo nano /etc/dovecot/conf.d/10-master.conf
```
Ubah `lmtp` menjadi
```
service lmtp {
    unix_listener /var/spool/postfix/private/dovecot-lmtp {
        mode = 0600
        user = postfix
        group = postfix
    }
}
```
Lalu, pada file
```
sudo nano /etc/postfix/main.cf
```
Kita ubah transportnya
```
mailbox_transport = lmtp:unix:private/dovecot-lmtp
# SMTPUTF8 not supported by Dovecot-LMTP
smtputf8_enable = no
```

Terakhir, pada file `/etc/dovecot/conf.d/10-auth.conf` pastikan `disable_plaintext_auth = yes` sudah diaktifkan (ON).

### Securing Dovecot
Pada file ini
```
sudo nano /etc/dovecot/conf.d/10-ssl.conf
```
Perbaharui cert file sesuai [Certbot (SSL)](03-certbot.md)
```
ssl_cert = </etc/letsencrypt/live/oxygen.my.id/fullchain.pem
ssl_key = </etc/letsencrypt/live/oxygen.my.id/privkey.pem
```
^^Jangan hapus tanda `<`

Pastikan juga `ssl_min_protocol = TLSv1.2` dan `ssl_prefer_server_ciphers = yes`

Next step, konfigurasi SASL auth
```
sudo nano /etc/dovecot/conf.d/10-master.conf
```
Sesuaikan `service auth`-nya,
```
unix_listener /var/spool/postfix/private/auth {
    mode = 0666
}
```

Terakhir, ubah `/etc/dovecot/conf.d/15-mailboxes.conf`
```
mailbox Junk {
    auto = create
    special_use = \Junk
}
```
apply juga pada `Drafts`, `Junk`, `Trash` dan `Sent`

Restart service
```
sudo service dovecot restart && sudo service postfix restart
#cek layanan
sudo service dovecot status
sudo service postfix status
```

### Hubungkan dengan PostgreSQL
Edit file,
```
sudo nano /etc/dovecot/conf.d/10-mail.conf
```
Sesuaikan
```
mail_home = /var/vmail/%d/%n
```
Perbaharui konfig
```
/etc/dovecot/conf.d/10-auth.conf
```
pastikan baris ini mati (supaya akses dari db)
```
#!include auth-system.conf.ext
!include auth-sql.conf.ext
```

Ubah konfigurasi sql nya
```
sudo nano /etc/dovecot/dovecot-sql.conf.ext
```
```
driver = pgsql
connect = host=localhost dbname=postfixadmin user=postfixadmin password=password
default_pass_scheme = SHA512
password_query = SELECT username AS user,password FROM mailbox WHERE username = '%u' AND active='1'
user_query = SELECT maildir, 2000 AS uid, 2000 AS gid FROM mailbox WHERE username = '%u' AND active='1'
iterate_query = SELECT username AS user FROM mailbox
```
Finally restart service
```
sudo service dovecot restart
```