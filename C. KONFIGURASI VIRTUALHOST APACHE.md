# C. KONFIGURASI VIRTUALHOST APACHE

VirtualHost digunakan untuk mengatur agar Apache dapat melayani website berdasarkan nama domain. Konfigurasi ini juga menentukan direktori website yang akan ditampilkan ketika client mengakses domain tersebut.

## 1. Membuat File Konfigurasi VirtualHost

Jalankan perintah berikut untuk membuat file konfigurasi VirtualHost:

```bash
sudo nano /etc/apache2/sites-available/ukom12tkj-3.sch.id.conf
```

Masukkan konfigurasi berikut:

```apache
<VirtualHost *:80>
    ServerName ukom12tkj-3.sch.id
    ServerAlias www.ukom12tkj-3.sch.id

    DocumentRoot /var/www/html
    DirectoryIndex index.html index.php

    <Directory /var/www/html>
        Options FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ukom-error.log
    CustomLog ${APACHE_LOG_DIR}/ukom-access.log combined
</VirtualHost>
```

**Keterangan:**

- `ServerName` menentukan domain utama website.
- `ServerAlias` menambahkan nama domain alternatif dengan awalan `www`.
- `DocumentRoot` menentukan lokasi file website, yaitu `/var/www/html`.
- `DirectoryIndex` menentukan file yang pertama kali dibuka oleh Apache.
- `Require all granted` memberikan izin kepada client untuk mengakses direktori website.
- `ErrorLog` dan `CustomLog` menentukan lokasi penyimpanan catatan error dan aktivitas akses website.

Simpan konfigurasi dengan menekan `Ctrl + O`, lalu `Enter`. Setelah itu, keluar menggunakan `Ctrl + X`.

## 2. Mengaktifkan VirtualHost

Jalankan perintah berikut untuk mengaktifkan konfigurasi VirtualHost yang sudah dibuat:

```bash
sudo a2ensite ukom12tkj-3.sch.id.conf
```

Perintah `a2ensite` digunakan untuk mengaktifkan konfigurasi website agar dapat digunakan oleh Apache.

## 3. Memeriksa Konfigurasi Apache

Periksa konfigurasi Apache untuk memastikan tidak ada kesalahan penulisan:

```bash
sudo apache2ctl configtest
```

Jika konfigurasi benar, akan muncul hasil:

```text
Syntax OK
```

Hasil tersebut menunjukkan bahwa konfigurasi Apache sudah sesuai dan tidak memiliki kesalahan sintaks.

## 4. Memuat Ulang Apache

Setelah pemeriksaan berhasil, jalankan perintah berikut:

```bash
sudo systemctl reload apache2
```

Perintah ini digunakan untuk memuat ulang Apache agar konfigurasi VirtualHost yang baru dapat diterapkan tanpa menghentikan layanan Apache sepenuhnya.

## 5. Pengujian VirtualHost

Setelah konfigurasi DNS selesai, buka browser pada client dan akses alamat berikut:

```text
http://ukom12tkj-3.sch.id
```

Untuk memastikan website dapat diakses melalui alamat IP server, gunakan alamat berikut:

```text
http://100.100.3.2
```

IP `100.100.3.2` merupakan alamat Ubuntu Server yang terhubung melalui interface ether3 pada MikroTik.

Jika website berhasil ditampilkan melalui domain maupun alamat IP, berarti layanan Apache dan konfigurasi VirtualHost sudah berjalan dengan baik.
