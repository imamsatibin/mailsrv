## Instalasi Postfixadmin

Install php
```
sudo apt install php-fpm -y
```

Kita akan install pada direktori `/var/www/postfixadmin`
Sesuaikan versi dengan release terakhir (stable).

```
cd /var/www/
sudo wget -O postfixadmin.tar.gz https://github.com/postfixadmin/postfixadmin/archive/postfixadmin-3.3.15.tar.gz
sudo tar -xzvf postfixadmin.tar.gz
sudo mv postfixadmin-postfixadmin-3.3.15/ postfixadmin/
```

Kemudian buat konfigurasi file (local) `config.local.php`:
```
sudo nano /var/www/postfixadmin/config.lokal.php
```

Sesuaikan konfig ini.
```
$CONF['configured'] = true;
$CONF['database_type'] = 'pgsql';
$CONF['database_host'] = 'localhost';
$CONF['database_user'] = 'postfixadmin';
$CONF['database_password'] = 'password';
$CONF['database_name'] = 'postfixadmin';
$CONF['encrypt'] = 'dovecot:SHA512';
```

Buat direktori `templates_c` yang akan digunakan oleh `postfixadmin` dan assign hak akses.
```
sudo mkdir /var/www/postfixadmin/templates_c/ && sudo chown -R www-data /var/www/postfixadmin/templates_c/
```

Buat konfigurasi nginx (SSL didapatkan pada step -> 3. Instalasi [Certbot (SSL)](03-certbot.md))
```
sudo nano /etc/nginx/sites-available/postfixadmin.oxygen.my.id
```
Sesuaikan versi php-fm
```
server {
    listen 80;
    listen [::]:80;
    server_name postfixadmin.oxygen.my.id;

    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name postfixadmin.oxygen.my.id;

    ssl_certificate /etc/letsencrypt/live/postfixadmin.oxygen.my.id/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/postfixadmin.oxygen.my.id/privkey.pem;

    root /var/www/postfixadmin/public;
    index index.php index.html index.htm;

    # Menangani permintaan untuk PostfixAdmin
    location / {
        try_files $uri $uri/ =404;
    }

    location ~ ^/install/ {
        deny all;  # Menangkal akses ke folder install
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock; # Sesuaikan dengan versi PHP Anda
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}

```

Buat symlink
```
sudo ln -s /etc/nginx/sites-available/postfixadmin.oxygen.my.id /etc/nginx/sites-enabled/
```

Verifikasi konfigurasi
```
sudo nginx -t
```

Reload nginx
```
sudo systemctl restart nginx
```

Sampai sini jalankan `https://postfixadmin.oxygen.my.id/setup.php`, kita akan menemukan error karena beberapa dependency belum ada, cek log error pada.
```
sudo tail -f /var/log/nginx/error.log
``` 

Pada contoh ini dependency yang perlu saya install
1. PHP PDO `sudo apt install php8.3-pgsql -y` (sesuaikan versi-nya pada `php --version`)
2. PHP IMAP dan MBString `sudo apt install php-imap php-mbstring -y`

### Setup PostfixAdmin
Panggil `https://postfixadmin.oxygen.my.id/setup.php` pada browser, lalu kita akan diarahkan untuk membuat password admin.
Kita perlu ubah file `/var/www/postfixadminnano config.inc.php` dan isi variabel `$CONF['setup_password']` sesuai instruksi.

```
sudo nano /var/www/postfixadminnano config.inc.php
```

Bagian ini,
```
$CONF['setup_password'] = 'hashed-password-dari-sistem';
```