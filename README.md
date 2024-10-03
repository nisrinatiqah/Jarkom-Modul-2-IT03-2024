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


## Topologi
![alt text](image.png)


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
    address 10.65.2.5
    netmask 255.255.255.0
    gateway 10.65.2.1
```

**Tanjungkulai (Web Server)**
```
auto eth0
iface eth0 inet static
    address 10.65.2.6
    netmask 255.255.255.0
    gateway 10.65.2.1
```

***Bedahulu (Web Server)**
```
auto eth0
iface eth0 inet static
    address 10.65.2.7
    netmask 255.255.255.0
    gateway 10.65.2.1
```

***Kotalingga (Web Server)**
```
auto eth0
iface eth0 inet static
    address 10.65.2.4
    netmask 255.255.255.0
    gateway 10.65.2.1
```

**Srikandi (Client)**
```
auto eth0
iface eth0 inet static
    address 10.65.2.3
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
    address 10.65.1.5
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
   address 10.65.1.2
   netmask 255.255.255.0
   gateway 10.65.1.1
```

Buka web console :
1. Mengecek IP nameserver dengan menjalankan command ini di web console Router (Nusantara).
```cat /etc/resolv.conf```
2. Edit file `/root/.bashrc` di semua node (kecuali Nusantara), dengan menggunakan command
```nano /root/.bashrc```
3. Tambahkan command `echo 'nameserver 192.168.122.1' > /etc/resolv.conf`
4. Install `bind9` di DNS Master (Sriwijaya), dengan command 
```apt-get install bind9 -y```
5. Lakukan NAT di web console node Nusantara dengan command
```iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.65.0.0/16```


### Hasil & Dokumentasi Pengerjaaan
!![WhatsApp Image 2024-10-03 at 21 38 01_45db5bfc](https://github.com/user-attachments/assets/26505277-6594-43a5-8d4d-9ee838868282)
!![WhatsApp Image 2024-10-03 at 21 42 01_49932ff1](https://github.com/user-attachments/assets/e43a4901-8ebc-4877-8e91-90f5649f423c)



---
## Soal 2
---
Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke Solok dengan alamat sudarsana.xxxx.com dengan alias www.sudarsana.xxxx.com, dimana xxxx merupakan kode kelompok. Contoh: sudarsana.it01.com.

### Penyelesaian 
1. Edit nameserver file `/etc/resolv.conf` . Isi dengan :
    ```nameserver 192.168.122.1
       nameserver 10.65.2.5
    ```
2. Tambahkan script kedalam file `/root/.bashrc`
    ```nano /root/.bashrc```
    Masukkan :
    ```
    # Konfigurasi zona DNS di named.conf.local
echo 'zone "sudarsana.it03.com" {
    type master;
    file "/etc/bind/jarkom/sudarsana.it03.com";
};' > /etc/bind/named.conf.local

    # Membuat direktori jika belum ada
    mkdir -p /etc/bind/jarkom

    # Menyalin template file db.local ke file zona baru
    cp /etc/bind/db.local /etc/bind/jarkom/sudarsana.it03.com

    # Menulis konfigurasi zona DNS
    echo '
    ;
    ; BIND data file for local loopback interface
    ;
    $TTL    604800
    @       IN      SOA     root.sudarsana.it03.com. admin.sudarsana.it03.com. (
                        2024100312      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      sudarsana.it03.com.
@       IN      A       10.65.2.2     ; IP Solok
www     IN      CNAME   sudarsana.it03.com.' > /etc/bind/jarkom/sudarsana.it03.com

    # Restart service BIND
sudo service bind9 restart
    ```

3. Install BIND dengan command :
    ```apt install bind9 bind9utils bind9-doc -y```
4. Restart BIND dengan command :
    ```service bind9 restart```
5. Tes PING ke domain yang sudah ditentukan di web console node Sriwijaya dengan command :
    ```ping sudarsana.it03.com```

### Dokumentasi Pengerjaan
![alt text](image-1.png)




---
## Soal 3
---
Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu pasopati.xxxx.com dengan alias www.pasopati.xxxx.com yang mengarah ke Kotalingga. 

### Penyelesaian


### Dokumentasi Pengerjaan



