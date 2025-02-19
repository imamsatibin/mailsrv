## Certbot (SSL Certificate)

Ada beberapa cara untuk mendapatkan SSL, bisa manual ataupun otomatis.
Otomatis mengharuskan kita mengarahkan A-record ke IP host.
Karena kita tidak akan mengarahkan `oxygen.my.id` ke IP host, jadi kita akan request manual.

Install certbot (manual).
```
sudo apt-get install certbot -y
```

Request SSL (Let's Encrypt)
```
sudo certbot certonly --manual -d "oxygen.my.id" -d "mail.oxygen.my.id" -d "postfixadmin.oxygen.my.id" --preferred-challenges=dns
```

@location of the cert.
```
/etc/letsencrypt/live/oxygen.my.id/fullchain.pem
/etc/letsencrypt/live/oxygen.my.id/privkey.pem
```
```
/etc/letsencrypt/live/mail.oxygen.my.id/fullchain.pem
/etc/letsencrypt/live/mail.oxygen.my.id/privkey.pem
```
```
/etc/letsencrypt/live/postfixadmin.oxygen.my.id/fullchain.pem
/etc/letsencrypt/live/postfixadmin.oxygen.my.id/privkey.pem
```

### Wildcard SSL Manual
Atau, lebih mudah menggunakan wildcard SSL (manual renewal)
```
certbot certonly \
  --manual \
  --preferred-challenges=dns \
  --email imam@oxygen.my.id \
  --server https://acme-v02.api.letsencrypt.org/directory \
  --agree-tos \
  -d "oxygen.my.id" \
  -d "*.oxygen.my.id"
```
^command ini hanya akan menghasilkan 1 certificate yaitu oxygen.my.id

### Wildcard SSL Automatic (API Cloudflare)
Simpan API
```
sudo mkdir -p /etc/letsencrypt/credentials
sudo touch /etc/letsencrypt/credentials/cloudflare.ini
sudo chmod 600 /etc/letsencrypt/credentials/cloudflare.ini
sudo chown root:root /etc/letsencrypt/credentials/cloudflare.ini
```
Isi file `cloudflare.ini` adalah sebagai berikut. API bisa didapat di menu `My Profile`.
```
dns_cloudflare_api_token = your-cloudflare-api-token
```

Menjalankan command ini perlu plugin tambahan.
```
sudo apt-get install python3-certbot-dns-cloudflare -y
```
Ini command request Wildcard SSL + DNS CF + Auto renewal
```
certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /etc/letsencrypt/credentials/cloudflare.ini \
  -d "oxygen.my.id" \
  -d "*.oxygen.my.id"
```

## Renewal (Manual)

```
sudo certbot renew
```
Lalu ikuti panduan pada terminal.