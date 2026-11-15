# E. KONFIGURASI DNS SERVER BIND9

DNS Server digunakan untuk menerjemahkan nama domain menjadi alamat IP. Pada konfigurasi ini, saya menggunakan BIND9 agar domain `ukom12tkj-3.sch.id` dapat diarahkan ke IP Ubuntu Server `100.100.3.2`.

## 1. Instalasi BIND9

Jalankan perintah berikut untuk memperbarui daftar paket dan menginstal BIND9 beserta utilitas DNS:

```bash
sudo apt update
sudo apt install bind9 bind9-utils dnsutils -y
```

Perintah `apt update` memperbarui daftar paket yang tersedia. Sementara itu, `apt install` digunakan untuk memasang BIND9 sebagai layanan DNS dan `dnsutils` untuk menyediakan alat pengujian DNS seperti `dig` dan `nslookup`.

## 2. Menambahkan Konfigurasi Zona DNS

Buka file konfigurasi zona lokal:

```bash
sudo nano /etc/bind/named.conf.local
```

Tambahkan konfigurasi berikut di bagian paling bawah file:

```conf
zone "ukom12tkj-3.sch.id" {
    type master;
    file "/etc/bind/db.ukom12tkj-3";
};

zone "3.100.100.in-addr.arpa" {
    type master;
    file "/etc/bind/db.100.100.3";
};
```

**Keterangan:**
- Zona pertama digunakan untuk menerjemahkan domain menjadi alamat IP (Forward Zone).
- Zona kedua digunakan untuk menerjemahkan alamat IP menjadi nama domain (Reverse Zone).
- `type master` menunjukkan bahwa Ubuntu Server bertindak sebagai DNS Master.
- `file` menentukan lokasi file konfigurasi masing-masing zona.

Simpan file dengan menekan `Ctrl + O`, lalu `Enter`, kemudian keluar menggunakan `Ctrl + X`.

## 3. Membuat File Zona DNS Forward

Salin file zona bawaan sebagai template:

```bash
sudo cp /etc/bind/db.local /etc/bind/db.ukom12tkj-3
```

Kemudian buka file zona yang sudah dibuat:

```bash
sudo nano /etc/bind/db.ukom12tkj-3
```

Sesuaikan isinya menjadi seperti berikut:

```dns
$TTL 604800
@   IN  SOA ns.ukom12tkj-3.sch.id. root.ukom12tkj-3.sch.id. (
        2026111203
        604800
        86400
        2419200
        604800
)

@   IN  NS  ns.ukom12tkj-3.sch.id.
@   IN  A   100.100.3.2
ns  IN  A   100.100.3.2
www IN  A   100.100.3.2
```

**Keterangan:**
- `SOA` berisi informasi utama mengenai zona DNS.
- `NS` menentukan server DNS yang melayani zona tersebut.
- `A` menghubungkan nama domain dengan alamat IPv4.
- `www` digunakan agar domain dengan awalan `www` juga dapat diakses.
- Nomor serial digunakan sebagai penanda versi file zona. Nomor ini perlu dinaikkan setiap kali file zona diubah.

Simpan file dengan menekan `Ctrl + O`, lalu `Enter`, kemudian keluar menggunakan `Ctrl + X`.

## 4. Membuat File Zona DNS Reverse

Buat file Reverse DNS dengan menyalin template zona bawaan:

```bash
sudo cp /etc/bind/db.127 /etc/bind/db.100.100.3
```

Kemudian buka file tersebut:

```bash
sudo nano /etc/bind/db.100.100.3
```

Sesuaikan isinya menjadi seperti berikut:

```dns
$TTL 604800
@   IN  SOA ns.ukom12tkj-3.sch.id. root.ukom12tkj-3.sch.id. (
        2026111203
        604800
        86400
        2419200
        604800
)

@   IN  NS  ns.ukom12tkj-3.sch.id.
2   IN  PTR ukom12tkj-3.sch.id.
```

**Keterangan:**
- File ini digunakan untuk konfigurasi Reverse DNS.
- `PTR` digunakan untuk menghubungkan alamat IP dengan nama domain.
- Angka `2` menunjukkan bagian terakhir dari IP server `100.100.3.2`.
- `NS` menentukan server DNS yang melayani zona Reverse.

Simpan file dengan `Ctrl + O`, tekan `Enter`, lalu keluar dengan `Ctrl + X`.

## 5. Mengatur Opsi BIND9

Buka file konfigurasi opsi BIND9:

```bash
sudo nano /etc/bind/named.conf.options
```

Sesuaikan bagian `options` agar memuat konfigurasi berikut. Jika sudah terdapat blok `options`, edit blok yang tersedia dan jangan membuat blok kedua.

```conf
options {
    directory "/var/cache/bind";

    listen-on { 127.0.0.1; 100.100.3.2; };
    listen-on-v6 { any; };

    allow-query {
        localhost;
        10.10.3.0/28;
        100.100.3.0/30;
        192.168.3.0/24;
    };

    recursion yes;

    forwarders {
        1.1.1.1;
        8.8.8.8;
    };

    dnssec-validation auto;
};
```

Atau bisa juga dengan ini:

```conf
    options {
    directory "/var/cache/bind";

    listen-on {
        127.0.0.1;
        100.100.32.2;
    };

    listen-on-v6 { none; };

    allow-query {
        localhost;
        10.10.32.0/28;
        192.168.32.0/24;
    };

    recursion yes;

    allow-recursion {
        localhost;
        10.10.32.0/28;
        192.168.32.0/24;
    };

    allow-query-cache {
        localhost;
        10.10.32.0/28;
        192.168.32.0/24;
    };

    forwarders {
        8.8.8.8;
        1.1.1.1;
    };

    dnssec-validation auto;
};
```

**Keterangan:**
- `listen-on` menentukan alamat IPv4 yang digunakan BIND9 untuk menerima permintaan DNS.
- `allow-query` mengizinkan perangkat dari jaringan yang dicantumkan untuk mengirim permintaan DNS.
- `recursion yes` mengaktifkan pencarian DNS lanjutan.
- `forwarders` meneruskan permintaan DNS yang tidak dapat dijawab secara lokal ke DNS publik.
- `dnssec-validation auto` mengaktifkan validasi DNSSEC secara otomatis.

Simpan file setelah selesai mengedit.

## 6. Memeriksa Konfigurasi BIND9

Periksa sintaks konfigurasi BIND9 menggunakan perintah:

```bash
sudo named-checkconf
```

Jika tidak muncul pesan kesalahan, lanjutkan dengan memeriksa file zona Forward:

```bash
sudo named-checkzone ukom12tkj-3.sch.id /etc/bind/db.ukom12tkj-3
```

Kemudian periksa file zona Reverse:

```bash
sudo named-checkzone 3.100.100.in-addr.arpa /etc/bind/db.100.100.3
```

Pastikan kedua pemeriksaan menampilkan status `OK`. Pemeriksaan ini membantu memastikan konfigurasi zona sudah benar sebelum layanan dijalankan.

## 7. Mengaktifkan dan Memeriksa Layanan BIND9

Jalankan perintah berikut untuk mengaktifkan layanan BIND9 dan menjalankannya secara otomatis saat server dinyalakan:

```bash
sudo systemctl enable ---now named
sudo systemctl restart bind9
sudo systemctl status bind9
```

Perintah `enable --now` mengaktifkan layanan sekaligus menjalankannya. Perintah `restart` memulai ulang layanan agar konfigurasi terbaru diterapkan. Sementara itu, `status` digunakan untuk memeriksa kondisi layanan.

Pastikan status layanan menunjukkan `active (running)`.

## 10. Menguji DNS dari Ubuntu Server

Uji domain utama dan subdomain `www` menggunakan DNS lokal:

```bash
dig @127.0.0.1 ukom12tkj-3.sch.id
dig @127.0.0.1 www.ukom12tkj-3.sch.id
```

```bash
dig @100.100.3.2 ukom12tkj-3.sch.id
dig @100.100.3.2 www.ukom12tkj-3.sch.id
```

Kemudian uji Reverse DNS:

```bash
dig @127.0.0.1 -x 100.100.3.2
```

Periksa bagian `ANSWER SECTION`. Hasil Forward DNS harus mengarah ke `100.100.3.2`, sedangkan hasil Reverse DNS harus menampilkan domain `ukom12tkj-3.sch.id`.

## 11. Menguji DNS dari Komputer Client

Buka CMD sebagai Administrator, kemudian bersihkan cache DNS:

```cmd
ipconfig /flushdns
```

Setelah itu, uji domain menggunakan DNS MikroTik yang sesuai. Contoh jika client terhubung melalui ether2:

```cmd
nslookup ukom12tkj-3.sch.id 10.10.3.1
nslookup www.ukom12tkj-3.sch.id 10.10.3.1
```

Untuk menguji Reverse DNS:

```cmd
nslookup 100.100.3.2 10.10.3.1
```

Pastikan hasil pengujian menunjukkan alamat IP dan nama domain yang sesuai dengan konfigurasi server.

## 12. Pengujian Akses Website Menggunakan Domain

Setelah DNS berhasil diuji, buka browser pada komputer client dan akses:

```text
http://ukom12tkj-3.sch.id
```

Kemudian uji juga alamat berikut:

```text
http://www.ukom12tkj-3.sch.id
```

Jika website berhasil ditampilkan, berarti konfigurasi DNS sudah terhubung dengan layanan web Apache.

## 13. Kesimpulan

Konfigurasi BIND9 digunakan untuk menghubungkan domain `ukom12tkj-3.sch.id` dengan alamat IP Ubuntu Server `100.100.3.2`. Konfigurasi meliputi pembuatan Forward Zone, Reverse Zone, pengaturan opsi BIND9, serta pengaktifan layanan DNS.

Selanjutnya, DNS Static ditambahkan pada MikroTik agar domain website mengarah ke IP Ubuntu Server. Pengujian Forward DNS dan Reverse DNS menggunakan `dig` serta `nslookup` dilakukan untuk memastikan penerjemahan domain dan alamat IP berjalan sesuai konfigurasi.
