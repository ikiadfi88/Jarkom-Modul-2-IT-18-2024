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
   
    ![soal1](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/67eaca30-7e49-4800-9d87-5e181c48de9f)

   
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

3. Pastikan enjalankan command di bawah ini untuk seluruh node server agar dapat terkoneksi internet
   ```
   echo 'nameserver 192.168.122.1' > /etc/resolv.conf
   ```

**TESTING**

Testing akses internet dengan contoh tujuan google.com pada seluruh node.
```
ping google.com -c 5
```

**HASIL**

![soal1a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/1d94a1f1-c94c-4ebb-a5ff-d5bfe088f373)

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

![soal5a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/c18e68a8-ca89-49e6-b569-c99db3faf293)


- GatkaRadio

![soal5b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/0fdbd527-8e4d-4248-b627-a54371da6c57)


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

![soal6a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/0a4d3d97-bead-4eff-a143-c480ce6ca72b)

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

![soal6b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/1cade50c-33db-4921-b8ff-ca968e0999d7)


- GatkaRadio

![soal6c](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/43b88b5f-f3bd-42a9-b264-e9acd57212bd)

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

3. kemudian setup node Georgopol, lalu menggunakan script bash soal7georgopol.sh. Gunakan command `nano soal7georgopol.sh`, lalu masukkan kode bash di bawah ini.

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

4. Ketik command `chmod +x soal7georgopol.sh` dan run dengan `./soal7georgopol.sh`

5. Untuk memastikan **Georgopol** bekerja sebagai DNS Slave, maka matikan server **Pochinki** menggunakan `service bind9 stop`

**TESTING**

Testing menggunakan command di bawah ini di salah satu domain pada client GatkaTrenches dan GatkaRadio

```
ping airdrop.it18.com -c 5
ping redzone.it18.com -c 5
ping loot18.it18.com -c 5
```

Jika berhasil menampilkan IP nya , maka berarti client bisa mengakses domain tersebut walaupun DNS master nya mati, artinya Georgopol sebagai DNS Slave telah berjalan dan berhasil.

**HASIL**

- GatkaTrenches

![soal5a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/5256ce39-ca78-4660-90c4-2d8e223f5d9a)


- GatkaRadio

![soal5b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/6da85ec6-3fc8-45ef-87d5-787c7c5966d5)


---

### **SOAL 8**

Kamu juga diperintahkan untuk membuat subdomain khusus melacak airdrop berisi peralatan medis dengan subdomain medkit.airdrop.xxxx.com yang mengarah ke Lipovka

**CARA PENGERJAAN**
1. Tambahkan subdomain medkit.airdrop.it18.com pada domain airdrop.it18.com yang mengarah ke Lipovka. Pada pengerjaan ini saya membuka web console pada **Pochinki**, lalu menggunakan script bash soal.sh. Gunakan command `nano soal8.sh`, lalu masukkan kode bash di bawah ini.

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

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
ping medkit.airdrop.it18.com -c 5
```

**HASIL**

- GatkaTrenches

![soal8b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/03698d57-c1c2-4900-831b-b9a917356aa3)

- GatkaRadio

![soal8a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/9cdd0a13-434a-4cbd-ba68-9ff8fb0c3e5c)

---

### **SOAL 9**

Terkadang red zone yang pada umumnya di bombardir artileri akan dijatuhi bom oleh pesawat tempur. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan air raid dan memasukkannya ke subdomain siren.redzone.xxxx.com dalam folder siren dan pastikan dapat diakses secara mudah dengan menambahkan alias www.siren.redzone.xxxx.com dan mendelegasikan subdomain tersebut ke Georgopol dengan alamat IP menuju radar di Severny

**CARA PENGERJAAN**
1. Tambahkan subdomain siren.redzone.it18.com pada pada server **Pochinki** /etc/bind/jarkom/redzone.it18.com .Pada pengerjaan ini saya membuka web console pada Pochinki, lalu menggunakan script bash soal.sh. Gunakan command `nano soal9pochink.sh`, lalu masukkan kode bash di bawah ini.

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
ns1     IN      A       192.242.1.5     ; IP Georgopol
siren   IN      A       ns1'           
EOL

# Restart server
service bind9 restart
```

2. Ketik command `chmod +x soal9pochink.sh` dan jalankan dengan `./soal9pochink.sh`

3. Buka `named.conf.options` dengan command `/etc/bind/named.conf.options` pada **Pochinki**. Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options. `  allow-query{any;};`

4. Setelah itu restart **Pochinki** dengan command `service bind9 restart`

5. Pada server **Georgopol** juga tambahkan subdomain siren.redzone.it18.com pada config local **Georgopol**. Setelah itu buat folder delegasi baru dan konfigurasikan seperti file soal9georgopol.sh. Gunakan command `nano soal9georgopol.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

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
@       IN      A       192.242.2.3
www     IN      CNAME   siren.redzone.it18.com.
EOL

# Restart BIND to apply changes
service bind9 restart

```

6. Ketik command `chmod +x soal9georgopol.sh` dan run dengan `./soal9georgopol.sh`

7. Sama seperti sebelumnya, buka `named.conf.options` dengan command `/etc/bind/named.conf.options` pada **Georgopol**. Kemudian comment dnssec-validation auto; dan tambahkan baris berikut pada /etc/bind/named.conf.options. `  allow-query{any;};`

8. Setelah itu restart **Georgopol** dengan command `service bind9 restart`

**TESTING**

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
ping siren.redzone.it18.com -c 5
```

**HASIL**

- GatkaTrenches

![soal9b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/ddade1ce-9028-4e2a-bd2a-e6defe290468)


- GatkaRadio

![soal9a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/7e96f1db-fc65-49ba-917f-9e356358b732)


---

### **SOAL 10**

Markas juga meminta catatan kapan saja pesawat tempur tersebut menjatuhkan bom, maka buatlah subdomain baru di subdomain siren yaitu log.siren.redzone.xxxx.com serta aliasnya www.log.siren.redzone.xxxx.com yang juga mengarah ke Severny

**CARA PENGERJAAN**
1. Tambahkan subdomain log.siren.redzone.it18.com pada pada server **Georgopol**.Pada pengerjaan ini saya membuka web console pada **Georgopol**, lalu menggunakan script bash soal10.sh. Gunakan command `nano soal10.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

cat << EOL > /etc/bind/siren/siren.redzone.it18.com
;
; BIND data file for local loopback interface
;
\$TTL    604800
@       IN      SOA     siren.redzone.it18.com. siren.redzone.it18.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      siren.redzone.it18.com.
@       IN      A       192.242.2.3     ; IP Severny
www     IN      CNAME   siren.redzone.it18.com.
log     IN      A       192.242.2.3     ; IP Severny
www.log IN      CNAME   siren.redzone.it18.com.
EOL

service bind9 restart

```
2. Ketik command `chmod +x soal10.sh` dan jalankan dengan `./soal10.sh`

**TESTING**

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
ping log.siren.redzone.it18.com -c 5
ping www.log.siren.redzone.it18.com -c 5
```

**HASIL**

- GatkaTrenches

![soal10a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/b5d47efa-e12d-4a4b-b9c4-e6af1e2f2f35)

- GatkaRadio

![soal10b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/0927d598-ac3d-4ae7-b0c5-f88ab62fba4a)

---

### **SOAL 11**

Setelah pertempuran mereda, warga Erangel dapat kembali mengakses jaringan luar, tetapi hanya warga Pochinki saja yang dapat mengakses jaringan luar secara langsung. Buatlah konfigurasi agar warga Erangel yang berada diluar Pochinki dapat mengakses jaringan luar melalui DNS Server Pochinki

**CARA PENGERJAAN**

1. Edit file menggunakan `nano /etc/bind/named.conf.options` pada server **Pochinki**

2. Kemudian uncomment pada bagian ini dan tambahkan IP Erangel
   
```
forwarders {
      192.168.122.1; //IP Erangel
};
```

3. Lalu comment pada bagian ini

```
// dnssec-validation auto;
```

4. Dan tambahkan

```
allow-query{any;};
```

5. Lakukan restart pada **Pochinki** dengan commadn berikut

```
service bind9 restart
```

6. Dengan cara yang sama, edit file menggunakan `nano /etc/bind/named.conf.options` pada server **Georgopol**

7. Kemudian uncomment pada bagian ini dan tambahkan IP Erangel
   
```
forwarders {
      192.168.122.1; //IP Erangel
};
```

8. Lalu comment pada bagian ini

```
// dnssec-validation auto;
```

9. Dan tambahkan

```
allow-query{any;};
```

10. Lakukan restart pada **Pochinki** dengan commadn berikut

```
service bind9 restart
```

**TESTING**

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
ping google.com -c 5
```

**HASIL**

- GatkaTrenches

![soal11a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/da78e396-b478-4d4d-b6f0-711b5da46451)

- GatkaRadio

![soal11b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/ada82b5e-8a21-4d80-af90-330527263063)

---

### **SOAL 12**

Karena pusat ingin sebuah website yang ingin digunakan untuk memantau kondisi markas lainnya maka deploy lah website ini (cek resource yang lb) pada severny menggunakan apache

**CARA PENGERJAAN**

1. Pada server **Pochinki** dan **Georgopol** tambahkan IP Severny dengan ketik `nano /etc/resolv.conf`. Lalu tambahkan bagian ini:

```
nameserver 10.75.2.3    #IP Severny
```

2. Selanjutnya pada server client **GatkaTrenches** dan **GatkaRadio** jalankan script bash install-lynx.sh. Gunakan command `nano install-lynx.sh`, lalu masukkan kode bash di bawah ini.

```bash
#!/bin/bash

# Update package lists
apt-get update

# Install lynx
apt-get install lynx -y
```
3. Ketik command `install-lynx.sh` dan jalankan dengan `./install-lynx.sh`
   
4. Kemudian pada server **Severny** jalankan script bash severny.sh. Gunakan command `nano severny.sh`, lalu masukkan kode bash di bawah ini. Script bash ini berisi instalasi dan konfigurasi severny.

```bash
#!/bin/bash

    # Melakukan instalasi apache2
    apt-get update
    apt-get install apache2 -y
    apt-get install libapache2-mod-php7.0 -y

    # Melakukan instalasi unzip
    apt-get update
    apt-get install unzip -y

    # Melakukan instalasi php
    apt-get update
    apt-get install php -y

# Download file lb.zip
curl -L -o lb.zip --insecure "https://drive.google.com/uc?export=download&id=1xn03kTB27K872cokqwEIlk8Zb121HnfB"

# Unzip file lb.zip
unzip lb.zip

# Hapus file template
rm -rf /var/www/html/index.php

# Copy file index.php
cp worker/index.php /var/www/html/index.php

service apache2 restart
```

5. Ketik command `chmod +x severny.sh` dan jalankan dengan `./severny.sh`

**TESTING**

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
lynx http://192.242.2.3/index.php
```

**HASIL**

- GatkaTrenches

![soal12](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/39c9db92-66e5-467b-86ff-856f47349042)

- GatkaRadio

![soal12b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/122e9381-6361-4d8d-b6c5-81292834cddd)

---

### **SOAL 13**

Tapi pusat merasa tidak puas dengan performanya karena traffic yang tinggi maka pusat meminta kita memasang load balancer pada web nya, dengan Severny, Stalber, Lipovka sebagai worker dan Mylta sebagai Load Balancer menggunakan apache sebagai web server nya dan load balancernya

**CARA PENGERJAAN**

1. Pada server **Pochinki** dan **Georgopol** tambahkan IP Mylta dengan ketik `nano /etc/resolv.conf`. Lalu tambahkan bagian ini:

```
nameserver 192.242.2.5    #IP Mylta
```

2. Kemudian pada server **Stalber** dan **Lipovka** jalankan script bash loadbalance.sh. Gunakan command `nano loadbalance.sh`, lalu masukkan kode bash di bawah ini. Script bash ini berisi instalasi dan konfigurasi load balancer.

```bash
#!/bin/bash

    # Melakukan instalasi apache2
    apt-get update
    apt-get install apache2 -y
    apt-get install libapache2-mod-php7.0 -y

    # Melakukan instalasi unzip
    apt-get update
    apt-get install unzip -y

    # Melakukan instalasi php
    apt-get update
    apt-get install php -y

# Download file lb.zip
curl -L -o lb.zip --insecure "https://drive.google.com/uc?export=download&id=1xn03kTB27K872cokqwEIlk8Zb121HnfB"

# Unzip file lb.zip
unzip lb.zip

# Hapus file template
rm -rf /var/www/html/index.php

# Copy file index.php
cp worker/index.php /var/www/html/index.php

service apache2 restart
```

3. Ketik command `chmod +x loadbalance.sh` dan jalankan dengan `./loadbalance.sh`

4. Kemudian pada server **Mylta** jalankan script bash loadbalancemylta.sh . Gunakan command `nano loadbalancemylta.sh`, lalu masukkan kode bash di bawah ini. Script bash ini berisi instalasi dan konfigurasi load balancer.

```bash
#!/bin/bash

    # Melakukan instalasi apache2
    apt-get update
    apt-get install apache2 -y
    apt-get install libapache2-mod-php7.0 -y

    # Melakukan instalasi php
    apt-get update
    apt-get install php -y

# Enable apache2 module
a2enmod proxy_balancer
a2enmod proxy_http
a2enmod lbmethod_byrequests
cat <<EOF > /etc/apache2/sites-available/000-default.conf
<VirtualHost *:80>
    <Proxy balancer://serverpool>
        BalancerMember http://192.242.2.2/
        BalancerMember http://192.242.2.3/
        BalancerMember http://192.242.2.4/
        Proxyset lbmethod=byrequests
    </Proxy>
    ProxyPass / balancer://serverpool/
    ProxyPassReverse / balancer://serverpool/
</VirtualHost>
EOF

service apache2 restart
```

5. Ketik command `chmod +x loadbalancemylta.sh` dan jalankan dengan `./loadbalancemylta.sh`

**TESTING**

Testing menggunakan command di bawah ini pada client **GatkaTrenches** dan **GatkaRadio**

```
lynx http://192.242.2.5/index.php
```

**HASIL**

- GatkaTrenches

![soal13a](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/8f3cd0a9-b26c-4645-a67c-1b4ad0d40a53)

- GatkaRadio

![soal13b](https://github.com/ikiadfi88/Jarkom-Modul-2-IT-18-2024/assets/120791817/54267ec6-88b2-40aa-8dc9-922c524ae535)

---

### **SOAL 14**

Mereka juga belum merasa puas jadi pusat meminta agar web servernya dan load balancer nya diubah menjadi nginx

**CARA PENGERJAAN**

1. Stop service apache yang sebelumnya berjalan di web server dan load balancer 
2. Lakukan install Nginx 

    ```
    apt-get update
    apt-get install nginx
    ```

3. Untuk pengecekan bisa dengan ```service nginx status```
4. Selanjutnya input konfigurasi
  ```
  http {
    upstream jarkom_backend {
        zone upstream;
        server 192.242.2.3; # IP Severny
        server 192.242.2.2; # IP Stalber
    }
}

server {
    listen 80;
    name_server myIta.it18.com www.myIta.it18.com;

    location / {
        proxy_pass http://jarkom_backend;
    }
}
  ```
5. Dan lakukan simlink file di sites-available dengan sites-enabled
  ```
  " > /etc/nginx/sites-available/jarkom

  ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom
  ```
6. Selanjutnya, hapus default agar tidak menabrak konfigurasi yang sudah dibuat sebelumnya
  ```
  rm /etc/nginx/sites-enabled/default
  ```
7. Lalu kita bisa restart kembali Nginx ```service nginx restart``` 

8. Untuk severny, stalber, dan lipovka lakukan kembali step 1 dan 2
9. Install ```apt install php php-fpm php-mysql -y```
10. Selanjutnya buat folder untuk jarkom
  ```
  mkdir /var/www/jarkom
  cp /root/lb/worker/index.php /var/www/jarkom/index.php
  ```
11. Jalankan ```service php7.2-fpm``` = ```service php7.0-fpm start```
12. Selanjutnya input konfigurasi ke dalam ```/etc/nginx/sites-available/jarkom```
  ```
  echo -e "server {
        listen 80;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        name-server;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/var/run/php/php7.2-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
          }" > /etc/nginx/sites-available/jarkom
  ```
13. Seperti step 5, 6 dan 7, lakukan simlink file, delete file default dan restart Nginx 
  ```
  ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

  rm /etc/nginx/sites-enabled/default

  service nginx restart
  ```
14. Lakukan konfigurasi ini pada semua worker

---

### **SOAL 15**

Markas pusat meminta laporan hasil benchmark dengan menggunakan apache benchmark dari load balancer dengan 2 web server yang berbeda tersebut dan meminta secara detail dengan ketentuan:
- Nama Algoritma Load Balancer
- Report hasil testing apache benchmark 
- Grafik request per second untuk masing masing algoritma. 
- Analisis

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)

---

### **SOAL 16**

Karena dirasa kurang aman karena masih memakai IP, markas ingin akses ke mylta memakai mylta.xxx.com dengan alias www.mylta.xxx.com (sesuai web server terbaik hasil analisis kalian)

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)

---

### **SOAL 17**

Agar aman, buatlah konfigurasi agar mylta.xxx.com hanya dapat diakses melalui port 14000 dan 14400

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)

---

### **SOAL 18**

Apa bila ada yang mencoba mengakses IP mylta akan secara otomatis dialihkan ke www.mylta.xxx.com

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)

---

### **SOAL 19**

Karena probset sudah kehabisan ide masuk ke salah satu worker buatkan akses direktori listing yang mengarah ke resource worker2

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)

---

### **SOAL 20**

Worker tersebut harus dapat di akses dengan tamat.xxx.com dengan alias www.tamat.xxx.com

**CARA PENGERJAAN**

1. 

**TESTING**


**HASIL**

(GAMBAR)
