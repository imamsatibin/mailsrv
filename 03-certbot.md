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

### Renewal (Manual)

```
sudo certbot renew
```
Lalu ikuti panduan pada terminal.