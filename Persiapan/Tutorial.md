<p align="center">
    <img width=250 weigth=250 src="https://avatars1.githubusercontent.com/u/55744810?s=460&       u=0c5a11e08d64a6bc0d524aa0e9293052d144330d&v=4" /><br>
    <div align="center">
        <i>Dicky Mulia Fiqri</i><br>
        <i>SMKN 1 Banyuwangi</i><br><br>
    </div>
</p>

# Daftar Isi
- [Daftar Isi](#daftar-isi)
- [Tambahan](#tambahan)
  - [Router (Mikrotik)](#router-mikrotik)
    - [Merubah Jumlah TTL (Time To Live)](#merubah-jumlah-ttl-time-to-live)
    - [Menggunakan WLAN Sebagai DHCP Client](#menggunakan-wlan-sebagai-dhcp-client)
    - [Konfigurasi Login Page Hotspot Untuk MAC Address Tertentu](#konfigurasi-login-page-hotspot-untuk-mac-address-tertentu)


---
# Tambahan
## Router (Mikrotik)
### Merubah Jumlah TTL (Time To Live)
- Penjelasan  
  ```TTL ( Time To Live ) adalah yaitu nilai pada paket data yang dikirim dalam jaringan yang menyatakan berapa lama paket tersebut bisa beredar dalam jaringan. Nilai default TTL pada paket data adalah 64 dan nilai maksimum TTL adalah 255.```
- Kegunaan  
  ```TTL digunakan untuk membatasi jumlah paket yang akan diteruskan, contoh, apabila jumlah TTL adalah 1, maka apabila koneksi internet (paket) telah terkirim ke router pertama, koneksi tersebut tidak akan dapat diteruskan ke router kedua. TTL dapat difungsikan untuk mencegah pengguna agar tidak dapat melakukan sharing hotspot dengan teman atau pengguna lain.```
- Diperlukan
  - Konfigurasi DHCP Client
  - Konfigurasi DHCP Server
  - Konfigurasi Firewall (NAT)
- Langkah-Langkah
  - IP -> Firewall -> Mangle -> +
    - General -> Chain: `postrouting`
    - General -> Out. Interface: `PORT YANG TERSAMBUNG DENGAN PC/LAPTOP (DHCP SERVER)`
    - Acton -> Action: `change TTL`
    - TTL Action:
      - - [x] `change`
      - New TTL: `JUMLAH TTL YANG DIINGINKAN`
      - - [X] `passthrough`
- Cek  
  ```Ping ke cloudflare (1.1.1.1) menggunakan terminal (cmd), apabila jumlah TTL telah berubah sesuai dengan yang kita terapkan maka konfigurasi berhasil.```

### Menggunakan WLAN Sebagai DHCP Client
- Penjelasan  
  ```Menggunakan WLAN sebagai sumber koneksi internet (DHCP Client)```
- Langkah-Langkah  
  - AP Dengan Password (Apabila AP Tidak Memiliki Password Lanjutkan Ke Langkah Berikutnya)
    - Wireless -> Security Profiles -> +
      - Name: `ISIKAN NAMA PASSWORD`
      - Mode: `dynamic keys`
      - Authentication Types:
        - [x] `WPA PSK`
        - [x] `WPA2 PSK`
      - Unicast Ciphers:
        - [x] `aes ccm`
      - Group Ciphers:
        - [x] `aes ccm`
      - WPA Pre-Shared Key: `PASSWORD AP`
      - WPA2 Pre-Shared Key: `PASSWORD AP`
  - Wireless -> Interfaces -> Double Click `WLAN INTERFACE (DEFAULT: wlan1)`
    - Wireless
      - Mode: `station bridge`
      - Secutiry Profile: `NAMA PASSWORD, JIKA AP TIDAK MEMILIKI PASSWORD PILIH 'default'`
      - - [x] `Default Authenticate`
      - Scan ... (Tab Samping)
        - Pilih AP
        - Connect
  - IP -> DHCP Client -> +
    - Interface: `WLAN INTERFACE (DEFAULT: wlan1)`

### Konfigurasi Login Page Hotspot Untuk MAC Address Tertentu
- Penjelasan  
  ```Melewati login page hotspot mikrotik```
- Kegunaan  
  ```Konfigurasi ini berfungsi agar supaya pengguna tertentu tidak perlu login menggunakan 'username' dan 'password', dengan kata lain pengguna itu dapat langsung tersambung ke internet tanpa login.```
- Diperlukan
  - Konfigurasi DHCP Client
  - Konfigurasi Hotspot
  - Konfigurasi Firewall (NAT)
- Langkah-Langkah
  - Mengetahui MAC Address Perangkat
    - Terminal (CMD)
      - `ipconfig /all`
      - Wireless LAN adapter Wi-Fi:
        - Physical Address: `MAC ADDRESS PERANGKAT Wi-Fi`
    - Windows 10
      - Settings -> Network & Internet -> Wi-Fi -> Adapter Properties
        - Physical Address (MAC): `MAC ADDRESS PERANGKAT Wi-Fi`
  - IP -> Hotspot
    - IP Bindings -> +
      - MAC Address: `MAC ADDRESS PERANGKAT, CONTOH: 'E4:8D:8C:B1:86:D7'`
      - Server: `SERVER YANG AKAN DILEWATI, DEFAULT: 'all'`
      - Type: 
        - `regular`: `PENGGUNA PERLU LOGIN DENGAN USERNAME DAN PASSWORD UNTUK TERSAMBUNG KE JARINGAN INTERNET`
        - `bypassed`: `PENGGUNA AKAN LANGSUNG TERSAMBUNG JARINGAN INTERNET`
        - `blocked`: `PENGGUNA TIDAK AKAN DAPAT TERSAMBUNG KE JARINGAN INTERNET`