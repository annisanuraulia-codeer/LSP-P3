# PERBAIKAN STATUS SERVER: UNKNOWN PADA NSLOOKUP DNS SERVER BIND9

# BAGIAN I – TAHAPAN UTAMA PERBAIKAN

Bagian ini menjelaskan langkah-langkah untuk mengatasi status `Server: Unknown` pada pengujian DNS menggunakan BIND9.

## STEP 1 – Memeriksa Konfigurasi Forward Zone dan Reverse Zone

Buka file konfigurasi zona DNS menggunakan perintah:

```bash
sudo nano /etc/bind/named.conf.local
```

Pastikan konfigurasi di dalamnya sesuai:

```conf
zone "ukom12tkja-3.sch.id" {
    type master;
    file "/etc/bind/db.ukom12tkja-3";
};

zone "3.10.10.in-addr.arpa" {
    type master;
    file "/etc/bind/db.10.10.3";
};
```

**Penjelasan:**

File `named.conf.local` digunakan untuk mendaftarkan zona DNS yang dikelola BIND9.

- Forward Zone menerjemahkan nama domain menjadi IP Address.
- Reverse Zone menerjemahkan IP Address menjadi nama domain.
- `type master` menunjukkan bahwa server bertindak sebagai DNS Master.
- `file` menentukan lokasi file database masing-masing zona.

Simpan perubahan dengan `CTRL + O`, Enter, kemudian `CTRL + X`.

## STEP 2 – Memperbaiki Konfigurasi Forward Zone

Buka file Forward Zone:

```bash
sudo nano /etc/bind/db.ukom12tkja-3
```

Sesuaikan konfigurasi menjadi:

```dns
$TTL 604800
@   IN  SOA ukom12tkja-3.sch.id. root.ukom12tkja-3.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@       IN  NS  ukom12tkja-3.sch.id.
@       IN  A   10.10.3.3
www     IN  A   10.10.3.3
```

**Penjelasan:**

Forward Zone menghubungkan nama domain dengan alamat IP DNS Server.

- `SOA` menyimpan informasi utama pengelolaan zona DNS.
- `NS` menentukan DNS Server yang bertanggung jawab terhadap zona.
- `A` menghubungkan nama domain dengan alamat IPv4.
- `@` mewakili nama domain utama.
- `www` merupakan subdomain yang dapat diakses melalui `www.ukom12tkja-3.sch.id`.
- Serial `2026111201` merupakan nomor versi zona. Setiap kali mengubah isi file zona, naikkan serialnya.

Dengan konfigurasi tersebut, domain utama dan subdomain `www` diarahkan ke IP `10.10.3.3`.

Simpan perubahan dengan `CTRL + O`, Enter, kemudian `CTRL + X`.

## STEP 3 – Memperbaiki Konfigurasi Reverse Zone

Buka file Reverse Zone:

```bash
sudo nano /etc/bind/db.10.10.3
```

Sesuaikan konfigurasi menjadi:

```dns
$TTL 604800
@   IN  SOA ukom12tkja-3.sch.id. root.ukom12tkja-3.sch.id. (
        2026111201
        604800
        86400
        2419200
        604800
)

@   IN  NS  ukom12tkja-3.sch.id.
3   IN  PTR ukom12tkja-3.sch.id.
```

**Penjelasan:**

Reverse Zone digunakan untuk menerjemahkan IP Address menjadi nama domain melalui PTR Record.

Pada konfigurasi tersebut, angka `3` merupakan oktet terakhir dari IP `10.10.3.3`. Sementara itu, nama zona `3.10.10.in-addr.arpa` mewakili tiga oktet sebelumnya.

Jika digabungkan, alamat Reverse DNS menjadi:

```text
3.3.10.10.in-addr.arpa
```

PTR Record tersebut akan mengarahkan IP `10.10.3.3` ke nama `ukom12tkja-3.sch.id`.

Simpan perubahan dengan `CTRL + O`, Enter, kemudian `CTRL + X`.

**Catatan:** Jika isi file zona diubah, nomor serial SOA harus dinaikkan agar perubahan dapat dikenali oleh BIND9.

## STEP 4 – Memeriksa Konfigurasi DNS

Setelah Forward Zone dan Reverse Zone diperbaiki, lakukan pemeriksaan sebelum memuat ulang layanan BIND9.

**A. Memeriksa konfigurasi utama BIND9**

```bash
sudo named-checkconf
```

Digunakan untuk memeriksa kesalahan sintaks pada konfigurasi utama BIND9. Jika tidak ada kesalahan, terminal akan kembali ke prompt tanpa menampilkan pesan error.

**B. Memeriksa Forward Zone**

```bash
sudo named-checkzone ukom12tkja-3.sch.id /etc/bind/db.ukom12tkja-3
```

Hasil yang diharapkan:

```text
zone ukom12tkja-3.sch.id/IN: loaded serial 2026111201
OK
```

**C. Memeriksa Reverse Zone**

```bash
sudo named-checkzone 3.10.10.in-addr.arpa /etc/bind/db.10.10.3
```

Hasil yang diharapkan:

```text
zone 3.10.10.in-addr.arpa/IN: loaded serial 2026111201
OK
```

Pastikan kedua pemeriksaan menghasilkan keterangan `OK`. Jika masih terdapat kesalahan, perbaiki terlebih dahulu sebelum melanjutkan.

## STEP 5 – Memuat Ulang Layanan BIND9

Setelah seluruh konfigurasi berhasil diperiksa, jalankan:

```bash
sudo systemctl restart bind9.service
```

Perintah ini digunakan untuk memulai ulang layanan agar konfigurasi DNS terbaru diterapkan.

Selanjutnya, periksa status layanan:

```bash
sudo systemctl status bind9.service
```

Pastikan muncul keterangan:

```text
Active: active (running)
```

Keterangan tersebut menunjukkan bahwa layanan BIND9 sedang berjalan. Tekan `Q` untuk keluar dari tampilan status.

## STEP 6 – Mengatur DNS Server pada Laptop Windows

Setelah konfigurasi Ubuntu Server selesai, arahkan DNS laptop ke DNS Server yang telah dibuat.

Langkah-langkahnya:

1. Buka Control Panel.
2. Pilih Network and Internet.
3. Masuk ke Network and Sharing Center.
4. Klik Change adapter settings.
5. Pilih adapter jaringan yang sedang digunakan.
6. Klik kanan adapter, kemudian pilih Properties.
7. Pilih Internet Protocol Version 4 (TCP/IPv4).
8. Klik Properties.

Pada bagian DNS, pilih `Use the following DNS server addresses`, kemudian masukkan:

| Kolom | Alamat |
|---|---|
| Preferred DNS server | `10.10.3.3` |
| Alternate DNS server | Kosongkan dahulu |

Klik OK, kemudian Close.

**Penjelasan:**

Pengaturan ini bertujuan agar laptop menggunakan DNS Server `10.10.3.3` ketika melakukan permintaan penerjemahan nama domain. Pastikan IP laptop berada pada jaringan yang dapat menjangkau DNS Server tersebut.

## STEP 7 – Membersihkan Cache DNS Windows

Buka Command Prompt sebagai Administrator, kemudian jalankan:

```cmd
ipconfig /flushdns
```

Digunakan untuk membersihkan cache DNS lama pada Windows agar pengujian menggunakan informasi DNS terbaru.

Selanjutnya, periksa konfigurasi jaringan:

```cmd
ipconfig /all
```

Cari adapter yang sedang digunakan dan pastikan bagian DNS Servers menunjukkan:

```text
DNS Servers . . . . . . . . . . : 10.10.3.3
```

Jika alamat tersebut sudah muncul, berarti laptop telah menggunakan DNS Server yang ditentukan.

## STEP 8 – Melakukan Pengujian NSLOOKUP

Setelah seluruh konfigurasi selesai, lakukan pengujian menggunakan `nslookup`.

**A. Pengujian dengan menentukan IP DNS Server**

```cmd
nslookup ukom12tkja-3.sch.id 10.10.3.3
```

Pengujian ini digunakan untuk memastikan DNS Server dapat memberikan jawaban terhadap permintaan domain.

**B. Pengujian tanpa menambahkan IP DNS Server**

```cmd
nslookup ukom12tkja-3.sch.id
```

Pengujian ini menggunakan DNS Server yang telah diatur pada adapter jaringan Windows.

Jika kedua pengujian berhasil dan bagian `Server` menampilkan nama domain, berarti masalah `Server: Unknown` telah berhasil diatasi.

---

# BAGIAN II – TROUBLESHOOTING / PEMECAHAN MASALAH

Bagian ini digunakan apabila hasil pengujian masih belum sesuai atau muncul pesan kesalahan.

## TROUBLESHOOTING 1 – Server Masih Menampilkan Unknown

**Gejala:**

```text
Server: Unknown
Address: 10.10.3.3
```

**Kemungkinan penyebab:**

- PTR Record belum dikonfigurasi dengan benar.
- Reverse Zone belum berhasil dimuat.
- Nama domain pada PTR Record tidak sesuai.

**Langkah pemeriksaan:**

Periksa Reverse Zone:

```bash
sudo named-checkzone 3.10.10.in-addr.arpa /etc/bind/db.10.10.3
```

Kemudian lakukan pengujian:

```bash
dig @127.0.0.1 -x 10.10.3.3
```

Pastikan hasil PTR mengarah ke:

```text
ukom12tkja-3.sch.id.
```

Jika belum sesuai, buka kembali file Reverse Zone:

```bash
sudo nano /etc/bind/db.10.10.3
```

Pastikan terdapat konfigurasi:

```dns
3 IN PTR ukom12tkja-3.sch.id.
```

Setelah diperbaiki, naikkan serial SOA, periksa zona kembali, kemudian restart BIND9.

## TROUBLESHOOTING 2 – Forward DNS Berhasil, tetapi Reverse DNS Gagal

**Gejala:**

Forward DNS dapat menerjemahkan domain menjadi IP Address, tetapi nama DNS Server belum berhasil dikenali.

**Langkah pemeriksaan:**

Jalankan perintah berikut pada Windows CMD:

```cmd
nslookup -type=PTR 3.3.10.10.in-addr.arpa 10.10.3.3
```

Perintah ini digunakan untuk meminta informasi PTR Record secara langsung kepada DNS Server.

Jika hasilnya belum menunjukkan nama domain, periksa kembali file Reverse Zone dan pastikan PTR Record sudah sesuai dengan IP DNS Server.

## TROUBLESHOOTING 3 – NSLOOKUP Mengalami Request Timed Out

**Gejala:**

```text
DNS request timed out.
timeout was 2 seconds.
```

**Kemungkinan penyebab:**

- Layanan BIND9 tidak berjalan.
- Port 53 belum menerima permintaan DNS.
- Laptop tidak dapat menjangkau DNS Server.
- Firewall memblokir koneksi DNS.

**Langkah pemeriksaan:**

Periksa layanan BIND9:

```bash
sudo systemctl status bind9.service
```

Periksa port DNS:

```bash
sudo ss -tulpn | grep ':53'
```

Periksa koneksi dari Windows:

```cmd
ping 10.10.3.3
```

Jika layanan tidak berjalan, periksa pesan kesalahan sebelum melakukan perbaikan.

**Catatan:** Ping yang gagal belum tentu menunjukkan DNS tidak dapat digunakan karena ICMP dapat diblokir. Lakukan juga pengujian DNS secara langsung.

## TROUBLESHOOTING 4 – NSLOOKUP Masih Menggunakan DNS Server Lain

**Gejala:**

Ketika menjalankan `nslookup` tanpa tambahan IP, bagian Server masih menunjukkan DNS Server lain.

**Langkah pemeriksaan:**

```cmd
ipconfig /all
```

Cari bagian DNS Servers pada adapter jaringan yang sedang digunakan.

Pastikan alamat DNS yang muncul adalah:

```text
10.10.3.3
```

Jika belum sesuai, periksa kembali pengaturan IPv4 pada adapter jaringan Windows. Jika DNS diperoleh melalui DHCP, pastikan DHCP tidak memberikan DNS Server lain.

## TROUBLESHOOTING 5 – BIND9 Gagal Berjalan Setelah Konfigurasi Diubah

**Gejala:**

```text
Active: failed
```

**Langkah pemeriksaan:**

Periksa konfigurasi utama:

```bash
sudo named-checkconf
```

Periksa Forward Zone:

```bash
sudo named-checkzone ukom12tkja-3.sch.id /etc/bind/db.ukom12tkja-3
```

Periksa Reverse Zone:

```bash
sudo named-checkzone 3.10.10.in-addr.arpa /etc/bind/db.10.10.3
```

Jika ditemukan kesalahan, perbaiki file konfigurasi yang bermasalah.

Untuk melihat informasi error layanan BIND9:

```bash
sudo journalctl -u bind9.service -n 30 --no-pager
```

Setelah kesalahan diperbaiki, jalankan kembali:

```bash
sudo systemctl restart bind9.service
```

Kemudian periksa status layanan.

## TROUBLESHOOTING 6 – DNS Server Tidak Menerima Permintaan dari Laptop

**Gejala:**

DNS Server dapat diuji secara lokal, tetapi tidak dapat memberikan jawaban ketika diakses dari laptop.

**Langkah pemeriksaan:**

Periksa port DNS:

```bash
sudo ss -tulpn | grep ':53'
```

Pastikan BIND9 mendengarkan pada alamat jaringan yang dapat dijangkau laptop, bukan hanya pada localhost.

Periksa konfigurasi:

```bash
sudo nano /etc/bind/named.conf.options
```

Pastikan pengaturan `listen-on` dan `allow-query` mengizinkan permintaan dari jaringan klien.

Jika menggunakan UFW, periksa status firewall:

```bash
sudo ufw status
```

Jika UFW aktif dan belum mengizinkan DNS dari jaringan lab, tambahkan aturan:

```bash
sudo ufw allow from 10.10.3.0/28 to any port 53 proto udp
sudo ufw allow from 10.10.3.0/28 to any port 53 proto tcp
```

Setelah itu, lakukan pengujian kembali dari laptop.

---

# BAGIAN III – CHECKLIST PENGUJIAN AKHIR

Sebelum menyatakan konfigurasi berhasil, pastikan seluruh poin berikut telah terpenuhi:

- [ ] Forward Zone sudah terdaftar pada `named.conf.local`.
- [ ] Reverse Zone sudah terdaftar pada `named.conf.local`.
- [ ] A Record mengarah ke IP `10.10.3.3`.
- [ ] PTR Record mengarah ke `ukom12tkja-3.sch.id`.
- [ ] `named-checkconf` tidak menghasilkan error.
- [ ] Pemeriksaan Forward Zone menghasilkan `OK`.
- [ ] Pemeriksaan Reverse Zone menghasilkan `OK`.
- [ ] Layanan BIND9 berstatus `active (running)`.
- [ ] Laptop dapat menjangkau DNS Server `10.10.3.3`.
- [ ] DNS Server pada Windows menunjukkan IP `10.10.3.3`.
- [ ] Pengujian `nslookup` dengan tambahan IP berhasil.
- [ ] Pengujian `nslookup` tanpa tambahan IP berhasil.
- [ ] Bagian Server menampilkan nama domain, bukan `Unknown`.

---

# KESIMPULAN

Permasalahan `Server: Unknown` pada pengujian DNS menggunakan perintah `nslookup` dapat ditangani dengan memperbaiki konfigurasi Reverse DNS, khususnya melalui penambahan PTR Record yang menghubungkan alamat IP DNS Server dengan nama domain.

Selain itu, konfigurasi Forward DNS harus dipastikan berjalan dengan benar agar nama domain dapat diterjemahkan menjadi IP Address. Pengaturan DNS pada komputer klien juga perlu diarahkan ke alamat IP DNS Server yang telah dikonfigurasi.

Setelah seluruh tahapan perbaikan berhasil dilakukan, pengujian dapat dilanjutkan menggunakan:

```cmd
nslookup ukom12tkja-3.sch.id
```

Hasil yang diharapkan adalah bagian `Server` menampilkan nama domain DNS Server, bukan lagi `Unknown`, serta bagian `Address` menunjukkan alamat IP `10.10.3.3`.
