<p align="center">
    <img width=250 weigth=250 src="https://avatars1.githubusercontent.com/u/55744810?s=460&       u=0c5a11e08d64a6bc0d524aa0e9293052d144330d&v=4" /><br>
    <img src="https://img.shields.io/badge/Telegram-d__fordlalatina-orange?logo=Telegram&style=for-the-badge"/>
    <div align="center">
        <i>Dicky Mulia Fiqri</i><br>
        <i>SMKN 1 Banyuwangi</i>
    </div>
</p>

# Daftar Isi
- [Daftar Isi](#daftar-isi)
- [Modul A](#modul-a)
  - [Router (Mikrotik)](#router-mikrotik)
    - [Konfigurasi DHCP Client](#konfigurasi-dhcp-client)
    - [Konfigurasi DHCP Server](#konfigurasi-dhcp-server)
    - [Konfigurasi Firewall (NAT)](#konfigurasi-firewall-nat)
    - [Konfigurasi DHCP Pool](#konfigurasi-dhcp-pool)
    - [Konfigurasi DNS](#konfigurasi-dns)
    - [Konfigurasi NTP](#konfigurasi-ntp)
    - [Static Routing](#static-routing)
    - [Dynamic Routing (OSPF)](#dynamic-routing-ospf)
    - [Konfigurasi VLAN](#konfigurasi-vlan)
    - [Blok Ping IP Client Ke Router Pada Range IP Tertentu](#blok-ping-ip-client-ke-router-pada-range-ip-tertentu)
    - [Konfigurasi Logging Untuk Semua Akses Ke Router](#konfigurasi-logging-untuk-semua-akses-ke-router)
    - [Konfigurasi Akses HTTP dan HTTPS dari Client Ke Internet](#konfigurasi-akses-http-dan-https-dari-client-ke-internet)
    - [Konfigurasi AP](#konfigurasi-ap)
    - [Blok Akses Ke Situs Tertentu](#blok-akses-ke-situs-tertentu)
    - [Blok File Dengan Ekstensi Tertentu](#blok-file-dengan-ekstensi-tertentu)
    - [Blok Content Yang Mengandung Kata Tertentu](#blok-content-yang-mengandung-kata-tertentu)
    - [Konfigurasi Firewall Dengan Akses Hanya Pada Jam Tertentu](#konfigurasi-firewall-dengan-akses-hanya-pada-jam-tertentu)
    - [Konfigurasi Hotspot](#konfigurasi-hotspot)
- [Tambahan](#tambahan)
  - [Router (Mikrotik)](#router-mikrotik-1)
    - [Merubah Jumlah TTL (Time To Live)](#merubah-jumlah-ttl-time-to-live)
    - [Menggunakan WLAN Sebagai DHCP Client](#menggunakan-wlan-sebagai-dhcp-client)
    - [Konfigurasi Login Page Hotspot Untuk MAC Address Tertentu](#konfigurasi-login-page-hotspot-untuk-mac-address-tertentu)


---
# Modul A
## Router (Mikrotik)
### Konfigurasi DHCP Client
- Penjelasan  
  ```Menggunakan Mikrotik sebagai DHCP Client agar dapat mendistribusikan koneksi internet dari ISP.```
- Langkah-Langkah
  - IP -> DHCP Client -> +
    - Interface: `PORT INTERFACE YANG HENDAK DIGUNAKAN`
    - - [x] Use Peer DNS --> `MIKROTIK AKAN MENGGUNAKAN KONFIGURASI DNS DARI PUSAT`
    - - [x] Use Peer NTP --> `MIKROTIK AKAN MENGGUNAKAN KONFIGURASI NTP DARI PUSAT`
    - Add Default Route: `yes`
    - Default Route Distance: `1`

### Konfigurasi DHCP Server
- Penjelasan  
  ```Menggunakan Mikrotik sebagai DHCP Server untuk mendistribusikan koneksi internet dari DHCP Client.```
- Langkah-Langkah
  - IP -> Addresses -> +
    - Address: `IP NETWORK YANG AKAN DIGUNAKAN, CONTOH: '192.168.1.1/24'`
    - Interface: `PORT YANG AKAN DIGUNAKAN SEBAGAI DHCP SERVER`
  - IP -> DHCP Server -> DHCP Setup
    - DHCP Server Interface: `PORT YANG AKAN DIGUNAKAN SEBAGAI DHCP SERVER`
    - DHCP Address Space: `SESUAI DENGAN KONFIGURASI NETWORK ADDRESS PADA PORT INTERFACE`
    - Gateway for DHCP Network: `SESUAI DENGAN KONFIGURASI ADDRESS PADA PORT INTERFACE`
    - Addresses to Give Out: `RANGE ADDRESS YANG AKAN DIBERIKAN/DIDISTRIBUSIKAN`
    - DNS Server: `DNS YANG DIBERIKAN OLEH SERVER/PUSAT, DEFAULT: KOSONG`
    - Lease Time: `BERAPA LAMA IP AKAN DIPINJAMKAN KEPADA PERANGKAT PENGGUNA`

### Konfigurasi Firewall (NAT)
- Penjelasan  
  ```Konfigurasi ini digunakan agar jaringan LAN dapat mengakses internet.```
- Langkah-Langkah
  - IP -> Firewall -> NAT -> +
    - Chain: `srcnat`
    - Out. Interface: `PORT INTERFACE DHCP CLIENT`
    - Action
      - Action: `masquerade`

### Konfigurasi DHCP Pool
- Penjelasan  
  ```DHCP Pool adalah jarak/range IP yang akan digunakan dalam DHCP Server.```
- Diperlukan
  - DHCP Server
- Langkah-Langkah
  - IP -> DHCP Server -> Double Click `PORT INTERFACE YANG DIGUNAKAN`
    - Address Pool: `DHCP POOL YANG DIGUNAKAN`
  - IP -> Pool
    - Double Click `DHCP POOL YANG DIGUNAKAN ATAU HENDAK DIRUBAH`
      - Addresses: `RENTANG IP YANG AKAN DIGUNAKAN, CONTOH: '192.168.1.2-192.168.1.254'`

### Konfigurasi DNS
- Penjelasan  
  ```Digunakan agar Mikrotik dapat mengartikan nama domain ke bentuk IP.```
- Diperlukan
  - DHCP Client
- Langkah-Langkah
  - IP -> DNS
    - Servers: `SERVER DNS YANG HENDAK DIGUNAKAN`
    - - [x] Allow Remote Requests
    - 5 Daftar Server DNS
      - Cloudflare
        - Primer: `1.1.1.1`
        - Sekunder: `1.0.0.1`
      - Public DNS Google
        - Primer: `8.8.8.8`
        - Sekunder: `8.8.4.4`
      - OpenDNS
        - Primer: `208.67.222.222`
        - Sekunder: `208.67.220.220`
      - Norton ConnectSafe
        - Primer: `199.85.126.10`
        - Sekunder: `199.85.127.10`
      - Comodo Secure DNS
        - Primer: `8.26.56.26`
        - Sekunder: `8.20.247.20`

### Konfigurasi NTP
- Penjelasan  
  ```Digunakan agar Mikrotik dapat melakukan sinkronisasi zona waktu dengan server NTP (Network Time Protocol).```
- Kegunaan  
  ```Mikrotik akan memiliki waktu yang sesuai.```
- Diperlukan
  - DHCP Client
- Langkah-Langkah
  - System -> SNTP Client
    - - [x] Enabled
    - Primary NTP Server: `SERVER NTP PRIMER`
    - Secondary NTP Server: `SERVER NTP SEKUNDER`
    - Daftar Server NTP Indonesia
      - 0.id.pool.ntp.org: `203.114.74.17`
      - 1.id.pool.ntp.org: `162.159.200.1`
      - 2.id.pool.ntp.org: `203.114.224.31`
      - 3.id.pool.ntp.org: `202.162.32.12`

### Static Routing
- Penjelasan 
  ```Static Routing atau Routing Statik adalah salah satu cara routing dimana si administrator jaringan mensetting router mereka dengan cara yang manual. Dalam sebuah jaringan yang murni statik routing berarti si administrator jaringan mengisi semua forwarding table di setiap router secara manual. Biasa nya metode static routing ini digunakan pada sebuah jaringan yang berskala kecil, karena jika metode ini di gunakaLn pada jaringan berskala besar akan repot juga mengisi semua forwarding table di setiap router yang banyak itu. Untuk konfigurasi sederhananya bisa dilihat pada tutorial dibawah ini.```
- Kegunaan
  ```Memakan lebih sedikit resources pada mikrotik dibandingkan dengan Dynamic Routing```
- Diperlukan
  - 2 Mikrotik Router atau lebih
- Langkah - langkah
  - Tambahkan IP Address 
    - IP -> Address -> tambahkan ip address sesuai kebutuhan pada masing masing port router
  - Mengatur tabel routes
    - IP -> Routes -> tambahkan tabel routes baru (+) pada router satu dan dua
      - Dst Address adalah ip network tujuan yang akan dihubungkan
      - Gateway adalah jalan masuk (gerbang) untuk menuju ke tujuan (dst address)
    - Pastikan setelah konfigurasi diatas ip table tertulis reachable yang menandakan bahwa tersambung
    - Langkah terakhir cek menggunakan terminal atau cmd ping ip tujuan apabila muncul reply diikuti nilai ms maka setting telah berhasil.
  
### Dynamic Routing (OSPF)
- Penjelasan 
  ```Open Shortest Path First (OSPF) adalah sebuah protokol routing otomatis (Dynamic Routing) yang mampu menjaga, mengatur dan mendistribusikan informasi routing antar network mengikuti setiap perubahan jaringan secara dinamis. Pada OSPF dikenal sebuah istilah Autonomus System (AS) yaitu sebuah gabungan dari beberapa jaringan yang sifatnya routing dan memiliki kesamaan metode serta policy pengaturan network, yang semuanya dapat dikendalikan oleh network administrator. Dan memang kebanyakan fitur ini diguakan untuk management dalam skala jaringan yang sangat besar. Oleh karena itu untuk mempermudah penambahan informasi routing dan meminimalisir kesalahan distribusi informasi routing, maka OSPF bisa menjadi sebuah solusi.```
  
- Diperlukan
  - 2 Mikrotik Router atau lebih
- Manfaat
  - Memudahkan administrator jaringan dalam menkonfigurasi karena tidak harus manual seperti Static Routing
- Kekurangan
  - Lebih memakan resources mikrotik karena jalur yang dipilih akan dibebankan kepada CPU mikrotik
  
- Konfigurasi dasar OSPF
  - Tambahkan IP Address 
    - IP -> Address -> tambahkan ip address sesuai kebutuhan pada masing masing port router
  - Konfigurasi OSPF
    - Routing -> OSPF
    - Klik pada tab network, lalu tambahkan ip network yang berada di sekitar router
  - Setelah itu cek pada route list, apabila ip network yang telah ditulis sebelumnya sudah terdaftar dan reachable menandakan konfigurasi telah berhadil.
### Konfigurasi VLAN
- Penjelasan  
  ```Virtual LAN atau disingkat VLAN merupakan fitur yang dibuat dengan menggunakan jaringan pihak ketiga. Dengan VLan ini kita dapat mengkonfigurasikan beberapa perangkat pada satu LAN atau lebih agar dapat saling berkomunikasi seperti halnya bila perangkat tersebut terhubung langsung pada jalur yang sama, padahal sebenarnya perangkat tersebut berada dalam segmen jaringan LAN yang berbeda.```
- Diperlukan
  - 2 Buah Router Mikrotik
- Langkah-Langkah
  - Router 1
    - Interfaces -> VLAN -> +
      - Name: `NAMA VLAN`
      - VLAN ID: `ID VLAN, CONTOH: '10'`
      - Interface: `PORT INTERFACE YANG AKAN DIGUNAKAN UNTUK MENDISTRIBUSIKAN VLAN`
    - IP -> Addresses -> +
      - Konfigurasi Sama Dengan [DHCP Server](#konfigurasi-dhcp-server)
      - Interface: `PORT INTERFACE VLAN YANG AKAN DIGUNAKAN, CONTOH: 'vlan1'`
  - Router 2
    - Interfaces -> Interface -> Double Click `PORT YANG AKAN MENDISTRIBUSIKAN NETWORK DARI VLAN`
      - General
        - Master Port: `PORT YANG TERSAMBUNG DENGAN VLAN`
    - Switch -> Port
      - Double Click `PORT YANG TERSAMBUNG DENGAN VLAN`
        - VLAN Mode: `secure`
        - VLAN Header: `add if missing`
      - Double Click `PORT YANG AKAN MENDISTRIBUSIKAN NETWORK DARI VLAN`
        - VLAN Mode: `secure`
        - VLAN Header: `always strip`
        - Default VLAN ID: `VLAN ID DARI ROUTER 1, CONTOH: '10'`
    - Switch -> VLAN -> +
      - VLAN ID: `VLAN ID DARI ROUTER 1`
      - Ports: `PORT-PORT YANG DIGUNAKAN, CONTOH: 'ether1 dan ether2'`
        - Penjelasan  
        ```ether1 berfungsi sebagai trunk atau master port yang mengirimkan VLAN dari Router 1, sedangkan ether2 bertindak sebagai penerima Network dari ether1 dengan ID VLAN yang ditentukan pengguna untuk kemudian didistribusikan ke perangkat client```

### Blok Ping IP Client Ke Router Pada Range IP Tertentu
- Penjelasan  
  ```Melakukan drop atau menolak koneksi dari IP atau range IP tertentu```
- Langkah-Langkah
  - IP -> Firewall -> Filter Rules -> +
    - General
      - Chain: `forward`
      - Dst. Address: `IP ATAU RANGE IP YANG AKAN DITOLAK KONEKSINYA, CONTOH: '192.168.1.10-192.168.1.50'`
    - Action
      - Action: `drop`

### Konfigurasi Logging Untuk Semua Akses Ke Router
- Penjelasan  
  ```Logging ditujukan agar supaya admin dapat mengetahui proses, status, warning, dan error apa saja yang terjadi pada perangkat```
- Kegunaan  
  ```Digunakan untuk mengetahui riwayat akses pengguna ke Router```
- Langkah-Langkah
  - System -> Logging -> Rules -> +
    - Topics: `account`, dan `system`
    - Action: `disk`
    - Penjelasan
      ```Semua proses sistem yang tercakup dalam kategori 'account' dan 'system' akan disimpan sebagai log dalam bentuk file di penyimpanan Router. File dapat ditemukan pada 'Files' dengan nama file 'log.x.txt'```

### Konfigurasi Akses HTTP dan HTTPS dari Client Ke Internet
- Diperlukan
  - DHCP Server
- Langkah-Langkah
  - System -> Certificates -> +
    - General
      - Name: `NAMA SERTIFIKAT UTAMA, CONTOH: 'root-cert'`
      - Common Name: `NAMA PENGENAL/UMUM, CONTOH: 'MyRouter'`
      - Subject Alt. Name: `IP`
      - Key Size: `2048`
      - Days Valid: `365`
    - Key Usage
      - - [x] `key cert. sign`
      - - [x] `crl sign`
    - Apply (Tab Samping)
    - Sign (Tab Samping)
      - Certificate: `NAMA SERTIFIKAT YANG AKAN DIDAFTARKAN, CONTOH: 'root-cert'`
      - Sign
  - System -> Certificate -> +
    - General
      - Name: `NAMA SERTIFIKAT HTTPS, CONTOH: 'https-cert'`
      - Common Name: `NAMA PENGENAL/UMUM, CONTOH: 'MyRouter'`
      - Subject Alt. Name: `IP`
      - Key Size: `2048`
      - Days Valid: `365`
    - Apply (Tab Samping)
    - Sign (Tab Samping)
      - Certificate: `NAMA SERTIFIKAT YANG AKAN DIDAFTARKAN, CONTOH: 'https-cert'`
      - CA: `NAMA SERTIFIKAT UTAMA, CONTOH: 'root-cert'`
      - Sign
  - IP -> Services
    - Double Click `www`
      - Disable
    - Double Click `www-ssl`
      - Certificate: `SERTiFIKAT UMTUK HTTPS, CONTOH: 'https-cert'`
      - Enable
- Cek
  ```Akses Router Mikrotik menggunakan web browser dengan cara memasukkan IP Address Mikrotik ke bar address browser, apabila pada bar address browser tersebut terdapat tulisan 'https' maka konfigurasi berhasil```

### Konfigurasi AP
- Penjelasan  
  ```Konfigurasi AP (Access Point) pada Router Mikrotik```
- Diperlukan
  - DHCP Client
  - Firewall (NAT)
  - DNS
- Langkah-Langkah
  - Wireless
    - Security Profiles -> + (Apabila Ingin Membuat AP Tanpa Password Lanjut Ke Langkah Selanjutnya)
      - Name: `NAMA PASSWORD`
      - Mode: `dynamic keys`
      - Authentication Types
        - - [x] `WPA PSK`
        - - [x] `WPA2 PSK`
      - Unicast Ciphers
        - - [x] `aes ccm`
      - Group Ciphers
        - - [x] `aes ccm`
      - WPA Pre-Shared Key: `PASSWORD YANG INGIN DIGUNAKAN`
      - WPA2 Pre-Shared Key: `PASSWORD YANG INGIN DIGUNAKAN`
    - Interfaces -> Double Click `INTERFACE WLAN, CONTOH: 'wlan1'`
      - Wireless
        - Mode: `ap bridge`
        - SSID: `NAMA AP`
        - Security Profile: `PASsWORD YANG INGIN DIGUNAKAN, DEFAULT JIKA TIDAK ADA PASSWORD`
  - Konfigurasi [DHCP Server](#konfigurasi-dhcp-server)
    - Interface: `INTERFACE WLAN, CONTOH: 'wlan1'`

### Blok Akses Ke Situs Tertentu
- Penjelasan  
  ```Tolak akses ke situs web tertentu```
- Diperlukan
  - DHCP Client
  - DHCP Server
  - Firewall (NAT)
  - DNS
- Langkah-Langkah
  - IP -> Web Proxy
    - - [x] `Enabled`
    - Src. Address: `0.0.0.0`
    - Port: `8080`
  - IP -> Firewall
    - NAT -> +
      - General
        - Chain: `dstnat`
        - Protocol: `tcp`
        - Dst. Port: `80`
      - Action
        - Action: `redirect`
        - To Ports: `8080`
    - Filter Rules
      - General
        - Chain: `input`
        - Protocol: `tcp`
        - Dst. Address: `8080`
        - In. Interface: `PORT DHCP CLIENT`
      - Action
        - Action: `drop`
  - IP -> Web Proxy
    - Access (Tab Samping) -> +
      - Dst. Host: `SITUS YANG AKAN DIBLOKIR`
      - Action: `deny`
- Tambahan
  - Blok Akses Ke Situs Dengan Port 80 dan 443 (HTTP dan HTTPS)
    - IP -> Firewall
      - Address Lists -> +
        - Name: `NAMA GRUP YANG BERISIKAN DAFTAR SITUS YANG AKAN DIBLOKIR, CONTOH: 'BlockedWeb'`
        - Address: `ALAMAT WEB YANG AKAN DIBLOKIR, CONTOH: 'github.com'`
      - Filter Rules -> +
        - General
          - Chain: `forward`
          - Protocol: `tcp`
          - Dst. Port: `80, 443`
        - Advanced
          - Dst. Address List: `NAMA GRUP SITUS YANG DIBLOKIR`
        - Action
          - Action: `drop`

### Blok File Dengan Ekstensi Tertentu
- Penjelasan  
  ```Konfigurasi untuk memblokir atau menolak file dengan ekstensi tertentu```
- Kegunaan  
  ```Membatasi pengguna agar tidak mengunduh file sembarangan dari internet```
- Diperlukan
  - DHCP Client
  - DHCP Server
  - Firewall (NAT)
  - DNS
  - Konfigurasi [Firewall Untuk Menolak Akses Situs](#blok-akses-ke-situs-tertentu)
- Langkah-Langkah
  - IP -> Web Proxy -> Access (Tab Samping) -> +
    - Path: `EKSTENSI FILE YANG AKAN DIBLOKIR, CONTOH: '*.mp4, *.exe'`
    - Action: `deny`

### Blok Content Yang Mengandung Kata Tertentu
- Penjelasan  
  ```Menolak akses ke internet yang mengandung kata tertentu```
- Kegunaan  
  ```Mencegah pengguna untuk mengakses situs yang memiliki/mengandung kata tertentu yang telah ditentukan```
- Diperlukan
  - DHCP Server
  - DHCP Client
  - Firewall (NAT)
  - DNS
  - Konfigurasi [Firewall Untuk Menolak Akses Situs](#blok-akses-ke-situs-tertentu)
- Langkah-Langkah
  - IP -> Web Proxy -> Access (Tab Samping) -> +
    - Dst. Host: `KATA YANG AKAN DITOLAK, CONTOH: ':game, :pedia'`
    - Action: `deny`

### Konfigurasi Firewall Dengan Akses Hanya Pada Jam Tertentu
- Penjelasan  
  ```Konfigurasi untuk menolak akses internet pada jam yang telah ditentukan```
- Diperlukan
  - DHCP Client
  - DHCP Server
  - Firewall (NAT)
  - DNS
- Langkah-Langkah
  - IP -> Firewall
    - Filter Rules -> +
      - General
        - Chain: `forward`
      - Extra
        - Time
          - `KONFIGURASI DISESUAIKAN OLEH PENGGUNA`
      - Action
        - Action: `drop`

### Konfigurasi Hotspot
- Penjelasan  
  ```Berisi beberapa konfigurasi pada halaman login hotspot mikrotik```
- Dibutuhkan
  - DHCP Client
  - Firewall (NAT)
  - DNS
  - Sertifikat [HTTPS](#konfigurasi-akses-http-dan-https-dari-client-ke-internet)
- Langkah-Langkah
  - Membuat Hotspot
    - IP -> Addresses -> +
      - Address: `IP ADDRESS UNTUK HOTSPOT, CONTOH: '192.168.1.1/24'`
      - Interface: `PORT INTERFACE YANG AKAN DIGUNAKAN UNTUK HOTSPOT`
    - IP -> Hotspot
      - Servers -> Hotspot Setup
        - Hotspot Interface: `PORT INTERFACE HOTSPOT`
        - Local Address of Network: `ALAMAT IP HOTSPOT`
          - - [ ] `Masquerade Network`
        - Address Pool of Network: `JARAK/RANGE IP YANG AKAN DIBERIKAN`
        - Select Certificate: `none`
        - IP Address of SMTP Server: `0.0.0.0`
        - DNS Server: `SERVER DNS, DEFAULT: 'KOSONG'`
        - DNS Name: `NAMA DOMAIN YANG INGIN DIGUNAKAN UNTUK MENGAKSES HALAMAN LOGIN HOTSPOT, DEFAULT: 'KOSONG'`
  - Merubah Domain Hotspot
    - IP -> DNS -> Static (Tab Samping) -> +
      - Name: `NAMA DOMAIN YANG DIINGINKAN, CONTOH: 'siswatkj.org.id'`
      - Address: `IP Address Hotspot, CONTOH: '192.168.1.1'`
    - IP -> Hotspot
      - Servers -> Double Click `HOTSPOT YANG DIBUAT PADA LANGKAH PERTAMA`
        - Profile: `NAMA PROFILE SERVER YANG DIGUNAKAN HOTSPOT`
      - Server Profiles -> Double Click `NAMA PROFILE SERVER YANG DIGUNAKAN HOTSPOT`
        - General
          - DNS Name: `NAMA DOMAIN YANG DITENTUKAN PADA TAHAP AWAL, CONTOH: 'siswatkj.org.id'`
        - Login
          - -[x] `HTTPS`
          - SSL Certificate: `SERTIFIKAT HTTPS, CONTOH: 'https-cert'`
  - Menambah User
    - IP -> Hotspot
      - Users -> +
        - Server: `USER AKAN AKTIF DI SERVR YANG DIPILIH, DEFAULT: 'all'`
        - Name: `USERNAME`
        - Password: `PASSWORD`
        - Profile: `default`
  - Membatasi User
    - IP -> Hotspot
      - Users -> Double Click `USER YANG AKAN DIBATASI`
        - Profile: `PROFIL YANG DIGUNAKAN USER`
      - User Profiles -> Double Click `PROFIL YANG DIGUNAKAN USER`
        - Shared Users: `JUMLAH PERANGKAT YANG DAPAT MENGGUNAKAN USER INI`
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
  ```Konfigurasi login page untuk pengguna tertentu dengan MAC Address sebagai pengenal```
- Kegunaan  
  ```Konfigurasi ini berfungsi agar supaya pengguna tertentu tidak perlu login menggunakan 'username' dan 'password', atau mencegah pengguna untuk tersambung.```
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