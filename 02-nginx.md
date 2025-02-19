## Bagian I: Initial Setup

Install dulu nginxnya.
```
apt-get install nginx -y
```

## Bagian IX: Secure with SSL (Later)
```
cd /etc/nginx/sites-available
```

Buat konfigurasi (1) -> mail.oxygen.my.id
```
nano mail-oxygen-my-id.conf
```
Lalu paste file yang ada pada repository ini (nginx-mail.conf)
Save
```
sudo ln -s /etc/nginx/sites-available/mail-oxygen-my-id.conf /etc/nginx/sites-enabled/mail-oxygen-my-id.conf
```

Buat konfigurasi (2) -> postfixadmin.oxygen.my.id
```
nano postfixadmin-oxygen-my-id.conf
```
Lalu paste file yang ada pada repository ini (nginx-postfixadmin.conf)
Save
```
sudo ln -s /etc/nginx/sites-available/postfixadmin-oxygen-my-id.conf /etc/nginx/sites-enabled/postfixadmin-oxygen-my-id.conf
```

Unlink default config (supaya nginx tidak melayani domain yg tidak ada konfigurasi)
```
unlink /etc/nginx/sites-enabled/default
```
Cek ulang konfigurasi
```
nginx -t
```
Jika ok, lanjut apply konfigurasi baru
```
service nginx restart
```