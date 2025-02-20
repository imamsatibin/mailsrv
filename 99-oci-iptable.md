## Oracle Cloud Infrastructure (OCI)

Pada platform ini selain mendaftarkan port yang ingin dibuka pada VCN. Perlu juga mengganti `iptable` pada instance. 

### Ports 
1. Port 22 → Secure Shell (SSH)
2. Port 25 → SMTP untuk antar server
3. Port 80 → HTTP
4. Port 143 → IMAP tanpa enkripsi
5. Port 443 → HTTP dengan SSL (aman)
6. Port 465 → SMTP dengan SSL (deprecated)
7. Port 587 → SMTP dengan STARTTLS (direkomendasikan)
8. Port 993 → IMAP dengan SSL (aman)

## IPv4
Edit pada file ini `/etc/iptables/rules.v4`
```
sudo nano /etc/iptables/rules.v4
```

Tambahkan port lain, copy saja dari `port 22` yang sudah terbuka secara default.
Jangan lupa SAVE dan EXIT.

Apply perubahan dengan.
```
sudo iptables-restore < /etc/iptables/rules.v4
```

## IPv6
Edit pada file ini `/etc/iptables/rules.v6`
```
sudo nano /etc/iptables/rules.v6
```
Apply perubahan dengan.
```
sudo iptables-restore < /etc/iptables/rules.v6
```

Jika OK semua, kadang ada baiknya coba restart instance, untuk memastikan saja.