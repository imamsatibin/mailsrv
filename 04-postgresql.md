## PostgreSQL
Langkah pertama menginstall PostgreSQL
```
apt-get install postgresql -y
```
Pindah ke dalam terminal PostgreSQL
```
su - postgres
```
#### Buat user dan database dengan nama postfixadmin
```
createuser --createdb --username postgres --no-createrole --pwprompt postfixadmin
createdb --username postgres --owner=postfixadmin postfixadmin
```
Setelah command tersebut di atas di-execute, kita akan diarahkan untuk mengisi password, ingat dan simpan, karena akan digunakan sebagai config nantinya.

#### Buat user dan database dengan nama roundcube
```
createuser --createdb --username postgres --no-createrole --pwprompt roundcube
createdb --username postgres --owner=roundcube roundcube
```
Setelah command tersebut di atas di-execute, kita akan diarahkan untuk mengisi password, ingat dan simpan, karena akan digunakan sebagai config nantinya.

Dilanjutkan keluar dari terminal PostgreSQL
```
exit
```

### Khusus OCI
Setup tambahan ketika install PostgreSQL di OCI (Oracle Cloud Infrastructure)
```
sudo apt-get install iptables-persistent
```

Add rule iptables
```
sudo iptables -I INPUT 5 -p tcp --dport 5432 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```

Lalu simpan supaya perubahan ini permanen
```
sudo netfilter-persistent save
```

Atau ikuti langkah pada -> [OCI IPTable](99-oci-iptable.md)

Restart service postgres
```
sudo systemctl restart postgresql
```

### Problem Koneksi
Jika masih gagal koneksi dari luar, pastikan port (5432) VCN = ON
Pada file ini (sesuaikan versi) `/etc/postgresql/[versi]/main/postgresql.conf`

Bagian ini harus,
```
listen_addresses = '*'
```

Sementara pada file (sesuaikan versi) `/etc/postgresql/[versi]/main/pg_hba.conf`
Tambahkan baris ini
```
host    all             all             0.0.0.0/0            md5
```
Jangan lupa, restart service.
```
sudo systemctl restart postgresql
```