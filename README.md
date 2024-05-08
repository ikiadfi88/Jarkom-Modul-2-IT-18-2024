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

### **SOAL 2**

Karena para pasukan membutuhkan koordinasi untuk mengambil airdrop, maka buatlah sebuah domain yang mengarah ke Stalber dengan alamat airdrop.xxxx.com dengan alias www.airdrop.xxxx.com dimana xxxx merupakan kode kelompok. Contoh : airdrop.it01.com

**CARA PENGERJAAN**

1. Pada pengerjaan ini saya menggunakan script bash soal2.sh. Gunakan command `nano soal2.sh`, lalu masukkan kode bash di bawah ini.

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



