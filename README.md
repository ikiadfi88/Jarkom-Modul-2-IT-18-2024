# **LAPORAN RESMI PRAKTIKUM KOMUNIKASI DATA & JARINGAN KOMPUTER MODUL 2**

Berikut adalah Laporan Resmi Praktikum Komunikasi Data & Jaringan Komputer Modul 2 oleh Kelompok IT18.

|Nama                 |NRP               |                                   
|---------------------|----------|
|Iki Adfi Nur Mohamad |5027221033|          
|Siti Nur Ellyzah     |5027221014|
---

### **SOAL 1**

Untuk membantu pertempuran di Erangel, kamu ditugaskan untuk membuat jaringan komputer yang akan digunakan sebagai alat komunikasi. Sesuaikan rancangan Topologi dengan rancangan dan pembagian yang berada di link yang telah disediakan, dengan ketentuan nodenya sebagai berikut :
- DNS Master akan diberi nama Pochinki, sesuai dengan kota tempat dibuatnya server tersebut

- Karena ada kemungkinan musuh akan mencoba menyerang Server Utama, maka buatlah DNS Slave Georgopol yang mengarah ke Pochinki

- Markas pusat juga meminta dibuatkan tiga Web Server yaitu Severny, Stalber, dan Lipovka. Sedangkan Mylta akan bertindak sebagai Load Balancer untuk server-server tersebut

**CARA PENGERJAAN**

1. Membuat konfigurasi network pada setiap node sesuai dengan kerangka topologi 3
   
    (GAMBAR)
   
   - Konfigurasi di Node Erangel
     ```
     auto eth0
     iface eth0 inet dhcp

     auto eth1
     iface eth1 inet static
     address 192.242.1.1
     netmask 255.255.255.0

     auto eth2
     iface eth2 inet static
     address 192.242.2.1
     netmask 255.255.255.0
     ```
     
   - Konfigurasi di Node Pochinki
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.1.2
     netmask 255.255.255.0
     gateway 192.242.1.1
     ```

   - Konfigurasi di Node GatkaTrenches
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.1.3
     netmask 255.255.255.0
     gateway 192.242.1.1
     ```

   - Konfigurasi di Node GatkaRadio
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.1.4
     netmask 255.255.255.0
     gateway 192.242.1.1
     ```

   - Konfigurasi di Node Georgopol
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.1.5
     netmask 255.255.255.0
     gateway 192.242.1.1
     ```

   - Konfigurasi di Node Stalber
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.2.2
     netmask 255.255.255.0
     gateway 192.242.2.1
     ```

   - Konfigurasi di Node Severny
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.2.3
     netmask 255.255.255.0
     gateway 192.242.2.1
     ```

   - Konfigurasi di Node Lipovka
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.2.4
     netmask 255.255.255.0
     gateway 192.242.2.1
     ```

   - Konfigurasi di Node Mylta
     ```
     auto eth0
     iface eth0 inet static
     address 192.242.2.5
     netmask 255.255.255.0
     gateway 192.242.2.1
     ```

2. Menjalankan command di bawah ini pada node Erangel
   ```
   iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.242.0.0/16
   echo 'nameserver 192.168.122.1' > /etc/resolv.conf
   ```

3. Menjalankan command di bawah ini untuk seluruh node agar dapat terkoneksi internet
   ```
   echo 'nameserver 192.168.122.1' > /etc/resolv.conf
   ```

**TESTING**

Testing akses internet dengan contoh tujuan google.com pada seluruh node.
```
ping google.com -c 5
```

**HASIL**

(GAMBAR)

---

### **SOAL 2**

Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Contoh : airdrop.it01.com

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal2.sh. Gunakan command `nano soal2.sh`, lalu masukkan kode bash di bawah ini. 

```bash
#!/bin/bash

apt-get update
apt-get install bind9 -y

mkdir /etc/bind/jarkom

#Nomor 2
cat <<EOL >> /etc/bind/named.conf.local
zone "airdrop.it18.com" {
    type master;
    file "/etc/bind/jarkom/airdrop.it18.com";
};
EOL

cp /etc/bind/db.local /etc/bind/jarkom/airdrop.it18.com

cat <<EOL > /etc/bind/jarkom/airdrop.it18.com
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     airdrop.it18.com. airdrop.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it18.com.
@       IN      A       192.242.2.2    ; IP Stalber
www     IN      CNAME   airdrop.it18.com.
EOL

service bind9 restart
```
2. Setelah itu ketik `chmod +x soal2.sh` dan run dengan `./soal2.sh`

---

### **SOAL 3**

Para pasukan juga perlu mengetahui mana titik yang sedang di bombardir artileri, sehingga dibutuhkan domain lain yaitu redzone.xxxx.com dengan alias www.redzone.xxxx.com yang mengarah ke Severny

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal3.sh. Gunakan command `nano soal3.sh`, lalu masukkan kode bash di bawah ini.

```bash
cat <<EOL >> /etc/bind/named.conf.local
zone "redzone.it18.com" {
    type master;
    file "/etc/bind/jarkom/redzone.it18.com";
};
EOL

cp /etc/bind/db.local /etc/bind/jarkom/redzone.it18.com

cat <<EOL > /etc/bind/jarkom/redzone.it18.com
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     redzone.it18.com. redzone.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it18.com.
@       IN      A       192.242.2.3     ; IP Severny
www     IN      CNAME   redzone.it18.com.
EOL

service bind9 restart
```

2. Setelah itu ketik `chmod +x soal3.sh` dan run dengan `./soal3.sh`

---

### **SOAL 4**

Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi persenjataan dan suplai tersebut mengarah ke Mylta dan domain yang ingin digunakan adalah loot.xxxx.com dengan alias www.loot.xxxx.com

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal4.sh. Gunakan command `nano soal4.sh`, lalu masukkan kode bash di bawah ini.

```bash
cat <<EOL >> /etc/bind/named.conf.local
zone "loot.it18.com" {
    type master;
    file "/etc/bind/jarkom/loot.it18.com";
};
EOL

cp /etc/bind/db.local /etc/bind/jarkom/loot.it18.com

cat <<EOL > /etc/bind/jarkom/loot.it18.com
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     loot.it18.com. loot.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loot.it18.com.
@       IN      A       192.242.2.5     ; IP Mylta
www     IN      CNAME   loot.it18.com.
EOL

service bind9 restart
```

2. Setelah itu ketik `chmod +x soal4.sh` dan run dengan `./soal4.sh`

---

### **SOAL 5**

Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Erangel

**CARA PENGERJAAN**

1. Untuk memastikan agar client yaitu GatkaTrenches dan GatkaRadio dapat mengakses domain-domain di nomor 2-4, maka saya membuka web console ke GatkaTrenches dan GatkaRadio. Lalu dilakukan setup pada script bash dengan nama client.sh.  Gunakan command `nano client.sh`, lalu masukkan kode bash di bawah ini.

```bash
cat <<EOL > /etc/resolv.conf
nameserver 192.242.1.2
nameserver 192.242.1.5
EOL
```

2. Setelah itu ketik `chmod +x client.sh` dan run dengan `./client.sh`

**TESTING**

Testing menggunakan command di bawah ini di GatkaTrenches dan GatkaRadio

```
ping airdrop.it18.com -c 5
ping redzone.it18.com -c 5
ping loot.it18.com -c 5
```

**HASIL**

- GatkaTrenches

(GAMBAR)

- GatkaRadio

(GAMBAR)

---

### **SOAL 6**

Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain redzone.xxxx.com melalui alamat IP Severny (Notes : menggunakan pointer record)

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal6.sh. Gunakan command `nano soal6.sh`, lalu masukkan kode bash di bawah ini. Pada kode ini saya menggunakan pointer record.

```bash
#!/bin/bash

cat >> /etc/bind/named.conf.local <<EOL
zone "2.242.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.242.192.in-addr.arpa";
};
EOL

cp /etc/bind/db.local /etc/bind/jarkom/2.242.192.in-addr.arpa

cat >> /etc/bind/jarkom/2.242.192.in-addr.arpa <<EOL
\$TTL    604800
@       IN      SOA     redzone.it18.com. redzone.it18.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it18.com.
@       IN      A       192.242.2.3     ; IP Severny
www     IN      CNAME   redzone.it18.com.
2.242.192.in-addr.arpa IN NS redzone.it18.com.
3       IN      PTR     redzone.it18.com.
EOL

# Restart server
service bind9 restart
```

2. Setelah itu ketik `chmod +x soal6.sh` dan run dengan `./soal6.sh`

3. Setelah itu buka web console ke node client GatkaTrenches dan GatkaRadio, kemudian ketik command `nano /etc/resolv.conf`pada masing-masing node. Command nameserver Pochinki `192.242.1.2` dan Georgopol `192.242.1.2`, lalu tambahkan nameserver severny yaitu `192.242.2.3`

(GAMBAR)

**TESTING**

Testing menggunakan command di bawah ini di GatkaTrenches dan GatkaRadio

```
ping redzone.it18.com -c 5
```

Jika muncul `ping: redzone.it18.com: Temporary failure in name resolution` , maka berarti client tidak bisa mengakses domain tersebut, selanjutnya tes menggunakan command di bawah ini

```
ping 192.242.2.3 -c 5
```

Jika memunculkan hasil ping, maka client sudah berhasil hanya bisa mengakses server Severny.

**HASIL**

- GatkaTrenches

(GAMBAR)

- GatkaRadio

(GAMBAR)

---

### **SOAL 7**

Akhir-akhir ini seringkali terjadi serangan siber ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Georgopol untuk semua domain yang sudah dibuat sebelumnya.

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal7pochinki.sh. Gunakan command `nano soal7pochinki.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

#Nomor 7
cat <<EOL > /etc/bind/named.conf.local
zone "airdrop.it18.com" {
    type master;
    notify yes;
    also-notify { 192.242.1.5; }; //IP Georgopol
    allow-transfer { 192.242.1.5; }; //IP Georgopol
    file "/etc/bind/jarkom/airdrop.it18.com";
};

zone "redzone.it18.com" {
    type master;
    notify yes;
    also-notify { 192.242.1.5; }; //IP Georgopol
    allow-transfer { 192.242.1.5; }; //IP Georgopol
    file "/etc/bind/jarkom/redzone.it18.com";
};

zone "loot.it18.com" {
    type master;
    notify yes;
    also-notify { 192.242.1.5; }; //IP Georgopol
    allow-transfer { 192.242.1.5; }; //IP Georgopol
    file "/etc/bind/jarkom/loot.it18.com";
};
EOL

service bind9 restart

service bind9 stop
```

2. Ketik command `chmod +x soal7pochinki.sh` dan run dengan `./soal7pochinki.sh`
   
3. Setelah itu matikan server Pochinki sesuai dengan command di soal7pochinki.sh.

4. kemudian setup node Georgopol, lalu menggunakan script bash soal7georgopol.sh. Gunakan command `nano soal7georgopol.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

# Update package lists and install BIND9
apt-get update
apt-get install bind9 -y

# Configure BIND9 zones
cat <<EOL > /etc/bind/named.conf.local
zone "airdrop.it18.com" {
    type slave;
    masters {192.242.1.2; };
    file "/var/lib/bind/airdrop.it18.com";
};

zone "redzone.it18.com" {
    type slave;
    masters {192.242.1.2; };
    file "/var/lib/bind/redzone.it18.com";
};

zone "loot.it18.com" {
    type slave;
    masters {192.242.1.2; };
    file "/var/lib/bind/loot.it18.com";
};
EOL

# Restart BIND9 service
service bind9 restart

```

5. Ketik command `chmod +x soal7georgopol.sh` dan run dengan `./soal7georgopol.sh`

**TESTING**

Testing menggunakan command di bawah ini di salah satu domain pada client GatkaTrenches dan GatkaRadio

```
ping redzone.it18.com -c 5
```

Jika berhasil menampilkan IP nya , maka berarti client bisa mengakses domain tersebut walaupun DNS master nya mati, artinya Georgopol sebagai DNS Slave telah berjalan dan berhasil.

**HASIL**

- GatkaTrenches

(GAMBAR)

- GatkaRadio

(GAMBAR)

---

### **SOAL 8**

Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

**CARA PENGERJAAN**
1. Tambahkan subdomain medkit.airdrop.it18.com pada domain airdrop.it18.com yang mengarah ke lipovka. Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal.sh. Gunakan command `nano soal8.sh`, lalu masukkan kode bash di bawah ini.

```bash
cat <<EOL >/etc/bind/jarkom/airdrop.it18.com
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     airdrop.it18.com. airdrop.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      airdrop.it18.com.
@       IN      A       192.242.2.2     ; IP Stalber
www     IN      CNAME   airdrop.it18.com.
medkit  IN      A       192.242.2.4     ; IP Lipovka
@       IN      AAAA    ::1
EOL

service bind9 restart
```

2. Ketik command `chmod +x soal8.sh` dan run dengan `./soal8.sh`

**TESTING**

Testing menggunakan command di bawah ini pada client GatkaTrenches dan GatkaRadio

```
ping medkit.airdrop.it18.com -c 5
```

**HASIL**

- GatkaTrenches

(GAMBAR)

- GatkaRadio

(GAMBAR)

---

### **SOAL 9**

Terkadang red zone yang pada umumnya di bombardir artileri akan dijatuhi bom oleh pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke subdomain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

**CARA PENGERJAAN**
1. Tambahkan subdomain siren.redzone.it18.com pada pada server Pochinki /etc/bind/jarkom/redzone.it18.com .Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal.sh. Gunakan command `nano soal9pochink.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

cat >> /etc/bind/jarkom/redzone.it18.com <<EOL
\$TTL    604800
@       IN      SOA     redzone.it18.com. redzone.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      redzone.it18.com.
@       IN      A       192.242.1.2     ; IP Pochinki
www     IN      CNAME   redzone.it18.com.
@       IN      A       192.242.2.3     ; IP Severny
2.242.192.in-addr.arpa IN NS redzone.it18.com.
3       IN      PTR     redzone.it18.com.
siren   IN      A       192.242.1.5     ; IP Georgopol
ns1     IN      A       192.242.1.5     ; IP Georgopol
log     IN      NS      ns1
@       IN      AAAA    ::1
EOL

# Restart server
service bind9 restart
```

2. Ketik command `chmod +x soal9pochink.sh` dan jalankan dengan `./soal9pochink.sh`

3. Buka `named.conf.options` dengan command `/etc/bind/named.conf.options` pada Pochinki. Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options. `  allow-query{any;};`

4. Setelah itu restart Pochinki dengan command `service bind9 restart`

5. Pada server Georgopol juga tambahkan subdomain siren.redzone.it18.com pada config local Georgopol. Setelah itu buat folder delegasi baru dan konfigurasikan seperti file soal9georgopol.sh. Gunakan command `nano soal9georgopol.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

# Update package lists and install BIND9
apt-get update
apt-get install bind9 -y

# Add zone configuration to named.conf.local
cat <<EOL >> /etc/bind/named.conf.local
zone "siren.redzone.it18.com" {
    type master;
    file "/etc/bind/delegasi/siren.redzone.it18.com";
};
EOL

# Create directory for zone files
mkdir -p /etc/bind/delegasi/

# Copy db.local to siren.redzone.it18.com
cp /etc/bind/db.local /etc/bind/delegasi/siren.redzone.it18.com

# Create zone file for siren.redzone.it18.com
cat <<EOL > /etc/bind/delegasi/siren.redzone.it18.com
\$TTL 604800
@       IN      SOA     siren.redzone.it18.com. root.siren.redzone.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
@       IN      NS      siren.redzone.it18.com.
@       IN      A       192.242.1.5
log     IN      A       192.242.1.5
EOL

# Restart BIND to apply changes
service bind9 restart
```

6. Ketik command `chmod +x soal9georgopol.sh` dan run dengan `./soal9georgopol.sh`

7. Sama seperti sebelumnya, buka `named.conf.options` dengan command `/etc/bind/named.conf.options` pada Georgopol. Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options. `  allow-query{any;};`

8. Setelah itu restart Georgopol dengan command `service bind9 restart`

**TESTING**

Testing menggunakan command di bawah ini pada client GatkaTrenches dan GatkaRadio

```
ping siren.redzone.it18.com -c 5
```

**HASIL**

- GatkaTrenches

(GAMBAR)

- GatkaRadio

(GAMBAR)

---

### **SOAL 10**

Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

**CARA PENGERJAAN**
1. Tambahkan subdomain log.siren.redzone.it18.com pada pada server Pochinki /etc/bind/jarkom/redzone.it18.com .Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal.sh. Gunakan command `nano soal9pochink.sh`, lalu masukkan kode bash di bawah ini.




