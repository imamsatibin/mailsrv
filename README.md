# Instalasi Mail Server menggunakan Ubuntu 24.04

Pertama kita akan memetakan A/AAAA record ke DNS record domain yang hendak digunakan.
Misal akan menggunakan hostname `oxygen.my.id`. 

Di sini kita akan bagi menjadi 3 hostname.
1. `oxygen.my.id` -> sebagai system utama email
2. `mail.oxygen.my.id` -> untuk dovecot dan roundcube
3. `postfixadmin.oxygen.my.id` -> untuk postfixadmin

Pastikan ketiga-nya kita telah atur A-recordnya.

Step by step:
1. Persiapan -> [Ubuntu Prep](01-ubuntu-prep.md)
2. Instalasi -> [Nginx](02-nginx.md)
3. Instalasi -> [Certbot (SSL)](03-certbot.md)
4. Instalasi -> [PostgreSQL](04-postgresql.md)
5. Instalasi -> [Postfix](05-postfix.md)
6. Instalasi -> [Dovecot](06-dovecot.md)
7. Instalasi -> [PostfixAdmin](07-postfixadmin.md)
8. Instalasi -> [Roundcube](08-roundcube.md)