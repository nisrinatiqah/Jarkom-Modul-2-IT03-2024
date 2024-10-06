# Laporan Resmi Praktikum Jarkom Modul-2 2024

---

### Anggota Kelompok IT 03 :

- Nicholas Arya Krisnugroho Rerangin (5027231058)
- Nisrina Atiqah Dwiputri Ridzki (5027231075)

# Daftar Isi

- [Soal 1](#soal-1)
- [Soal 2](#soal-2)
- [Soal 3](#soal-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)
- [Soal 8](#soal-8)
- [Soal 9](#soal-9)


## Topologi
![Screenshot 2024-10-03 221802](https://github.com/user-attachments/assets/f9ee3f5f-2e93-4852-827f-5940e12f8711)


---
## Soal 1
---
Untuk mempersiapkan peperangan World War MMXXIV (Iya sebanyak itu), Sriwijaya membuat dua kotanya menjadi web server yaitu Tanjungkulai, dan Bedahulu, serta Sriwijaya sendiri akan menjadi DNS Master. Kemudian karena merasa terdesak, Majapahit memberikan bantuan dan menjadikan kerajaannya (Majapahit) menjadi DNS Slave. 

### Penyelesaian
Kita buat dulu configuration nya, sebagai berikut :

**Nusantara (Router)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
   address 10.65.1.1
   netmask 255.255.255.0

auto eth2
iface eth2 inet static
   address 10.65.2.1
   netmask 255.255.255.0

auto eth3
iface eth3 inet static
   address 10.65.3.1
   netmask 255.255.255.0
```

**Sriwijaya (DNS Master)**
```
auto eth0
iface eth0 inet static
	address 10.65.2.4
	netmask 255.255.255.0
	gateway 10.65.2.1
```

**Tanjungkulai (Web Server)**
```
auto eth0
iface eth0 inet static
	address 10.65.2.3
	netmask 255.255.255.0
	gateway 10.65.2.1
```

**Bedahulu (Web Server)**
```
auto eth0
iface eth0 inet static
	address 10.65.2.2
	netmask 255.255.255.0
	gateway 10.65.2.1
```

**Kotalingga (Web Server)**
```
auto eth0
iface eth0 inet static
	address 10.65.2.7
	netmask 255.255.255.0
	gateway 10.65.2.1
```

**Srikandi (Client)**
```
auto eth0
iface eth0 inet static
	address 10.65.2.5
	netmask 255.255.255.0
	gateway 10.65.2.1
```

**Solok (Load Balancer)**
```
auto eth0
iface eth0 inet static
    address 10.65.2.2
    netmask 255.255.255.0
    gateway 10.65.2.1
```

**Samaratungga (Client)**
```
auto eth0
iface eth0 inet static
	address 10.65.1.2
	netmask 255.255.255.0
	gateway 10.65.1.1
```

**GrahamBell (Client)**
```
auto eth0
iface eth0 inet static
   address 10.65.1.4
   netmask 255.255.255.0
   gateway 10.65.1.1
```

**Mulawarman (Client)**
```
auto eth0
iface eth0 inet static
   address 10.65.1.3
   netmask 255.255.255.0
   gateway 10.65.1.1
```

**Majapahit (DNS Slave)**
```
auto eth0
iface eth0 inet static
	address 10.65.1.5
	netmask 255.255.255.0
	gateway 10.65.1.1
```

Buka web console :
1. Mengecek IP nameserver dengan menjalankan command ini di web console Router (Nusantara).
   ```
   cat /etc/resolv.conf
   ```
3. Edit file `/root/.bashrc` di semua node (kecuali Nusantara), dengan menggunakan command
   ```
   nano /root/.bashrc
   ```
4. Tambahkan command `echo 'nameserver 192.168.122.1' > /etc/resolv.conf`
5. Install `bind9` di DNS Master (Sriwijaya), dengan command 
   ```
   apt-get install bind9 -y
   ```
6. Lakukan NAT di web console node Nusantara dengan command
   ```
   iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.65.0.0/16
   ```
7. Tes PING di web console node untuk memastikan berhasil atau tidaknya


### Hasil Dokumentasi Pengerjaaan
![WhatsApp Image 2024-10-03 at 21 38 01_45db5bfc](https://github.com/user-attachments/assets/26505277-6594-43a5-8d4d-9ee838868282)

![WhatsApp Image 2024-10-03 at 21 42 01_49932ff1](https://github.com/user-attachments/assets/e43a4901-8ebc-4877-8e91-90f5649f423c)

![image](https://github.com/user-attachments/assets/ffe5360c-da6c-40c9-a105-ed8f63acf258)



---
## Soal 2
---
Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke Solok dengan alamat sudarsana.xxxx.com dengan alias www.sudarsana.xxxx.com, dimana xxxx merupakan kode kelompok. Contoh: sudarsana.it01.com.

### Penyelesaian 
1. Edit nameserver file `/etc/resolv.conf` . Isi dengan :
    ```
    nameserver 192.168.122.1
    nameserver 10.65.2.5
    ```
2. Tambahkan script kedalam file `/root/.bashrc` dengan
   ```nano /root/.bashrc```
   
    Masukkan :
    ```
    echo 'zone "sudarsana.it03.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/sudarsana.it03.com";
   };' > /etc/bind/named.conf.local
 
    mkdir /etc/bind/jarkom

    cp /etc/bind/db.local /etc/bind/jarkom/sudarsana.it03.com

    echo '
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     root.sudarsana.it03.com. admin.sudarsana.it03.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      sudarsana.it03.com.
   @       IN      A       10.65.2.6     ; IP Solok
   www     IN      CNAME   sudarsana.it03.com.' > /etc/bind/jarkom/sudarsana.it03.com
    
   service bind9 restart
    ```

3. Install BIND dengan command :
    ```apt install bind9 bind9utils bind9-doc -y```
4. Restart BIND dengan command :
    ```service bind9 restart```
5. Tes PING ke domain yang sudah ditentukan di web console node Sriwijaya dengan command :
    ```ping sudarsana.it03.com```

### Hasil Dokumentasi Pengerjaan
![Screenshot 2024-10-03 223035](https://github.com/user-attachments/assets/df4aea13-f080-4d43-99d5-1dfe3ddec042)



---
## Soal 3
---
Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu pasopati.xxxx.com dengan alias www.pasopati.xxxx.com yang mengarah ke Kotalingga. 

### Penyelesaian
1. Tambahkan script ini ke dalam `/root/.bashrc`
   ```
   echo 'zone "pasopati.it03.com" {
    type master;
    notify yes;
    file "/etc/bind/modul2/pasopati.it03.com";
   };' > /etc/bind/named.conf.local

   mkdir /etc/bind/modul2

   cp /etc/bind/db.local /etc/bind/modul2/pasopati.it03.com

   echo '
   ;
   ; BIND data file for local loopback interface
   ;
   $TTL    604800
   @       IN      SOA     pasopati.it03.com. root.pasopati.it03.com. (
                        2023101001      ; Serial
                        604800         ; Refresh
                        86400         ; Retry
                        2419200         ; Expire
                        604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      pasopati.it03.com.
   @       IN      A       10.65.2.6     ; IP Kotalingga
   www     IN      CNAME   pasopati.it03.com.' > /etc/bind/modul2/pasopati.it03.com

   service bind9 restart
   ```
   
2. Tes PING dengan command ini
   ```
   ping pasopati.it03.com
   ```
   
### Dokumentasi Hasil Pengerjaan
![Screenshot 2024-10-04 003555](https://github.com/user-attachments/assets/a0caf412-c864-45a6-990b-bc157c4b5f2b)

![Screenshot 2024-10-04 003432](https://github.com/user-attachments/assets/6b6f95a7-6b61-48cc-88b3-cdbd82470a22)



---
## Soal 4
---
Markas pusat meminta dibuatnya domain khusus untuk menaruh informasi persenjataan dan suplai yang tersebar. Informasi dan suplai meme terbaru tersebut mengarah ke Tanjungkulai dan domain yang ingin digunakan adalah rujapala.xxxx.com dengan alias www.rujapala.xxxx.com.

### Penyelesaian
1. Tambahkan script ini ke dalam `/root/.bashrc`
   ```
   echo 'zone "rujapala.it03.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/rujapala.it03.com";
   };' > /etc/bind/named.conf.local
 
    mkdir /etc/bind/jarkom

    cp /etc/bind/db.local /etc/bind/jarkom/rujapala.it03.com

    echo '
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     root.rujapala.it03.com. admin.rujapala.it03.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      rujapala.it03.com.
   @       IN      A       10.65.2.3     ; IP Tanjungkulai
   www     IN      CNAME   rujapala.it03.com.' > /etc/bind/jarkom/rujapala.it03.com
    
   service bind9 restart
   ```
   
2. Tes PING dengan command ini
   ```
   ping rujapala.it03.com
   ```
   
### Dokumentasi Hasil Pengerjaan
![Screenshot 2024-10-04 003631](https://github.com/user-attachments/assets/cfc405b1-cae3-4e1d-84e0-ab5caaae4db9)

![Screenshot 2024-10-04 003444](https://github.com/user-attachments/assets/3273a7b1-07d6-4025-ba2e-00047fb38088)



---
## Soal 5
---
Pastikan domain-domain tersebut dapat diakses oleh seluruh komputer (client) yang berada di Nusantara.

 ### Penyelesaian
Jalankan command-command ini di semua web console node client (Srikandi, Samaratungga, GrahamBell, Mulawarman)
```
ping sudarsana.it03.com
```
```
ping pasopati.it03.com
```
```
ping pasopati.it03.com
```

### Dokumentasi Hasil Pengerjaan
**Srikandi**
![Screenshot 2024-10-04 003418](https://github.com/user-attachments/assets/c04c3ce0-14c3-4d2a-9430-a272923257ed)

**Samaratungga**
![Screenshot 2024-10-04 003432](https://github.com/user-attachments/assets/2cc8eda3-f0e0-46c6-be7d-cd2f6a684a3d)

**GrahamBell**
![Screenshot 2024-10-04 003444](https://github.com/user-attachments/assets/1e6a11c1-5e77-4fa2-85a4-a68a882791a0)

**Mulawarman**
![Screenshot 2024-10-04 012403](https://github.com/user-attachments/assets/9d858263-4ef1-4e68-9c9a-c5768c832d5e)



---
## Soal 6
---
Beberapa daerah memiliki keterbatasan yang menyebabkan hanya dapat mengakses domain secara langsung melalui alamat IP domain tersebut. Karena daerah tersebut tidak diketahui secara spesifik, pastikan semua komputer (client) dapat mengakses domain pasopati.xxxx.com melalui alamat IP Kotalingga (Notes: menggunakan pointer record).

### Penyelesaian
Script :
```
echo 'zone "2.65.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.65.10.in-addr.arpa";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/2.65.10.in-addr.arpa

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it03.com. pasopati.it03.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.65.10.in-addr.arpa.    IN      NS      pasopati.it03.com.
7                      IN      PTR     pasopati.it03.com.' > /etc/bind/jarkom/2.65.10.in-addr.arpa

service bind9 restart
```



---
## Soal 7
---
Akhir-akhir ini seringkali terjadi serangan brainrot ke DNS Server Utama, sebagai tindakan antisipasi kamu diperintahkan untuk membuat DNS Slave di Majapahit untuk semua domain yang sudah dibuat sebelumnya yang mengarah ke Sriwijaya.

### Penyelesaian
Run di Majapahit :
```
echo 'zone "2.65.10.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/2.65.10.in-addr.arpa";
};' >> /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/2.65.10.in-addr.arpa

echo ';
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     pasopati.it03.com. pasopati.it03.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
2.65.10.in-addr.arpa.    IN      NS      pasopati.it03.com.
7                      IN      PTR     pasopati.it03.com.' > /etc/bind/jarkom/2.65.10.in-addr.arpa

service bind9 restart
```

Run di Sriwijaya :
```
echo '
zone "sudarsana.it03.com" {
        type master;
        notify yes;
        also-notify { 10.65.2.4; }; //IP Majapahit
        allow-transfer { 10.65.2.4; }; //IP Majapahit
        file "/etc/bind/jarkom/sudarsana.it03.com";
};

zone "pasopati.it03.com" {
        type master;
        notify yes;
        also-notify { 10.65.2.4; }; //IP Majapahit
        allow-transfer { 10.65.2.4; }; //IP Majapahit
        file "/etc/bind/jarkom/pasopati.it03.com";
};

zone "rujapala.it03.com" {
        type master;
        notify yes;
        also-notify { 10.65.2.4; }; //IP Majapahit
        allow-transfer { 10.65.2.4; }; //IP Majapahit
        file "/etc/bind/jarkom/rujapala.it03.com";
};' > /etc/bind/named.conf.local

service bind9 restart
```

---
## Soal 8
---
Kamu juga diperintahkan untuk membuat subdomain khusus melacak kekuatan tersembunyi di Ohio dengan subdomain cakra.sudarsana.xxxx.com yang mengarah ke Bedahulu.

### Penyelesaian
1. Tambahkan script ini ke dalam `/root/.bashrc`
   ```
   echo 'zone "sudarsana.it03.com" {
    type master;
    notify yes;
    file "/etc/bind/jarkom/sudarsana.it03.com";
   };' > /etc/bind/named.conf.local
 
    mkdir /etc/bind/jarkom

    cp /etc/bind/db.local /etc/bind/jarkom/sudarsana.it03.com

    echo '
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     root.sudarsana.it03.com. admin.sudarsana.it03.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
   ;
   @       IN      NS      sudarsana.it03.com.
   @       IN      A       10.65.2.6     ; IP Solok
   www     IN      CNAME   sudarsana.it03.com.
   cakra       IN      A       10.65.2.2     ; IP Bedahulu
   www     IN      CNAME   sudarsana.it03.com.' > /etc/bind/jarkom/sudarsana.it03.com
    
   service bind9 restart
   ```
   
2. Tes PING dengan command ini
   ```
   ping cakra.sudarsana.it03.com
   ```
   


---
## Soal 9
---
Karena terjadi serangan DDOS oleh shikanoko nokonoko koshitantan (NUN), sehingga sistem komunikasinya terhalang. Untuk melindungi warga, kita diperlukan untuk membuat sistem peringatan dari siren man oleh Frekuensi Freak dan memasukkannya ke subdomain panah.pasopati.xxxx.com dalam folder panah dan pastikan dapat diakses secara mudah dengan menambahkan alias www.panah.pasopati.xxxx.com dan mendelegasikan subdomain tersebut ke Majapahit dengan alamat IP menuju radar di Kotalingga.

### Penyelesaian
Run di Sriwijaya :
```
echo '
options {
        directory "/var/cache/bind";

        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;    
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

echo 'zone "panah.pasopati.it03.com" {
	type master;
	file "/etc/bind/panah/panah.pasopati.it03.com";
};' >> /etc/bind/named.conf.local

mkdir /etc/bind/panah

cp /etc/bind/db.local /etc/bind/panah/panah.pasopati.it03.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     panah.pasopati.it03.com. panah.pasopati.it03.com. (
                        2024050301      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      panah.pasopati.it03.com.
@       IN      A       10.65.2.7     ; IP Kotalingga
www     IN      CNAME   panah.pasopati.it03.com.' > /etc/bind/panah/panah.pasopati.it03.com

service bind9 restart
```
