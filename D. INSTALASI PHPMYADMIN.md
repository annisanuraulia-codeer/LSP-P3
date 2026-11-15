# D. INSTALASI PHPMYADMIN

phpMyAdmin merupakan aplikasi berbasis web yang digunakan untuk mengelola database MariaDB melalui browser. Dengan phpMyAdmin, pengguna dapat melihat database, membuat tabel, menambahkan data, dan mengatur akun database tanpa harus selalu menggunakan terminal.

## 1. Memperbarui Daftar Paket dan Menginstal phpMyAdmin

Jalankan perintah berikut pada Ubuntu Server:

```bash
sudo apt update
sudo apt install phpmyadmin php-mysql -y
```

Perintah `apt update` digunakan untuk memperbarui daftar paket yang tersedia. Selanjutnya, `apt install` digunakan untuk menginstal phpMyAdmin beserta modul PHP MySQL agar dapat terhubung dengan MariaDB.

Saat proses instalasi berlangsung, jika muncul pilihan web server, pilih `apache2` dengan menekan tombol Space, kemudian tekan Enter. Jika muncul pilihan konfigurasi database menggunakan dbconfig-common, ikuti petunjuk instalasi yang tersedia.

## 2. Memeriksa Konfigurasi Apache phpMyAdmin

Periksa apakah file konfigurasi phpMyAdmin sudah tersedia:

```bash
ls -l /etc/apache2/conf-available/ | grep -i phpmyadmin
```

Perintah ini digunakan untuk mencari file konfigurasi phpMyAdmin yang disediakan untuk Apache.

Jika konfigurasi tersedia, aktifkan dengan perintah berikut:

```bash
sudo a2enconf phpmyadmin
```

Kemudian periksa sintaks konfigurasi Apache:

```bash
sudo apache2ctl configtest
```

Jika muncul `Syntax OK`, lanjutkan dengan memuat ulang Apache:

```bash
sudo systemctl restart apache2
```

Perintah `a2enconf` mengaktifkan konfigurasi phpMyAdmin, sedangkan `systemctl restart apache2` menjalankan ulang Apache agar perubahan konfigurasi diterapkan.

## 3. Pengujian melalui Browser

Buka browser pada laptop atau client yang terhubung dan dapat menjangkau Ubuntu Server. Kemudian akses alamat berikut:

```text
http://100.100.3.2/phpmyadmin
```

Alamat `100.100.3.2` merupakan IP Address Ubuntu Server yang terhubung melalui interface ether3 MikroTik.

Jika halaman login phpMyAdmin muncul, berarti layanan sudah dapat diakses melalui IP server.

## 4. Pemeriksaan Jika phpMyAdmin Tidak Bisa Diakses

Jika halaman phpMyAdmin belum terbuka, periksa apakah paket sudah terpasang:

```bash
dpkg -l | grep phpmyadmin
```

Perintah tersebut digunakan untuk melihat status instalasi paket phpMyAdmin.

Selanjutnya, periksa lokasi file konfigurasi Apache:

```bash
dpkg -L phpmyadmin | grep -E 'apache.conf|phpmyadmin.conf'
```

Jika hasilnya menunjukkan file `/etc/phpmyadmin/apache.conf` dan konfigurasi Apache belum tersedia, buat tautan konfigurasi dengan perintah:

```bash
sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/conf-available/phpmyadmin.conf
```

Tautan ini menghubungkan file konfigurasi phpMyAdmin dengan direktori konfigurasi Apache.

Setelah itu, aktifkan konfigurasi dan periksa kembali:

```bash
sudo a2enconf phpmyadmin
sudo apache2ctl configtest
```

Jika hasil pemeriksaan menunjukkan `Syntax OK`, mulai ulang Apache:

```bash
sudo systemctl restart apache2
```

Jika perintah `ln -s` menampilkan pesan bahwa file sudah tersedia, jangan membuat tautan baru. Periksa konfigurasi yang sudah ada terlebih dahulu.

## 5. Pengujian Ulang

Buka kembali browser dan akses:

```text
http://100.100.3.2/phpmyadmin
```

Jika halaman login muncul, masukkan akun MariaDB yang sudah dibuat sebelumnya, yaitu `ukomtkj-1` atau `ukomtkj-2`, beserta password yang telah ditentukan.

Setelah berhasil login, pilih database `ukom3` untuk melihat dan mengelola tabel yang sudah dibuat.

Jika konfigurasi DNS sudah selesai, phpMyAdmin juga dapat diakses menggunakan domain:

```text
http://ukom12tkj-3.sch.id/phpmyadmin
```

**Catatan:** Pengujian awal menggunakan IP `100.100.3.2` agar akses phpMyAdmin tidak bergantung pada konfigurasi DNS. Setelah DNS selesai dikonfigurasi, pengujian dapat dilakukan kembali menggunakan nama domain.

## 6. Kesimpulan

Pada tahap ini, saya berhasil melakukan instalasi dan konfigurasi phpMyAdmin pada Ubuntu Server. Aplikasi ini digunakan untuk mengelola database MariaDB melalui browser.

Pengujian dilakukan menggunakan IP Address Ubuntu Server, yaitu `100.100.3.2`, dan nama domain `ukom12tkj-3.sch.id`. Jika halaman login berhasil ditampilkan dan akun MariaDB dapat digunakan, berarti phpMyAdmin sudah dapat diakses.
