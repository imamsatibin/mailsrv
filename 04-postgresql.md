## PostgreSQL
Langkah pertama menginstall PostgreSQL
```
apt-get install postgresql -y
```
Pindah ke dalam terminal PostgreSQL
```
su - postgres
```
#### Buat user database dengan nama postfixadmin
```
createuser --createdb --username postgres --no-createrole --pwprompt postfixadmin
```
Setelah command tersebut di atas di-execute, kita akan diarahkan untuk mengisi password, ingat dan simpan, karena akan digunakan sebagai config nantinya.

Masuk ke terminal PSQL (di dalam PostgreSQL)
```
psql
```
Berikan hak akses
```
GRANT ALL PRIVILEGES ON DATABASE postfixadmin TO postfixadmin;
```

#### Buat user database dengan nama roundcube
```
createuser --createdb --username postgres --no-createrole --pwprompt roundcube
```
Setelah command tersebut di atas di-execute, kita akan diarahkan untuk mengisi password, ingat dan simpan, karena akan digunakan sebagai config nantinya.

Masuk ke terminal PSQL (di dalam PostgreSQL)
```
psql
```
Berikan hak akses
```
GRANT ALL PRIVILEGES ON DATABASE roundcube TO roundcube;
```

Keluar dari terminal PSQL
```
\q
```

Dilanjutkan keluar dari terminal PostgreSQL
```
exit
```

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
