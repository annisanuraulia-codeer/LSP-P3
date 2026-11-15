# KONFIGURASI NETWORK MIKROTIK DAN PENGUJIAN JARINGAN UKOM

Dokumentasi praktik konfigurasi jaringan menggunakan MikroTik, mulai dari pengaturan dasar router, DHCP Server, NAT, DNS, WiFi, Hotspot, Simple Queue, hingga pengujian koneksi jaringan.

---

## 👤 Identitas

| Keterangan | Informasi |
|:---|:---|
| **Nama** | Annisa Nur Aulia |
| **Nomor Absen** | 3 |
| **Materi** | Konfigurasi MikroTik dan Ubuntu Server |
| **Kegiatan** | UKOM TKJ 2026 |

---

## 📑 Daftar Isi

1. [Persiapan Router MikroTik](#1-persiapan-router-mikrotik)
   - [A. Mengatur IP Address Laptop](#a-mengatur-ip-address-pada-laptop)
   - [B. Reset Configuration](#b-reset-configuration-mikrotik)
   - [C. System Identity](#c-mengatur-system-identity)
   - [D. DHCP Client](#d-membuat-dhcp-client)
   - [E. Nama Interface](#e-mengubah-nama-interface)
   - [F. IP Address](#f-menambahkan-ip-address)
2. [Konfigurasi DHCP Server](#2-konfigurasi-dhcp-server-pada-ether2)
3. [Konfigurasi NAT](#3-konfigurasi-nat)
4. [Pengaturan DNS MikroTik](#4-pengaturan-dns-mikrotik)
5. [Konfigurasi WiFi](#5-konfigurasi-wifi)
6. [Konfigurasi Hotspot](#6-konfigurasi-hotspot)
7. [Pengujian Hotspot dan Speedtest](#7-pengujian-koneksi-hotspot-dan-speedtest)
8. [Konfigurasi Simple Queue](#8-konfigurasi-simple-queue-untuk-client-kabel)
9. [Mengatur IP Laptop Otomatis](#9-mengatur-ip-laptop-menjadi-otomatis)
10. [Pengujian Client Kabel](#10-pengujian-kecepatan-client-kabel-dan-speedtest)
11. [Ringkasan Konfigurasi](#11-ringkasan-konfigurasi)

---

## 1. Persiapan Router MikroTik

### A. Mengatur IP Address pada Laptop

Sebelum melakukan konfigurasi MikroTik, laptop harus memiliki IP Address yang satu jaringan dengan IP default MikroTik agar dapat terhubung melalui aplikasi WinBox.

**Langkah-langkah:**

1. Buka **Control Panel** pada Windows.
2. Pilih **Network and Internet → Network and Sharing Center**.
3. Klik **Change adapter settings**.
4. Klik kanan pada Ethernet, kemudian pilih **Properties**.
5. Pilih **Internet Protocol Version 4 (TCP/IPv4)**.
6. Atur IP Address berikut:

| Pengaturan | Nilai |
|:---|:---|
| IP Address | `192.168.88.2` |
| Subnet Mask | `255.255.255.0` |
| Default Gateway | Kosong |
| DNS Server | Kosong |

Pengaturan ini digunakan karena IP default MikroTik umumnya berada pada jaringan `192.168.88.0/24`. Dengan menggunakan jaringan yang sama, laptop dapat berkomunikasi dengan router saat konfigurasi awal.

### B. Reset Configuration MikroTik

**Langkah-langkah:**

1. Buka aplikasi **WinBox**.
2. Hubungkan laptop ke MikroTik.
3. Masuk ke menu **System → Reset Configuration**.
4. Klik **Reset Configuration** dan tunggu hingga router melakukan restart.

Reset Configuration berfungsi menghapus konfigurasi sebelumnya agar router kembali ke kondisi awal.

Langkah ini penting untuk menghindari konflik konfigurasi lama, seperti IP Address, DHCP Server, Firewall, dan pengaturan lainnya yang dapat mengganggu konfigurasi baru.

### C. Mengatur System Identity

**Langkah-langkah:**

1. Buka menu **System → Identity**.
2. Ubah nama router menjadi `R-UKOM3`.
3. Klik **Apply**, kemudian **OK**.

System Identity merupakan nama identitas router yang digunakan untuk membedakan satu MikroTik dengan MikroTik lainnya.

Penggunaan nama ini mempermudah pengelolaan router, terutama ketika terdapat beberapa perangkat MikroTik dalam satu jaringan.

### D. Membuat DHCP Client

**Langkah-langkah:**

1. Buka menu **IP → DHCP Client**.
2. Klik tombol tambah (+).
3. Pada bagian Interface, pilih `ether1`.
4. Klik **Apply**, kemudian **OK**.
5. Tunggu hingga status berubah menjadi `bound`.

DHCP Client berfungsi agar MikroTik mendapatkan IP Address secara otomatis dari perangkat ISP atau jaringan yang terhubung ke ether1.

**Mengapa menggunakan DHCP Client?**

Karena interface ether1 digunakan sebagai jalur WAN atau koneksi menuju internet. Dengan DHCP Client, router tidak perlu memasukkan IP Address secara manual apabila jaringan ISP menyediakan layanan DHCP.

Status `bound` menunjukkan bahwa MikroTik berhasil mendapatkan konfigurasi IP dari jaringan yang terhubung.

Jika tidak menggunakan DHCP Client, IP Address WAN harus diatur secara manual sesuai informasi dari ISP atau jaringan sumber.

### E. Mengubah Nama Interface

Masuk ke menu **Interfaces**, kemudian ubah nama interface sesuai pembagian berikut:

| Interface | Nama Baru | Fungsi |
|:---|:---|:---|
| `ether1` | ISP | Menghubungkan MikroTik ke jaringan internet |
| `ether2` | CLIENT | Menghubungkan laptop atau perangkat client |
| `ether3` | SERVER | Menghubungkan Ubuntu Server |
| `wlan1` | HOTSPOT | Menyediakan jaringan WiFi untuk pengguna |

Penggantian nama interface bertujuan agar setiap port lebih mudah dikenali berdasarkan fungsinya.

Hal ini juga mengurangi kesalahan ketika melakukan konfigurasi DHCP Server, IP Address, NAT, dan Hotspot.

### F. Menambahkan IP Address

**Langkah-langkah:**

1. Buka menu **IP → Addresses**.
2. Klik tombol tambah (+).
3. Masukkan IP Address sesuai interface berikut:

| Interface | IP Address |
|:---|:---|
| ISP (`ether1`) | DHCP dari ISP |
| CLIENT (`ether2`) | `10.10.3.1/28` |
| SERVER (`ether3`) | `100.100.3.1/30` |
| HOTSPOT (`wlan1`) | `192.168.3.1/24` |

**Penjelasan:**

- `10.10.3.1/28` digunakan sebagai gateway untuk jaringan client kabel.
- `100.100.3.1/30` digunakan sebagai gateway untuk jaringan server. Subnet /30 menyediakan dua alamat IP yang dapat digunakan perangkat.
- `192.168.3.1/24` digunakan sebagai gateway untuk jaringan WiFi Hotspot.

IP Address pada setiap interface harus berbeda jaringan agar masing-masing segmen dapat dikenali dan dikelola oleh MikroTik.

Jika tidak diberikan IP Address, perangkat yang terhubung pada interface tersebut tidak memiliki gateway untuk berkomunikasi melalui router.

---

## 2. Konfigurasi DHCP Server pada ether2

DHCP Server berfungsi membagikan IP Address secara otomatis kepada perangkat client yang terhubung melalui interface ether2.

Dengan DHCP Server, pengguna tidak perlu memasukkan IP Address secara manual pada setiap perangkat.

### Langkah-langkah konfigurasi

1. Buka menu **IP → DHCP Server**.
2. Klik **DHCP Setup**.
3. Pilih interface `ether2`.
4. Masukkan DHCP Address Space: `10.10.3.0/28`.
5. Masukkan Gateway: `10.10.3.1`.
6. Atur Address Pool menjadi `10.10.3.3-10.10.3.14`.
7. Tentukan DNS Server sesuai kebutuhan.
8. Selesaikan proses DHCP Setup.

Pastikan DHCP Server sudah aktif.

**Mengapa DHCP Pool dimulai dari 10.10.3.3?**

Karena alamat `10.10.3.1` digunakan oleh MikroTik sebagai gateway, sedangkan `10.10.3.2` digunakan oleh laptop untuk pengujian Simple Queue secara manual.

Dengan mengecualikan kedua alamat tersebut dari DHCP Pool, risiko terjadinya IP Conflict dapat dikurangi.

**Fungsi DHCP Server:**

- Membagikan IP Address secara otomatis.
- Memberikan informasi gateway kepada client.
- Memberikan informasi DNS Server.
- Mempermudah pengelolaan jaringan ketika terdapat banyak perangkat.

Jika tidak menggunakan DHCP Server, setiap perangkat client harus dikonfigurasi IP Address, subnet mask, gateway, dan DNS secara manual.

---

## 3. Konfigurasi NAT

NAT (Network Address Translation) digunakan untuk menerjemahkan alamat IP jaringan lokal agar perangkat client dapat mengakses internet melalui IP Address yang digunakan pada interface WAN.

### Langkah-langkah konfigurasi

1. Buka menu **IP → Firewall → NAT**.
2. Klik tombol tambah (+).
3. Pada tab **General**, atur:
   - Chain: `srcnat`
   - Out. Interface: `ether1`
4. Pindah ke tab **Action**.
5. Pilih Action: `masquerade`.
6. Klik **Apply**, kemudian **OK**.

**Penjelasan:**

- `srcnat` digunakan untuk mengubah alamat sumber paket yang berasal dari jaringan lokal.
- `ether1` dipilih karena merupakan interface yang mengarah ke ISP.
- `masquerade` digunakan untuk menyesuaikan alamat sumber paket dengan IP Address yang digunakan pada interface WAN.

**Mengapa menggunakan Masquerade?**

Karena IP Address WAN dapat berubah ketika menggunakan DHCP Client. Masquerade akan menyesuaikan NAT dengan alamat IP yang sedang digunakan oleh interface WAN.

Jika NAT tidak dikonfigurasi, perangkat lokal mungkin berhasil terhubung ke MikroTik, tetapi tidak dapat mengakses internet melalui jaringan WAN apabila tidak tersedia mekanisme routing dan translasi alamat lain yang sesuai.

---

## 4. Pengaturan DNS MikroTik

DNS (Domain Name System) berfungsi menerjemahkan nama domain menjadi alamat IP Address sehingga pengguna dapat mengakses website menggunakan nama domain tanpa harus menghafalkan alamat IP.

### A. Mengatur DNS Server

**Langkah-langkah:**

1. Buka menu **IP → DNS**.
2. Masukkan alamat DNS berikut pada bagian Servers:
   - `1.1.1.1`
   - `8.8.8.8`
3. Aktifkan **Allow Remote Requests**.
4. Klik **Apply**, kemudian **OK**.

**Penjelasan:**

- `1.1.1.1` merupakan DNS Resolver dari Cloudflare.
- `8.8.8.8` merupakan DNS Resolver dari Google.
- Allow Remote Requests memungkinkan MikroTik menerima permintaan DNS dari perangkat client.

**Mengapa Allow Remote Requests harus diaktifkan?**

Karena MikroTik akan digunakan sebagai DNS Resolver bagi perangkat yang terhubung ke jaringan lokal.

Jika tidak diaktifkan, client yang menggunakan MikroTik sebagai DNS Server tidak dapat meminta layanan penerjemahan domain melalui router.

> **Catatan keamanan:** Pastikan DNS hanya dapat diakses dari jaringan yang dipercaya dan tidak terbuka untuk akses dari internet.

### B. Menambahkan DNS Static

**Langkah-langkah:**

1. Masuk ke menu **IP → DNS → Static**.
2. Klik tombol tambah (+).
3. Masukkan konfigurasi berikut:

| Pengaturan | Nilai |
|:---|:---|
| Name | `ukom12tkj-3.sch.id` |
| Type | `A` |
| Address | `100.100.3.2` |

4. Klik **Apply**, kemudian **OK**.

DNS Static digunakan untuk menghubungkan nama domain dengan alamat IP tertentu secara manual.

Pada konfigurasi ini, domain `ukom12tkj-3.sch.id` diarahkan menuju IP Address `100.100.3.2` yang digunakan oleh Ubuntu Server.

Dengan begitu, pengguna dapat mengakses layanan server menggunakan nama domain tanpa harus memasukkan IP Address secara langsung.

---

## 5. Konfigurasi WiFi

WiFi digunakan untuk menyediakan koneksi jaringan secara nirkabel sehingga perangkat seperti HP dan laptop dapat terhubung ke MikroTik tanpa menggunakan kabel LAN.

### Langkah-langkah konfigurasi

1. Buka menu **WiFi** atau **Wireless**, sesuai versi RouterOS.
2. Pilih interface `wlan1`.
3. Atur Mode menjadi `ap bridge` jika tersedia.
4. Pilih band sesuai kemampuan perangkat.
5. Masukkan SSID atau nama jaringan WiFi: `ukom12tkj-3`.
6. Aktifkan interface `wlan1`.
7. Klik **Apply**, kemudian **OK**.

**Penjelasan:**

- `ap bridge` digunakan agar MikroTik berfungsi sebagai Access Point yang menyediakan jaringan WiFi bagi beberapa perangkat.
- Band menentukan frekuensi jaringan WiFi yang digunakan.
- SSID merupakan nama jaringan yang akan terlihat ketika perangkat mencari koneksi WiFi.

**Mengapa menggunakan mode Access Point?**

Karena MikroTik akan menyediakan jaringan nirkabel bagi pengguna Hotspot.

Jika interface WiFi tidak diaktifkan atau belum dikonfigurasi dengan benar, perangkat tidak akan dapat menemukan jaringan WiFi yang disediakan MikroTik.

> **Catatan:** Pada beberapa versi RouterOS, pengaturan WiFi menggunakan menu dan konfigurasi yang berbeda. Pastikan interface WiFi sudah mendukung mode Access Point.

---

## 6. Konfigurasi Hotspot

Hotspot merupakan layanan yang memungkinkan pengguna mengakses jaringan melalui sistem autentikasi berupa username dan password.

Dengan Hotspot, administrator dapat mengatur siapa saja yang diperbolehkan menggunakan jaringan serta membatasi kecepatan internet setiap pengguna.

### A. Membuat Hotspot Server

**Langkah-langkah:**

1. Buka menu **IP → Hotspot**.
2. Pilih **Hotspot Setup**.
3. Pilih interface `wlan1`.
4. Masukkan Local Address: `192.168.3.1/24`.
5. Atur Address Pool: `192.168.3.10-192.168.3.254`.
6. Lanjutkan proses konfigurasi hingga selesai.

**Penjelasan:**

- Local Address merupakan IP Gateway yang digunakan oleh jaringan Hotspot.
- Address Pool merupakan rentang IP Address yang akan dibagikan kepada pengguna WiFi.

**Mengapa menggunakan Address Pool?**

Karena setiap perangkat yang terhubung ke jaringan Hotspot membutuhkan IP Address agar dapat berkomunikasi dengan MikroTik.

Jika tidak menggunakan Address Pool, pengguna tidak dapat memperoleh IP Address secara otomatis melalui DHCP Hotspot.

### B. Membuat User Profile

**Langkah-langkah:**

1. Buka menu **IP → Hotspot → User Profiles**.
2. Klik tombol tambah (+).
3. Atur konfigurasi berikut:

| Pengaturan | Nilai |
|:---|:---|
| Name | `Profile-7Mbps` |
| Address Pool | Pool Hotspot |
| Shared Users | `2` |
| Rate Limit | `7M/7M` |

4. Klik **Apply**, kemudian **OK**.

**Penjelasan:**

- Address Pool menentukan kumpulan IP Address yang digunakan pengguna.
- Shared Users menentukan jumlah perangkat yang dapat menggunakan satu akun secara bersamaan.
- Rate Limit membatasi kecepatan upload dan download pengguna.

**Mengapa Shared Users menggunakan angka 2?**

Karena satu akun Hotspot diperbolehkan digunakan oleh maksimal dua perangkat secara bersamaan.

Jika jumlahnya lebih dari dua, perangkat tambahan tidak dapat menggunakan akun tersebut secara bersamaan sesuai batas profil.

**Mengapa menggunakan Rate Limit 7M/7M?**

Karena kecepatan pengguna Hotspot dibatasi maksimal 7 Mbps untuk upload dan 7 Mbps untuk download.

Pengaturan ini bertujuan agar penggunaan bandwidth lebih terkontrol dan tidak digunakan secara berlebihan oleh satu pengguna.

### C. Membuat Hotspot User

**Langkah-langkah:**

1. Masuk ke menu **IP → Hotspot → Users**.
2. Klik tombol tambah (+).
3. Masukkan username sesuai ketentuan penguji.
4. Masukkan password.
5. Pilih User Profile yang sudah dibuat.
6. Klik **Apply**, kemudian **OK**.

Username dan password digunakan sebagai identitas untuk proses login Hotspot.

Jika tidak dibuatkan akun pengguna, perangkat tidak dapat melewati proses autentikasi menggunakan akun tersebut.

---

## 7. Pengujian Koneksi Hotspot dan Speedtest

Setelah konfigurasi Hotspot selesai, lakukan pengujian untuk memastikan jaringan WiFi dapat digunakan dan pembatasan bandwidth berjalan sesuai ketentuan.

### Langkah-langkah pengujian

1. Aktifkan WiFi pada HP atau laptop.
2. Cari jaringan dengan nama `ukom12tkj-3`.
3. Hubungkan perangkat ke jaringan tersebut.
4. Buka browser dan tunggu hingga halaman login Hotspot muncul.
5. Masukkan username dan password yang sudah dibuat.
6. Setelah berhasil login, buka website Speedtest.
7. Jalankan pengujian kecepatan internet.
8. Periksa hasil download dan upload.

### Ketentuan Hasil Speedtest Hotspot

| Keterangan | Nilai |
|:---|:---|
| Batas maksimal bandwidth | **7 Mbps** |
| Target hasil pengujian | **Sekitar 6 Mbps** |

**Mengapa targetnya sekitar 6 Mbps, bukan tepat 7 Mbps?**

Karena angka 7 Mbps merupakan batas maksimal yang diberikan oleh MikroTik, bukan kecepatan yang harus selalu dicapai secara tepat.

Target sekitar 6 Mbps digunakan agar masih terdapat sedikit ruang di bawah batas maksimal. Hal ini membantu menghindari hasil pengujian yang terus-menerus menyentuh batas bandwidth.

Kecepatan yang stabil lebih baik daripada kecepatan yang sesekali tinggi tetapi sering mengalami penurunan.

**Mengapa tidak boleh jauh di bawah 6 Mbps?**

Karena jika hasilnya terlalu rendah, misalnya hanya 2–3 Mbps ketika jaringan sedang tidak memiliki beban besar, kemungkinan terdapat masalah pada koneksi, kualitas sinyal WiFi, penggunaan bandwidth, atau konfigurasi router.

Oleh karena itu, hasil pengujian diharapkan mendekati 6 Mbps dan tidak melebihi 7 Mbps.

**Mengapa kecepatan harus stabil?**

Kestabilan bandwidth membuat pengguna mendapatkan koneksi yang lebih konsisten. Jika kecepatan sering berubah secara drastis, aktivitas seperti membuka website, mengunduh file, dan melakukan komunikasi online dapat terganggu.

> **Catatan:** Hasil Speedtest dapat berubah karena kondisi jaringan, kualitas sinyal, jumlah pengguna, server pengujian, dan overhead jaringan. Angka 6 Mbps merupakan target pengujian, bukan jaminan kecepatan yang selalu sama pada setiap kondisi.

---

## 8. Konfigurasi Simple Queue untuk Client Kabel

Simple Queue merupakan fitur MikroTik yang digunakan untuk mengatur dan membatasi penggunaan bandwidth pada perangkat tertentu.

Pada konfigurasi ini, Simple Queue diterapkan pada laptop yang terhubung melalui interface `ether2`.

### A. Membuat Simple Queue

**Langkah-langkah:**

1. Buka aplikasi WinBox.
2. Masuk ke menu **Queues → Simple Queues**.
3. Klik tombol tambah (+).
4. Masukkan konfigurasi berikut:

| Pengaturan | Nilai |
|:---|:---|
| Name | `Client-Kabel` |
| Target | `10.10.3.2/32` |
| Max Limit | `10M/10M` |

5. Klik **Apply**, kemudian **OK**.

**Penjelasan:**

- Name digunakan sebagai identitas rule Simple Queue.
- Target menentukan IP Address perangkat yang akan dibatasi.
- Max Limit menentukan batas maksimal bandwidth upload dan download.

**Mengapa Target menggunakan IP Address?**

Karena Simple Queue membatasi bandwidth berdasarkan alamat IP perangkat yang menjadi sasaran pengaturan.

Penggunaan `10.10.3.2/32` menunjukkan bahwa pembatasan hanya berlaku untuk satu alamat IP, yaitu laptop yang digunakan untuk pengujian.

**Mengapa menggunakan Max Limit 10M/10M?**

Karena laptop client kabel diberikan batas maksimal 10 Mbps untuk upload dan download.

Jika Simple Queue tidak dibuat, perangkat tersebut tidak mendapatkan pembatasan bandwidth melalui rule ini sehingga dapat menggunakan bandwidth yang tersedia sesuai kondisi jaringan dan pengaturan lainnya.

---

## 9. Mengatur IP Laptop Menjadi Otomatis

Setelah pengujian Simple Queue menggunakan IP statis selesai, kembalikan pengaturan IP laptop menjadi otomatis agar dapat menggunakan DHCP Server MikroTik.

### Langkah-langkah

1. Buka **Control Panel → Network and Sharing Center**.
2. Pilih **Change adapter settings**.
3. Klik kanan Ethernet, kemudian pilih **Properties**.
4. Pilih **Internet Protocol Version 4 (TCP/IPv4)**.
5. Pilih pengaturan berikut:
   - Obtain an IP address automatically.
   - Obtain DNS server address automatically.
6. Klik **OK** untuk menyimpan perubahan.

**Penjelasan:**

Pengaturan ini membuat laptop mendapatkan IP Address, Subnet Mask, Gateway, dan DNS secara otomatis dari DHCP Server MikroTik.

**Mengapa harus dikembalikan menjadi otomatis?**

Karena DHCP Server pada ether2 sudah dikonfigurasi untuk membagikan IP Address kepada perangkat client.

Dengan menggunakan DHCP, pengelolaan jaringan menjadi lebih mudah dan pengguna tidak perlu memasukkan konfigurasi IP secara manual.

> **Catatan:** Pastikan DHCP Pool tidak memasukkan IP `10.10.3.2` agar alamat khusus pengujian Simple Queue tidak diberikan kepada perangkat lain.

---

## 10. Pengujian Kecepatan Client Kabel dan Speedtest

Setelah Simple Queue selesai dikonfigurasi, lakukan pengujian untuk memastikan pembatasan bandwidth pada laptop client kabel berjalan dengan baik.

### Langkah-langkah pengujian

1. Pastikan laptop terhubung ke interface `ether2`.
2. Pastikan laptop mendapatkan IP Address dari DHCP Server.
3. Buka browser.
4. Akses website Speedtest.
5. Jalankan pengujian kecepatan internet.
6. Periksa hasil download dan upload.

### Ketentuan Hasil Speedtest Client Kabel

| Keterangan | Nilai |
|:---|:---|
| Batas maksimal bandwidth | **10 Mbps** |
| Target hasil pengujian | **Sekitar 9 Mbps** |

**Mengapa targetnya 9 Mbps, bukan tepat 10 Mbps?**

Karena 10 Mbps merupakan batas maksimal yang ditentukan pada Simple Queue. Kecepatan sekitar 9 Mbps menunjukkan bahwa penggunaan bandwidth sudah mendekati batas maksimal tanpa harus terus-menerus menyentuh angka 10 Mbps. Target ini juga memberikan sedikit ruang untuk overhead jaringan dan perubahan kondisi koneksi.

**Mengapa tidak boleh jauh di bawah 9 Mbps?**

Jika hasil pengujian terlalu rendah, misalnya hanya 3–4 Mbps ketika jaringan sedang tidak sibuk, kemungkinan terdapat masalah pada konfigurasi Simple Queue, koneksi kabel LAN, kondisi jaringan, atau penggunaan bandwidth oleh perangkat lain.

Oleh karena itu, hasil pengujian diharapkan mendekati 9 Mbps agar penggunaan bandwidth lebih optimal.

### Bagaimana jika hasil Speedtest melebihi 10 Mbps?

Jika hasil Speedtest melebihi 10 Mbps secara konsisten, hal tersebut menunjukkan bahwa pembatasan bandwidth belum berjalan sebagaimana mestinya atau pengujian belum benar-benar melewati jalur yang terkena rule Simple Queue.

Beberapa hal yang perlu diperiksa:

1. Pastikan Simple Queue dalam keadaan aktif.
2. Periksa kembali Target, pastikan menggunakan IP `10.10.3.2/32`.
3. Pastikan IP laptop sesuai dengan target yang dibuat.
4. Periksa Max Limit, pastikan nilainya `10M/10M`.
5. Pastikan trafik client melewati router dan tidak melewati jalur jaringan lain.

**Mengapa hasilnya harus stabil di sekitar 9 Mbps?**

Agar penggunaan bandwidth dapat berjalan secara konsisten dan tidak melebihi kapasitas yang telah ditentukan. Dengan pembatasan ini, administrator dapat mengatur penggunaan bandwidth agar lebih terkontrol dan memberikan pengalaman koneksi yang lebih baik bagi pengguna.

> **Catatan:** Target 9 Mbps merupakan acuan hasil pengujian, bukan angka yang dijamin selalu muncul. Hasil aktual dapat dipengaruhi oleh kualitas kabel, perangkat jaringan, server Speedtest, overhead, dan kondisi trafik. Jika hasilnya sedikit berubah, periksa kestabilannya melalui beberapa kali pengujian.

---

## 11. Ringkasan Konfigurasi

Berikut merupakan rangkuman konfigurasi yang digunakan dalam praktik MikroTik:

### A. Konfigurasi IP Address

| Komponen | Konfigurasi |
|:---|:---|
| Identity | `R-UKOM3` |
| ether1 (ISP) | DHCP Client |
| ether2 (CLIENT) | `10.10.3.1/28` |
| ether3 (SERVER) | `100.100.3.1/30` |
| wlan1 (HOTSPOT) | `192.168.3.1/24` |
| IP Ubuntu Server | `100.100.3.2` |

### B. Konfigurasi Layanan Jaringan

| Komponen | Konfigurasi |
|:---|:---|
| DHCP Pool Client | `10.10.3.3-10.10.3.14` |
| DNS Cloudflare | `1.1.1.1` |
| DNS Google | `8.8.8.8` |
| Domain Static | `ukom12tkj-3.sch.id` |
| SSID WiFi | `ukom12tkj-3` |
| Hotspot Shared Users | `2` |
| Hotspot Rate Limit | `7M/7M` |
| Simple Queue Target | `10.10.3.2/32` |
| Simple Queue Max Limit | `10M/10M` |

### C. Perbandingan Target Pengujian Speedtest

| Pengujian | Batas Maksimal | Target Hasil |
|:---|---:|---:|
| Hotspot WiFi | 7 Mbps | Sekitar 6 Mbps |
| Client Kabel | 10 Mbps | Sekitar 9 Mbps |

Kedua pengujian tersebut bertujuan memastikan bahwa pembatasan bandwidth pada masing-masing jaringan sudah berjalan sesuai konfigurasi MikroTik.

---

**Dokumentasi Praktik UKOM TKJ 2026**  
Disusun oleh **Annisa Nur Aulia** | Nomor Absen **3**
