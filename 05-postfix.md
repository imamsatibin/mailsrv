## Instalasi Postfix

Run command ini
```
sudo apt install postfix libsasl2-modules -y
```

Sistem akan langsung prompt instalasi postfix. Ikuti saja petunjuk layar.
1. Tipe = Internet Site
2. Host = `oxygen.my.id` (sesuaikan) -> bukan `mail.oxygen.my.id`

#### Relay dengan Mailjet
Buat file baru,
```
sudo nano /etc/postfix/sasl_passwd
```

Isi dengan format ini,
```
in-v3.mailjet.com:587  api-key:secret-key
```

Lalu buat map-nya (hash file). Command ini akan membuat file `sasl_passwd.db`
```
sudo postmap /etc/postfix/sasl_passwd
```

Hapus file asli (plain text)
```
sudo rm /etc/postfix/sasl_passwd
```

Set permission `sasl_passwd.db` filenya.
```
sudo chmod 600 /etc/postfix/sasl_passwd.db
```

Ubah file ini `/etc/postfix/main.cf`
```
sudo nano /etc/postfix/main.cf
```
Cari dan edit bagian ini,
```
relayhost = in-v3.mailjet.com:587
```
Tambahkan ini di akhir konfigurasi,
```
# outbound relay configurations
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_tls_security_level = may
header_size_limit = 4096000
```

Terakhir restart service
```
sudo service postfix restart
```

#### Test kirim email
```
echo -e "Subject: Hi, there.\n\nI hope this email finds you well. Thanks" | sudo sendmail your.email@gmail.com
```