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
- [Soal 10](#soal-10)



---
## Soal 1
---
Untuk mempersiapkan peperangan World War MMXXIV (Iya sebanyak itu), Sriwijaya membuat dua kotanya menjadi web server yaitu Tanjungkulai, dan Bedahulu, serta Sriwijaya sendiri akan menjadi DNS Master. Kemudian karena merasa terdesak, Majapahit memberikan bantuan dan menjadikan kerajaannya (Majapahit) menjadi DNS Slave. 

### Penyelesaian
**Nusantara (Router)**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.168.1.2
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.168.2.2
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.168.3.2
	netmask 255.255.255.0
```

**Sriwijaya (DNS Master)**
```
auto eth0
iface eth0 inet static
	address 192.168.0.2
	netmask 255.255.255.0
	gateway 192.168.0.1
```

**Majapahit (DNS Slave)**
```
auto eth0
iface eth0 inet static
	address 192.168.0.2
	netmask 255.255.255.0
	gateway 192.168.0.1
```

### Dokumentasi Pengerjaaan



---
## Soal 2
---
Karena para pasukan membutuhkan koordinasi untuk melancarkan serangannya, maka buatlah sebuah domain yang mengarah ke Solok dengan alamat sudarsana.xxxx.com dengan alias www.sudarsana.xxxx.com, dimana xxxx merupakan kode kelompok. Contoh: sudarsana.it01.com.

### Penyelesaian 


### Dokumentasi Pengerjaan





---
## Soal 3
---
Para pasukan juga perlu mengetahui mana titik yang akan diserang, sehingga dibutuhkan domain lain yaitu pasopati.xxxx.com dengan alias www.pasopati.xxxx.com yang mengarah ke Kotalingga. 

### Penyelesaian


### Dokumentasi Pengerjaan



